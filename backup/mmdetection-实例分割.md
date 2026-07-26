1.mmdetection环境安装

```c
conda create --name openmmlab python=3.8 -y
conda activate openmmlab
（ PyTorch 1.8+.）
conda install pytorch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2 pytorch-cuda=11.8 -c pytorch -c nvidia
pip install -U openmim
mim install mmengine
mim install "mmcv>=2.0.0"
git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection
pip install -v -e .
```
验证是否成功：

```c
mim download mmdet --config rtmdet_tiny_8xb32-300e_coco --dest .
python demo/image_demo.py demo/demo.jpg rtmdet_tiny_8xb32-300e_coco.py --weights rtmdet_tiny_8xb32-300e_coco_20220902_112414-78e30dcc.pth --device cpu
```
您将在 ./outputs/vis 文件夹中看到一个新的图像 demo.jpg ，其中绘制了汽车、工作台等的边界框。
报错见[这个](https://yalungelin.github.io/post/Ubuntu%2022.0-an-zhuang-MMSegmentation.html)
2.数据的构建（coco）
	数据来源为自采，标注平台为[https://ai.baidu.com/easydata/](https://ai.baidu.com/easydata/)，这个标注平台对于新手友好，推荐新手使用。
标注类型为：实例分割，选用的是coco格式（实例分割使用coco格式比较友好，很多模型都是由他构建的，只需要更改类别信息即可）
3.数据的准备
	对于官方给的加载**coco数据集的结构**，
	

```c
images/
    train2017/
    val2017/
stuffthingmaps/
    train2017/
    val2017/
annotations/
    instances_train2017.json
    instances_val2017.json
stuffthingmaps_semseg/ 
    train2017/
    val2017/
 stuffthingmaps_semseg其中这个是通过stuffthingmaps来转换，只是为了要像素值对齐
```
如果通过easydata此标注平台进行操作，数据结构为这样

```c
images/
    xxx.jpg
    xxx.jpg
annotations/
    coco.json
```
下面代码可以转换为**coco数据集的结构**：

```python
import os
import json
import random
import shutil
import numpy as np
from PIL import Image, ImageDraw
from tqdm import tqdm

# ==== 配置路径 ====
json_path = r"Annotations\coco_info.json"       # 你的 COCO 标注文件
images_dir = r"\Images"              # 所有原始图片路径
output_dir = r"\dataset"               # 输出的根目录
train_ratio = 0.7                      # 训练集比例

# ==== 创建输出目录 ====
images_train_dir = os.path.join(output_dir, "images/train2017")
images_val_dir = os.path.join(output_dir, "images/val2017")
stuff_train_dir = os.path.join(output_dir, "stuffthingmaps/train2017")
stuff_val_dir = os.path.join(output_dir, "stuffthingmaps/val2017")
ann_dir = os.path.join(output_dir, "annotations")

for d in [images_train_dir, images_val_dir, stuff_train_dir, stuff_val_dir, ann_dir]:
    os.makedirs(d, exist_ok=True)

# ==== 读取 JSON ====
with open(json_path, "r", encoding="utf-8") as f:
    coco_data = json.load(f)

images_info = coco_data["images"]
annotations = coco_data["annotations"]
categories = coco_data["categories"]

# ==== 按图片分组标注 ====
image_to_anns = {}
for ann in annotations:
    image_to_anns.setdefault(ann["image_id"], []).append(ann)

# ==== 随机划分 ====
random.seed(42)
image_ids = [img["id"] for img in images_info]
random.shuffle(image_ids)

split_idx = int(len(image_ids) * train_ratio)
train_ids = set(image_ids[:split_idx])
val_ids = set(image_ids[split_idx:])

train_images, val_images = [], []
train_anns, val_anns = [], []

# ==== 生成 stuffthingmaps 并划分 ====
for img_info in tqdm(images_info, desc="Processing images"):
    file_name = img_info["file_name"]
    img_id = img_info["id"]
    width, height = img_info["width"], img_info["height"]

    # 创建单通道mask（全背景0）
    mask = np.zeros((height, width), dtype=np.uint8)
    anns = image_to_anns.get(img_id, [])

    for ann in anns:
        if ann["iscrowd"] == 1:
            continue
        if ann["segmentation"]:
            img_mask = Image.new("L", (width, height), 0)
            draw = ImageDraw.Draw(img_mask)
            seg = ann["segmentation"]
            if isinstance(seg, list):  # polygon
                for poly in seg:
                    if len(poly) >= 6:
                        xy = [(poly[i], poly[i+1]) for i in range(0, len(poly), 2)]
                        draw.polygon(xy, fill=1)  # 单类：前景设为1
            elif isinstance(seg, dict) and "counts" in seg:  # RLE
                import pycocotools.mask as maskUtils
                m = maskUtils.decode(seg)
                mask[m > 0] = 1
            mask = np.maximum(mask, np.array(img_mask))  # 取最大值，保持前景1

    # 保存mask
    if img_id in train_ids:
        mask_path = os.path.join(stuff_train_dir, os.path.splitext(file_name)[0] + ".png")
        img_out_path = os.path.join(images_train_dir, file_name)
        train_images.append(img_info)
        train_anns.extend(anns)
    else:
        mask_path = os.path.join(stuff_val_dir, os.path.splitext(file_name)[0] + ".png")
        img_out_path = os.path.join(images_val_dir, file_name)
        val_images.append(img_info)
        val_anns.extend(anns)

    Image.fromarray(mask).save(mask_path)
    shutil.copy(os.path.join(images_dir, file_name), img_out_path)

# ==== 保存新的JSON ====
train_json = {
    "images": train_images,
    "annotations": train_anns,
    "categories": categories
}
val_json = {
    "images": val_images,
    "annotations": val_anns,
    "categories": categories
}

with open(os.path.join(ann_dir, "instances_train2017.json"), "w", encoding="utf-8") as f:
    json.dump(train_json, f, ensure_ascii=False)
with open(os.path.join(ann_dir, "instances_val2017.json"), "w", encoding="utf-8") as f:
    json.dump(val_json, f, ensure_ascii=False)

print("数据集处理完成！")

```
如果是其他平台标注也没有问题，只需要导出为coco格式都可。
4.模型训练
处理完数据以后，需要把代码中的数据对齐自定义的数据。
其中有个比较重要的问题，就是在这个模型中，背景默认不需要写入到类别中。（尤其是单类别的时候）
我用的是实例分割，需要更改下面几个文件
coco.py: mmdet/datasets/coco.py

```python
METAINFO = {
        'classes':
        ( 'rice'),
        # palette is a list of color tuples, which is used for visualization.
        'palette':
        [ (255, 0, 0)]
    }
```
其中类别换成自己的。
还有对于类别数量的更改，一般都在需要跑的模型yaml文件中。

```bash
num_classes：1
```
改成自己的类别数量
然后就可以训练模型了，我先尝试的是yolact，命令如下：

```bash
python tools/train.py configs/yolact/yolact_r50_1xb8-55e_coco.py
```
训练的过程中需要保存最优权重，可有几种选择。首先第一可以选自定义的指标保存最优的权重，也可以自动（auto）。
如果更关注检测性能（检测任务），使用：
```python
save_best='coco/bbox_mAP'
```
如果更关注实例分割性能（Mask），使用：
```python
save_best='coco/segm_mAP'
```
自动的情况：
auto 会选择 Evaluator 返回的第一个指标，而 CocoMetric(metric=['bbox', 'segm']) 默认先计算并返回 bbox 指标，再返回 segm 指标。
因此：
```python
save_best='auto'
```
实际等价于：
```python
save_best='coco/bbox_mAP'
```

使用训练权重推理

```bash
from mmdet.apis import DetInferencer

inferencer = DetInferencer(model='/work_dirs/yolact_r50_1xb8-55e_coco/yolact_r50_1xb8-55e_coco.py', weights='work_dirs/yolact_r50_1xb8-55e_coco/epoch_100.pth')

inferencer('5_frame_00010.jpg', out_dir='/outputs/', no_save_pred=False)
```
**结果展示：**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7fb9b6633bbf4aa89e73584128dd4f04.png)

很多模型一起进行可视化预测的代码，包含对于各个类别的计算和统计。 

```c
import cv2
import os
from mmdet.apis import DetInferencer
from collections import Counter

# 定义所有模型配置
MODEL_CONFIGS = [
    {
        'name': 'yolact',
        'config': '/home/lsl/mmdetection/work_dirs/yolact_r50_1xb8-55e_coco/yolact_r50_1xb8-55e_coco.py',
        'checkpoint': '/home/lsl/mmdetection/work_dirs/yolact_r50_1xb8-55e_coco/epoch_100.pth'
    },
    {
        'name': 'mask-rcnn_convnext',
        'config': '/home/lsl/mmdetection/work_dirs/mask-rcnn_convnext-t-p4-w7_fpn_amp-ms-crop-3x_coco/mask-rcnn_convnext-t-p4-w7_fpn_amp-ms-crop-3x_coco.py',
        'checkpoint': '/home/lsl/mmdetection/work_dirs/mask-rcnn_convnext-t-p4-w7_fpn_amp-ms-crop-3x_coco/epoch_100.pth'
    },
    {
        'name': 'mask-rcnn_r50',
        'config': '/home/lsl/mmdetection/work_dirs/mask-rcnn_r50_fpn_1x_coco/mask-rcnn_r50_fpn_1x_coco.py',
        'checkpoint': '/home/lsl/mmdetection/work_dirs/mask-rcnn_r50_fpn_1x_coco/epoch_100.pth'
    },
    {
        'name': 'queryinst_r50',
        'config': '/home/lsl/mmdetection/work_dirs/queryinst_r50_fpn_1x_coco/queryinst_r50_fpn_1x_coco.py',
        'checkpoint': '/home/lsl/mmdetection/work_dirs/queryinst_r50_fpn_1x_coco/epoch_100.pth'
    },
    {
        'name': 'sparseinst_r50',
        'config': '/home/lsl/mmdetection/work_dirs/sparseinst_r50_iam_8xb8-ms-270k_coco/sparseinst_r50_iam_8xb8-ms-270k_coco.py',
        'checkpoint': '/home/lsl/mmdetection/work_dirs/sparseinst_r50_iam_8xb8-ms-270k_coco/iter_25500.pth'
    }
]

# 输入图片文件夹
img_dir = '/home/lsl/mmdetection/data/test'
base_out_dir = '/home/lsl/mmdetection/data/outputs/'
threshold = 0.5  # 置信度阈值

# 获取文件夹下所有图片路径
img_paths = [os.path.join(img_dir, f) for f in os.listdir(img_dir)
             if f.lower().endswith(('.jpg', '.png', '.jpeg'))]

print(f"共找到 {len(img_paths)} 张图片用于推理。")

# 遍历模型
for cfg in MODEL_CONFIGS:
    print(f"\n=== 推理模型: {cfg['name']} ===")
    
    # 初始化推理器
    inferencer = DetInferencer(
        model=cfg['config'],
        weights=cfg['checkpoint']
    )
    
    # 输出目录
    model_out_dir = os.path.join(base_out_dir, cfg['name'])
    os.makedirs(model_out_dir, exist_ok=True)
    
    # 遍历图片推理
    for img_path in img_paths:
        img_name = os.path.splitext(os.path.basename(img_path))[0]
        
        result = inferencer(img_path, no_save_pred=True, return_vis=True)
        
        if 'visualization' not in result or len(result['visualization']) == 0:
            print(f"[警告] 模型 {cfg['name']} 图片 {img_name} 没有生成可视化结果")
            continue
        
        preds = result['predictions'][0]
        scores = preds['scores']
        labels = preds['labels']
        
        keep_idx = [i for i, s in enumerate(scores) if s >= threshold]
        count_total = len(keep_idx)
        category_counts = Counter(labels[i] for i in keep_idx)
        
        # 转换为 BGR
        vis_img_rgb = result['visualization'][0]
        vis_img = cv2.cvtColor(vis_img_rgb, cv2.COLOR_RGB2BGR)
        
        # 保存预测图
        first_img_path = os.path.join(model_out_dir, f'{img_name}_pred.jpg')
        cv2.imwrite(first_img_path, vis_img)
        
        # 添加数量文字
        vis_with_count = vis_img.copy()
        text = f"Total: {count_total}"
        font = cv2.FONT_HERSHEY_SIMPLEX
        font_scale = 3.0
        thickness = 5
        (text_w, text_h), baseline = cv2.getTextSize(text, font, font_scale, thickness)
        pos_x = vis_with_count.shape[1] - text_w - 20
        pos_y = 20 + text_h
        cv2.putText(vis_with_count, text, (pos_x, pos_y),
                    font, font_scale, (0, 0, 0), thickness, cv2.LINE_AA)
        
        second_img_path = os.path.join(model_out_dir, f'{img_name}_pred_with_count.jpg')
        cv2.imwrite(second_img_path, vis_with_count)
        
        print(f"保存: {second_img_path}")

print("\n推理完成！")
```


**50系显卡得编译才能使用**


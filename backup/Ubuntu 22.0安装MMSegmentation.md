步骤 1. 创建一个 conda 环境，并激活

conda create --name openmmlab python=3.8 -y
conda activate openmmlab
步骤 2. 安装 PyTorch

安装低版本的pytorch，在继续安装MMSegmentation，才能避免报错。因为显卡是4090，CUDA Toolkit必须安装11.8以上的。（只有本机系统的CUDA是合适的，在环境中的是向下兼容的，个人的理解）[CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit-archive)
conda安装：`conda install pytorch==1.13.0 torchvision==0.14.0 torchaudio==0.13.0 pytorch-cuda=11.7 -c pytorch -c nvidia`
pip安装：`pip install torch==1.13.0+cu117 torchvision==0.14.0+cu117 torchaudio==0.13.0 --extra-index-url https://download.pytorch.org/whl/cu117`

以下的步骤就是为安装MMSegmentation做准备。
最佳实践
步骤 0. 使用 MIM 安装 MMCV

	pip install -U openmim
	mim install mmengine
	mim install "mmcv>=2.0.0"
步骤 1. 安装 MMSegmentation

情况 a: 如果您想立刻开发和运行 mmsegmentation，您可通过源码安装：
	
	git clone -b main https://github.com/open-mmlab/mmsegmentation.git
	cd mmsegmentation
	pip install -v -e .
	'-v' 表示详细模式，更多的输出
	 '-e' 表示以可编辑模式安装工程，
	 因此对代码所做的任何修改都生效，无需重新安装
 
情况 b: 如果您把 mmsegmentation 作为依赖库或者第三方库，可以通过 pip 安装：
	
	pip install "mmsegmentation>=1.0.0"

然后运行测试代码
步骤1.我们需要下载配置和检查点文件。

	mim download mmsegmentation --config pspnet_r50-d8_4xb2-40k_cityscapes-512x1024 --dest .
下载过程将需要几秒钟或更长时间，具体取决于您的网络环境。下载完成后，您将在当前文件夹中找到两个文件pspnet_r50-d8_4xb2-40k_cityscapes-512x1024.py和。pspnet_r50-d8_512x1024_40k_cityscapes_20200605_003338-2966598c.pth

步骤2.验证推理演示。

选项（a）。如果从源安装 mmsegmentation，只需运行以下命令。

	python demo/image_demo.py demo/demo.png configs/pspnet/pspnet_r50-d8_4xb2-40k_cityscapes-512x1024.py pspnet_r50-d8_512x1024_40k_cityscapes_20200605_003338-2966598c.pth --device cuda:0 --out-file result.jpg
您将在当前文件夹中看到一个新图像result.jpg，其中所有对象都覆盖有分割蒙版。

下面可能会进行的报错，以及我解决的方式。
报错一：
Traceback (most recent call last):
  File "demo/image_demo.py", line 6, in <module>
    from mmseg.apis import inference_model, init_model, show_result_pyplot
  File "/home/ubuntu/mmsegmentation/mmseg/__init__.py", line 61, in <module>
    assert (mmcv_min_version <= mmcv_version < mmcv_max_version), \
AssertionError: MMCV==2.2.0 is used but incompatible. Please install mmcv>=2.0.0rc4.、
报错二：
ModuleNotFoundError: No module named 'mmcv._ext'

有两种解决方式：
	

```
pip install mmcv-lite==2.0.0rc4
```

如果还是报错可以使用下面的这个命令

```
mim install mmcv==2.1.0
```
在运行测试代码，即可。


在进行预测时默认带有类别标签，去除如下：

```c
show_result_pyplot(model, img_path, result, show=False, out_file=output_path, opacity=1,with_labels=False)
```
**with_labels=False,   # 不显示标签**

resume模型一直卡住，也不报错

卡在这里 Advance dataloader 102456 steps to skip data that has already been trained
解决方法：降低mmengine 版本  

mim install mmengine==0.10.2   

如果在子系统上出现训练中断，多次出现在权重保存方面，可能是yapf 版本与 Python 3.8 或 mmengine 不兼容
```
pip install -U yapf           # 升级到最新版
or
pip install yapf==0.32.0      # 安装官方推荐版本
````
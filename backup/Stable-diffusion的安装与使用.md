**首先是对环境的配置：**
设备为Linux，显卡：4090
conda环境：python=3.10，pytorch=2.6

```bash
pip install torch==2.6.0 torchvision==0.21.0 torchaudio==2.6.0 --index-url https://download.pytorch.org/whl/cu124
```
```bash
pip install diffusers["torch"] transformers
```
Diffusers 的安装
```bash
git clone https://github.com/huggingface/diffusers.git
cd diffusers
uv pip install -e ".[torch]"
```
使用以下命令将克隆的存储库更新到最新版本的 Diffusers。

```bash
cd ~/diffusers/
git pull
```
模型权重和文件会从 Hub 下载到缓存中，缓存通常是您的主目录。您可以使用HF_HOME或HF_HUB_CACHE环境变量更改缓存位置，或者在诸如from_pretrained()cache_dir之类的方法中配置参数。

```bash
export HF_HOME="/path/to/your/cache"
export HF_HUB_CACHE="/path/to/your/hub/cache"
```
缓存文件允许您离线使用扩散器。设置HF_HUB_OFFLINE环境变量可1阻止扩散器连接到互联网。

```bash
使用HF_HUB_DISABLE_TELEMETRY环境变量选择退出并禁用遥测数据收集。
```
使用HF_HUB_DISABLE_TELEMETRY环境变量选择退出并禁用遥测数据收集。

```bash
export HF_HUB_DISABLE_TELEMETRY=1
```
我使用的是Image-to-image（图像到图像的生成）
使用的模型为：Stable Diffusion XL (SDXL)
SDXL 是稳定扩散模型的增强版本。它使用更大的基础模型，并增加了一个细化模型来提升基础模型的输出质量。

```python
import torch
from diffusers import AutoPipelineForImage2Image
from diffusers.utils import make_image_grid, load_image

pipeline = AutoPipelineForImage2Image.from_pretrained(
    "stabilityai/stable-diffusion-xl-refiner-1.0", torch_dtype=torch.float16, variant="fp16", use_safetensors=True
)
pipeline.enable_model_cpu_offload()
# remove following line if xFormers is not installed or you have PyTorch 2.0 or higher installed
pipeline.enable_xformers_memory_efficient_attention()

# prepare image
url = "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/diffusers/img2img-sdxl-init.png"
init_image = load_image(url)

prompt = "Astronaut in a jungle, cold color palette, muted colors, detailed, 8k"

# pass prompt and image to pipeline
image = pipeline(prompt, image=init_image, strength=0.5).images[0]
make_image_grid([init_image, image], rows=1, cols=2)
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1096784afd8b475d85416f1b8df2b571.png)
**这里注意：**访问不了huggingface的，或者下载速度较慢的可以使用国内的镜像网站：`https://hf-mirror.com/`
1. 安装依赖

```bash
pip install -U huggingface_hub
```
2. 设置环境变量

```bash
export HF_ENDPOINT=https://hf-mirror.com
```
下载模型的命令：

```bash
hf download  stabilityai/stable-diffusion-xl-base-1.0  --local-dir /models/stable-diffusion-xl-base-1.0
```
下载数据集：

```bash
hf  download --repo-type dataset --resume-download wikitext --local-dir wikitext
```
stable-diffusion-xl-base-1.0 适合正常的图片增强。能够更改很多东西，stable-diffusion-xl-refiner-1.0是对原图的细化操作。
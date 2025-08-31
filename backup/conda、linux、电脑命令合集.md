**conda命令**
```c
conda list：查看环境中的所有包
conda install XXX：安装 XXX 包
conda remove XXX：删除XXX 包
conda env list：列出所有环境
conda create -n XXX python=3.XXX：创建名为 XXX 的环境 conda
create -n env_name jupyter notebook ：创建虚拟环境
activate XXX（或 source activate XXX）：启用/激活XXX环境
conda env remove -n XXX：删除指定XXX环境
deactivate（或source deactivate）：退出环境
conda env export > xxx.yaml:导出环境
conda env create -f xxx.yaml:导入环境
jupyter notebook ：打开Jupyter Notebook
conda config --remove-key channels ：换回默认源
conda update --all：更新环境中的所有软件包
conda update conda：Conda中更新自己
conda update package_name1 package_name2 ...：更新环境中的某些软件包
conda install package_name=old_version：回退软件包版本
conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia
pip install -r requirements.txt  //安装依赖
conda list -e > requirements.txt  //导出环境依赖
conda clean --all  这个命令会删除 Conda 包缓存、索引缓存、临时文件、未使用的包等，帮助释放空间。
pip list --format=freeze > requirements.txt 这样导出的requirements.txt，不会含有文件路径。
```
**conda 导出环境**
1. 一般方式
```c
conda env export --no-builds > environment.yml
```

-  --no-builds：不导出具体的构建号，只保留包名+版本号，移植性更好（不同平台安装成功率高）。

- 生成的是 YAML 格式，包含 conda 包和 pip 包。

- 用于恢复环境：`conda env create -f environment.yml

2. 精确复现方式

```c
conda list --explicit > spec-file.txt
```

- 会导出精确的包版本和构建号（甚至频道），能 100% 还原 环境。

- 缺点：不同平台（Linux/Windows）可能会安装失败，因为构建依赖平台。

- 用于恢复环境：`conda create --name myenv --file spec-file.txt`

**只想导出 pip 依赖**
进入环境后：`pip list --format=freeze > requirements.txt`
**建议：**

- 如果是给别人用、或者跨平台部署 → 用 conda env export --no-builds

- 如果是自己备份、同平台恢复 → 用 conda list --explicit

**Linux命令**
nvidia-smi：查看GPU信息
nvcc -V :查看 CUDA 编译器版本

在脚本中运行如下代码，查看是否anconda在安装pytorch环境的时候也安装了cuda和cudnn。
```c

import torch
torch.__version__
torch.cuda.is_available()
torch.backends.cudnn.is_available()
torch.version.cuda
torch.backends.cudnn.version()
```
递归（-R）地把某个目录（/directory_path）及其里面的所有文件和子目录的所有者改成你指定的用户（username）

```c
sudo chown -R username /directory_path
username为你的用户名，directory_path为你想要修改的文件夹权限的路径
```

```c
sudo：以管理员权限执行（因为修改文件所有者通常需要 root 权限）

chown：change owner（更改文件/目录的所有者）

-R：递归操作，包含所有子目录和文件

username：要设为所有者的用户账号名（可以用 whoami 查看自己当前的用户名）

/directory_path：要修改权限的文件夹路径
```

chown 只是改所有者，不改读写执行权限（那是 chmod 做的事）


word公式标号快捷键

```c
公式#()回车
```

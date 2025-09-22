WSL安装
首先在启用或关闭wwindows功能中启用Linux虚拟化平台等等
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/660cfae3a173454f82be442c58df479d.png)
在微软商店中安装不同版本的Ubuntu，如果使用深度学习训练，建议使用20.04，别的版本会有显卡软连接问题，后续会提到。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/94df50660c1e41198740e38f1abf3c35.png)
安装完成，设置完用户名和密码，即可使用。
显卡驱动都可安装，需要配置驱动信息，类似于Windows的环境变量。

还有就是wsl的 卸载问题，直接卸载，在本机还是有保留。完整卸载如下：
查看所拥有的子系统列表
在power shell中操作
```c
wsl --list
```
删除你要删除的版本
```c
wsl --unregister Ubuntu-24.04  
```
所遇到的问题，在模型训练中：
**Could not load library libcudnn_cnn_infer.so.8. Error: libcuda.so: cannot open shared object file**
有两种解决方法：
 1. 修复软连接问题

```c
cd /usr/lib/wsl/lib/

#backup
sudo mv libcuda.so.1 libcuda.so.1.bak
sudo mv libcuda.so libcuda.so.bak

#symbolic link
sudo ln -s libcuda.so.1.1 libcuda.so.1
sudo ln -s libcuda.so.1.1 libcuda.so
sudo ldconfig
```
这种方法可以暂时性解决，但是电脑死机后，就失效，得反复操作这个命令。（不知道是不是因为这个原因导致电脑死机？疑问）
这个是原博主连接：[修复软连接](https://blog.csdn.net/zfjBIT/article/details/129679186)

 2. 直接连接
 只需在 .bashrc 中添加以下内容：
 

```c
export LD_LIBRARY_PATH=/usr/lib/wsl/lib:$LD_LIBRARY_PATH
```
确保您的库位于 /usr/lib/wsl/lib 中，您可以运行

```c
ldconfig -p | grep cuda
```
目前来看是可以使用，等到电脑在突然死机，再进行实验。
贴上连接：[ .bashrc](https://discuss.pytorch.org/t/libcudnn-cnn-infer-so-8-library-can-not-found/164661/2)


**WSL使用Windows的代理**
1.加上这个可以正常代理并且使用vscode连接wsl2开发

在Windows中的C:\Users\<your_username>目录下创建一个.wslconfig文件，然后在文件中写入如下内容

```c
[experimental]
autoMemoryReclaim=gradual
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```
[链接](https://www.cnblogs.com/hg479/p/17869109.html)
2.现在不用手动开关了 修改一下.wslconfig文件就能自动开启代理了，以下是我的配置文件，修改前需要在powershell运行 wsl --update --pre-release

```
[wsl2]
nestedVirtualization=true
ipv6=true
[experimental]
autoMemoryReclaim=gradual # gradual | dropcache | disabled
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```
[链接](https://zhuanlan.zhihu.com/p/153124468)

这个是微软官方配置描述[WSL高级配置](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#networkingmode-mirrored)

**Rustdesk问题**
在使用模型进行训练时，使用Rustdesk远程会导致内存或进程抢占，导致在需要模型验证阶段，需要大量的线程和内存导致溢出。更换远程工具立即恢复。
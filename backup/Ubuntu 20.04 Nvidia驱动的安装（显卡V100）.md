


---

## **1. 检查 NVIDIA 内核模块是否加载**
运行：
```bash
lsmod | grep nvidia
```
如果没有输出，说明 **NVIDIA 内核模块未加载**，尝试手动加载：
```bash
sudo modprobe nvidia
nvidia-smi
```
如果 `modprobe` 报错，比如：
```
modprobe: ERROR: could not insert 'nvidia': No such device
```
说明 **驱动与内核不兼容**，请跳到 **步骤 3** 重新安装驱动。

---

## **2. 检查 `nouveau` 是否占用显卡**
运行：
```bash
lsmod | grep nouveau
```
如果有输出，说明 `nouveau` 仍在运行，导致 NVIDIA 驱动无法正常工作。解决方法：
```bash
echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
sudo update-initramfs -u
sudo reboot
```
重启后，再次运行：
```bash
lsmod | grep nouveau
```
如果 `nouveau` 不再出现，再次尝试：
```bash
sudo modprobe nvidia
nvidia-smi
```

---

## **3. 重新安装 NVIDIA 驱动**
如果上面的方法仍然不行，尝试 **完全卸载并重新安装 NVIDIA 驱动**：

### **(1) 卸载旧驱动**
```bash
sudo apt-get purge '^nvidia-.*'
sudo apt-get autoremove
sudo apt-get update
```

### **(2) 重新安装最新驱动**
```bash
sudo apt update                                      # 更新源列表
ubuntu-drivers devices                               # 查看可安装的驱动列表（见下图选择recommended那项
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5bf6863a247f40e1b9b002efac8d1e69.png)

```bash
sudo apt-get install nvidia-driver-570（根据当前推荐的版本）
```
这一块如果安装驱动很慢，在软件和更新部分更换源
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/72216c26a7164f3c9239f4f1e9732b8a.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/00193562e4b34568a94335812cd76340.png)
这里换了阿里云的服务器速度比较快。
然后重启：
```bash
sudo reboot
```
重启后，运行：
```bash
nvidia-smi
```
如果仍然失败，可能是 **Linux 内核缺少 NVIDIA 头文件**，继续下一步。

---

## **4. 确保内核支持 NVIDIA 驱动**
运行：
```bash
uname -r
```
然后安装相应的内核头文件：
```bash
sudo apt-get install linux-headers-$(uname -r)
```
然后重新安装驱动：
```bash
sudo apt-get install --reinstall nvidia-driver-535
sudo reboot
```
重启后，再次尝试 `nvidia-smi`。

---

## **5. 检查 NVIDIA 服务是否正常**
```bash
systemctl status nvidia-persistenced
```
如果未启动，运行：
```bash
sudo systemctl restart nvidia-persistenced
sudo systemctl enable nvidia-persistenced
```
然后再试 `nvidia-smi`。

---

### **如果仍然失败，请执行以下命令并粘贴输出**
```bash
lsmod | grep nvidia
dmesg | grep -i nvidia
journalctl -xe | grep -i nvidia
```


如果直接使用`sudo apt-get install nvidia-driver-570`安装，或对`nouveau`部分操作时导致黑屏可能是这种方式操作导致驱动不兼容，系统无法启动，最后还是使用本地推荐安装的方法即可。如下图：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/582121d0a5bf47f8b7533079e11a299e.png)

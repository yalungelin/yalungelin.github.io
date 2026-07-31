1. 配置目的

通过 SSH Key 实现本地计算机免密码登录远程 Linux 服务器，解决 VS Code Remote SSH、Codex CLI、自动化脚本等工具因密码认证失败导致的连接问题。

配置完成后：

VS Code Remote SSH 可以稳定连接服务器
Codex CLI 可以正常调用远程环境
Git、脚本、自动化任务无需重复输入密码
不影响服务器已有环境和运行任务

一、本地 Windows 端生成 SSH Key
1. 打开 PowerShell

执行：

`ssh-keygen -t ed25519`

如果提示：

Enter file in which to save the key

直接回车，默认保存：

C:\Users\用户名\.ssh\id_ed25519

生成两个文件：

```
id_ed25519        # 私钥，不可泄露
id_ed25519.pub    # 公钥，需要上传服务器
```
2. 查看公钥内容

执行：

type ~/.ssh/id_ed25519.pub

输出类似：

`ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAxxxx user@computer`

复制整行内容。

注意：

不要复制换行
不要修改字符
不要上传私钥 id_ed25519

二、上传公钥到服务器
1. 使用密码登录服务器

例如：

`ssh username@ip`

输入密码进入服务器。

2. 创建 SSH 配置目录

执行：

```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```
3. 添加公钥

编辑：

`nano ~/.ssh/authorized_keys`

在文件最后新增一行：

`ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAxxxx user@computer`

保存：
```
Ctrl + O
Enter
Ctrl + X
```
4. 设置权限

执行：

`chmod 600 ~/.ssh/authorized_keys`

检查：

`ls -la ~/.ssh`

正常：

```
drwx------ .ssh
-rw------- authorized_keys
```

三、本地测试 SSH Key 登录

退出服务器：

`exit`

重新连接：

`ssh username@ip`

如果无需输入密码直接进入：

说明 SSH Key 配置成功。

补充相关命令说明：
nano的编辑快捷键：
**说明：^ 表示 Ctrl 键。**
<html>
<body>
<!--StartFragment-->
快捷键 | 功能
-- | --
Ctrl + O | 保存文件（Write Out）
Enter | 确认保存文件名
Ctrl + X | 退出 nano
Ctrl + W | 搜索文本（Where Is）
Ctrl + K | 剪切当前行
Ctrl + U | 粘贴剪切内容
Ctrl + C | 显示当前光标位置
Ctrl + _ | 跳转到指定行号
Ctrl + A | 移动到行首
Ctrl + E | 移动到行尾
Ctrl + Y | 上翻一页
Ctrl + V | 下翻一页
Alt + A | 开始选择文本
Alt + 6 | 复制当前行
Ctrl + T | 拼写检查（部分系统支持）

<!--EndFragment-->
</body>
</html>

**chmod权限解释：**
```
chmod 700 文件或目录
chmod 600 文件
```
表示修改文件/目录权限。

权限由三组数字组成：
```
chmod XYZ
      │││
      ││└── 其他用户（others）
      │└─── 同组用户（group）
      └──── 文件所有者（owner）
```
每一位数字代表：
<html>
<body>
<!--StartFragment-->
数字 | 权限 | 含义
-- | -- | --
0 | --- | 无权限
1 | --x | 执行权限
2 | -w- | 写权限
3 | -wx | 写 + 执行
4 | r-- | 读权限
5 | r-x | 读 + 执行
6 | rw- | 读 + 写
7 | rwx | 读 + 写 + 执行

<!--EndFragment-->
</body>
</html>
其中：

r = read（读取）
w = write（写入）
x = execute（执行/进入目录）

例如：**chmod 700 ~/.ssh**
```
700

owner   group   others
rwx     ---     ---
```
也就是：
<html>
<body>
<!--StartFragment-->
用户 | 权限
-- | --
文件所有者（你） | 读、写、进入
同组用户 | 无权限
其他用户 | 无权限

<!--EndFragment-->
</body>
</html>

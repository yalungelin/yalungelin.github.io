一、hyper-v的开启
我的设备为Win 11 专业版（建议专业版）
家庭版升级专业版，[见此链接](https://github.com/massgravel/Microsoft-Activation-Scripts)
启用或关闭Windows功能
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/21d04179b51c4002a9b65a20f38d9e61.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/86e6f0ba6027427eb3b5e11001b64968.png)
二、Easy-GPU-PV下载
下载文件包，[链接](https://github.com/jamesstringerparsec/Easy-GPU-PV)
三、使用
确保您的系统满足先决条件。
下载仓库并解压。
在系统中搜索 PowerShell ISE 并以管理员身份运行。
在您下载的提取文件夹中，打开 PowerShell ISE 中的 PreChecks.ps1。在提取文件夹内运行文件。不要移动它们。
打开并运行 PreChecks.ps1，使用 Powershell ISE 中的绿色播放按钮，并复制列出的 GPU（或需要修复的警告）。
打开 CopyFilesToVM.ps1 PowerShell ISE 并编辑文件顶部的 params 部分，你需要小心分配多少 RAM、存储和硬盘空间，因为你的系统需要有这些可用。在 Windows 10 上，GPUName 必须保持为"AUTO"，在 Windows 11 上可以是"AUTO"或你想要分区的 GPU 的具体名称，与 PreChecks.ps1 中显示的完全一致。此外，你还需要提供你下载的 Windows 10/11 ISO 文件的路径。
运行 CopyFilesToVM.ps1 并在 params 部分应用您的更改 - 这可能需要 5-10 分钟。
在虚拟机上打开并登录 Parsec。您可以使用 Parsec 连接到虚拟机，最高可达 4K60FPS。（todesk也能使用）
您应该可以开始了
（GitHub上都有写）
视频教程[国外](https://www.youtube.com/watch?v=gA66IDBuAoE)、[国内](https://www.bilibili.com/video/BV1BM4y1H7ag/?vd_source=a38132be4d3ccc1e74a2b23f0b772d2a)
对照即可。

11.28 补档：测试深度学习影响较大，单个GPU仍然存在性能瓶颈。多个GPU倒是可以试试。
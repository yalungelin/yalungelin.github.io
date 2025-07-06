# 三种方式解决CUDA安装
# 1.CUDA, pip/conda pytorch
从系统的层面去安装CUDA工具包，然后用pip或者conda 安装pytorch
缺点:得更新CUDA版本，Nvidia的GPU没有版本兼容性
     得找相应的CUDA版本的包
# 2.Nvidia driver, conda pytorch
在本机上安装显卡驱动即可（一般本机都是有的，只需更新到最新），然后再通过conda来安装pytorch和CUDA
![](https://i-blog.csdnimg.cn/blog_migrate/27021ded86b3139b3c45f5de8234b9d6.png)
使用这个命令他会安装pytorch和对应的一个CUDA的工具包，系统只需要驱动即可
缺点：如果用CUDA的编辑器来做开发，其中是少了很多包的，需额外安装。
# 3.Nvidia driver, nvidia docker
先安装驱动，然后再去安装Nvidia的容器
然后可以根据这个链接进行相应的操作[（链接）](https://github.com/mli/transformers-benchmarks)

最后推荐第二种和第三种方法，我是用的是第二种，比较方便！
上述来自于李沐大神的视频，贴上引用链接。

> https://www.bilibili.com/video/BV1LT411F77M/?spm_id_from=333.999.0.0&vd_source=a38132be4d3ccc1e74a2b23f0b772d2a

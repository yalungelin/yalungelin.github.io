第一步 GitHub上官方安装
贴一下链接[mask2former](https://github.com/facebookresearch/Mask2Former/blob/9b0651c6c1d5b3af2e6da0589b719c514ec0d69a/INSTALL.md)
要是设备（显卡）不是很新，可以先照着官方步骤进行安装。
对CUDA环境设备安装有疑问，可参考我的另一篇博文[解决CUDA环境问题](https://blog.csdn.net/qq_43712324/article/details/135427738?spm=1001.2014.3001.5501)
接下来我就把我踩的坑一一讲述。
首先我的服务器的显卡为4090，下面是详细信息，Windows本机只有显卡驱动，没有CUDA工具包。训练使用的是wsl2（Windows子系统-Ubuntu 20.04）
![nvidia-smi](https://i-blog.csdnimg.cn/direct/1247e55086244037aa6c1f689fd23e8e.png)
我经历了很多过程，我首先是从官方的步骤进行安装，遇到了很多问题。我一一列举。

 1. CUDA_HOME not Found
 运行一下命令时

```
 cd mask2former/modeling/pixel_decoder/ops
 sh make.sh
```
        
    
  因本机没有安装CUDA Toolkit，没有配置环境变量，在编译时会提醒没有找到CUDA。
  我的解决方法是：在我当前的子系统上进行CUDA的安装。（就是这个CUDA的版本令我苦恼了很久）
   如果你所有的安装工作都完成了，跑训练代码出现以下问题：
  nvrtc: error: invalid value for --gpu-architecture (-arch)
  那可能就是CUDA的版本和显卡对不上了。
  因为我的服务器显卡比较新，所以在对应CUDA的版本是有兼容性问题的。下面我贴出网上相应说法图片和链接。
  ![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/aa5cbb80687f403bbca68a9d20968c89.png)
[链接](https://github.com/pytorch/pytorch/issues/87595)
  所以我做了很多工作都是无用功，不停的安装CUDA可就是有各种各样的问题。
 最后安装了CUDA　11.8的版本才能进行相应配置。
 我个人认为，首先的明确机器上的CUDA　toolkit和环境的CUDA对上，不然就容易出现这种问题，但这种情况只针对于当前的项目而言（可能是因为需要CUDA Toolkit编译的问题吧）。因为以前别的项目，不用这么麻烦，只需要在环境中和官方提供的CUDA对上就没有这个多问题。

 2. TypeError: __init__() got an unexpected keyword argument 'dtype'
这个问题出现的情况比较有趣，是因为torch版本和detectron2的不兼容性。
 我有好几种方法解决这个问题，这些解决方法来自于这些博客，我把链接贴上，截图方便查看。
 
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c75a974bc13343c69055eda9b921c391.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e312400be581452cb18971ac077774d6.png)

 [解决问题的博客1](https://blog.csdn.net/qq_41811902/article/details/134236417?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172147755016800180697559%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=172147755016800180697559&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-28-134236417-null-null.142%5Ev100%5Epc_search_result_base9&utm_term=mask2former%E8%AE%AD%E7%BB%83&spm=1018.2226.3001.4187)
 [解决问题的博客2](https://blog.csdn.net/weixin_63293091/article/details/135187598?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172147755016800180697559%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=172147755016800180697559&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-21-135187598-null-null.142%5Ev100%5Epc_search_result_base9&utm_term=mask2former%E8%AE%AD%E7%BB%83&spm=1018.2226.3001.4187)

还有别的问题可以在上述博客找到，大多数都是个别包兼容性问题，高了需要降版本，低了需要升版本。
我写的比较乱，算是对我过去一周的总结？我真的是太笨了，但是我非常感谢那些愿意分享解决问题的博主。
“开源模型是智商税”，我非常不同意这个说法，没有广大愿意分享交流的群众，怎能拨云见日？唾弃某某人！
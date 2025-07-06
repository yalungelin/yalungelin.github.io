1.Segmentation fault (core dumped)
测试完，是因为pytorch版本对不上导致的问题。
解决：更换环境，python=3.8，pytorch为1.9版本的，CUDA 11.1。换完如何还是报这个错误，建议多换几个pytorch版本。ps：我的换了pytorch1.7不行，1.9就好使。
2.ModuleNotFoundError: No module named 'numpy._core'
是由于当前数据集缓存的问题，把数据集生成的缓存(labels.cache)删除，在运行训练即可。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/78878b01b99949289e37c98aaa58c661.png)


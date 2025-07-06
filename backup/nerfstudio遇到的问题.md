![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/28e624756ea94406a14326c0fab8a6c0.png)
sudo apt install ffmpeg
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5dbf20bc94b0407aa6f7b3cd4fbbc8af.png)
sudo apt-get install colmap

nerfstudio训练自己的数据
[写的不错的博文](https://blog.csdn.net/Destiny_Di/article/details/136382803)

从只是使用的角度来说，分为三个部分：

1.数据处理
 

```bash
ns-process-data images --data /home/lsl/3Dseg/data/images/2 --output-dir /home/lsl/3Dseg/data/demorobo
```

 2.训练
 

```bash
 ns-train nerfacto --data /home/lsl/3Dseg/data/demorobo
```

  3.导出点云
  
这里导出的命令在可视化面板的可以直接生成，复制即可。
```bash
ns-export pointcloud --load-config outputs/demorobo/nerfacto/2025-03-30_162615/config.yml --output-dir exports/pcd/ --num-points 1000000 --remove-outliers True --normal-method open3d --save-world-frame False
```


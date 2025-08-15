一、Linux 常用命令
1. 系统信息

```bash
uname -a                # 查看系统内核和架构信息
cat /etc/os-release     # 查看系统版本
whoami                  # 查看当前用户
hostname                # 查看主机名
uptime                  # 查看系统运行时间和负载
```

2. 文件与目录管理

```bash
pwd                                 # 显示当前路径
ls -l                               # 列出文件详细信息
ls -a                               # 显示隐藏文件
cd <目录>                           # 切换目录
mkdir <目录>                        # 创建目录
rm -rf <文件/目录>                  # 删除文件或目录（危险）
cp file1 file2                      # 复制文件
mv old new                          # 重命名/移动文件
find /path -name "*.txt"            # 查找文件
tree                                # 树形结构查看目录（需安装）
```

3. 文件内容查看

```bash
cat file                # 查看文件内容
more file               # 分页查看文件
less file               # 上下翻页查看
head -n 10 file         # 查看前10行
tail -n 10 file         # 查看后10行
tail -f file            # 实时查看文件更新（日志常用）
```

4. 权限管理

```bash
ls -l                   # 查看文件权限
chmod 755 file          # 修改文件权限
chown user:group file   # 修改文件所属用户和组
sudo <命令>             # 以管理员权限执行
```

5. 网络

```bash
ifconfig / ip addr              # 查看网络信息
ping baidu.com                  # 测试网络连通性
curl http://example.com         # 访问网址
wget http://example.com/file    # 下载文件
scp file user@ip:/path          # 远程拷贝文件
ssh user@ip                     # 远程登录
ss -tulnp                       # 查看端口占用
```

6. 进程管理

```bash
ps aux                  # 查看进程
top                     # 实时查看进程
htop                    # 高级进程查看（需安装）
kill -9 PID             # 强制杀死进程
pkill <进程名>          # 按名称杀死进程
```

7. 压缩与解压

```bash
tar -czvf file.tar.gz dir/      # 打包压缩tar.gz
tar -xzvf file.tar.gz           # 解压tar.gz
zip -r file.zip dir/            # 压缩为zip
unzip file.zip                  # 解压zip
```
8. 按文件名查找

```bash
# 查找所有文件
find /path/to/search -type f

# 按文件名查找（精确匹配）
find /path/to/search -type f -name "filename.txt"

# 按文件名查找（模糊匹配）
find /path/to/search -type f -name "*keyword*"

# 查找并用 grep 过滤
find /path/to/search -type f | grep "keyword"
find /path/to/search -type f | grep -i "keyword"   # 忽略大小写
```
 

二、Docker 常用命令
11. 镜像管理

```bash
docker pull 镜像名[:tag]     # 拉取镜像
docker images                # 查看本地镜像
docker rmi 镜像ID            # 删除镜像
docker build -t 名字:tag .   # 构建镜像（当前目录Dockerfile）
```

2. 容器管理（启动/停止/重启）

```bash
docker ps                        # 查看运行中容器
docker ps -a                     # 查看所有容器
docker run -it --name 名字 镜像名 /bin/bash   # 创建并进入容器
docker start 容器ID/名称         # 启动容器
docker stop 容器ID/名称          # 停止容器
docker restart 容器ID/名称       # 重启容器
docker pause 容器ID/名称         # 暂停容器
docker unpause 容器ID/名称       # 恢复容器
docker rm 容器ID                 # 删除容器
docker exec -it 容器ID /bin/bash # 进入运行中容器
docker logs -f 容器ID            # 查看容器日志
```
docker run -it --name 名字 镜像名 /bin/bash   # 创建并进入容器
/bin/bash 是 Linux 系统中 Bash Shell 的可执行路径

作用：启动容器后直接进入 Bash 交互环境，而不是让容器跑默认的 ENTRYPOINT 或 CMD

这样可以让你手动输入命令，就像登录到一台新的 Linux 机器
如：
```bash
docker run -it ubuntu /bin/bash
```
执行后你会进入一个 Ubuntu 容器的命令行：

```bash
root@a1b2c3d4e5:/#
```


3. 数据卷

```bash
docker volume ls                                 # 查看卷
docker volume create 卷名                        # 创建卷
docker run -v 卷名:/容器路径 镜像名              # 挂载卷
docker run -v /宿主路径:/容器路径 镜像名         # 挂载宿主机目录
```

4. 网络

```bash
docker network ls                               # 查看网络
docker network create --driver bridge 网络名    # 创建网络
docker run --network 网络名 镜像名              # 使用指定网络
```

5. 清理命令

```bash
docker system prune -f     # 清理无用数据
docker image prune -f      # 清理无用镜像
docker container prune -f  # 清理已停止容器
docker volume prune -f     # 清理无用卷
```
6. 管理 Docker 服务
```bash
# 启动 Docker 服务
sudo systemctl start docker

# 停止 Docker 服务
sudo systemctl stop docker

# 重启 Docker 服务
sudo systemctl restart docker

# 查看 Docker 服务状态
sudo systemctl status docker

# 设置开机自启 Docker
sudo systemctl enable docker

# 取消开机自启 Docker
sudo systemctl disable docker
```

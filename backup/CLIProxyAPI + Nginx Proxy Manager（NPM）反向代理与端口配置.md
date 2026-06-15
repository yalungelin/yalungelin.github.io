一、目标架构
1. 原始问题结构

```bash
Internet
   ↓
服务器IP:8317（公网暴露）
   ↓
CLIProxyAPI
```
**问题：**
8317 等业务端口直接暴露公网
被恶意扫描
安全面过大
2. 改造后结构（标准生产）

```bash
Internet
   ↓ 443
Nginx Proxy Manager
   ↓ Docker internal network (proxy)
cli-proxy-api:8317
vaultwarden:80
```
3. 安全目标
❌ 不允许业务端口暴露公网
✔ 仅开放 80 / 443 / SSH
✔ 所有服务通过 NPM 统一入口
✔ Docker 内部通信
二、Docker Compose 标准配置
1. CLIProxyAPI（标准版）

```yaml
services:
  cli-proxy-api:
    image: eceasy/cli-proxy-api:latest
    container_name: cli-proxy-api

    environment:
      DEPLOY: ""

    expose:
      - "8317"
      - "8085"
      - "1455"
      - "54545"
      - "51121"
      - "11451"

    volumes:
      - ./config.yaml:/CLIProxyAPI/config.yaml
      - ./auths:/root/.cli-proxy-api
      - ./logs:/CLIProxyAPI/logs

    restart: unless-stopped

    networks:
      - proxy

networks:
  proxy:
    external: true
```
2. Nginx Proxy Manager（标准版）
可在面板更改
```yaml
services:
  npm:
    image: jc21/nginx-proxy-manager:latest
    container_name: npm

    ports:
      - "80:80"
      - "81:81"
      - "443:443"

    restart: unless-stopped

    networks:
      - proxy

networks:
  proxy:
    external: true
```
三、创建 Docker 网络（只需一次）

```bash
docker network create proxy
```
四、部署流程
1. 启动服务

```bash
docker compose up -d
```
2. 检查容器

```bash
docker ps
```
确认：
cli-proxy-api
npm
3. 检查网络

```bash
docker network inspect proxy
```
必须看到：

```bash
cli-proxy-api
npm
```
五、NPM 面板配置（核心）
访问：

```bash
http://IP:81
```
1. 添加 Proxy Host（CLIProxyAPI）
SOURCE

```bash
域名
```
DESTINATION

```bash
Scheme: http
Forward Hostname: cli-proxy-api
Forward Port: 8317
```
SSL

```bash
Request new certificate
Force SSL
HTTP/2
Block exploits
```
六、验证流程
1. 检查公网端口

```bash
ss -tlnp | grep 8317
```
必须为空（关键）
2. 内部网络测试

```bash
docker exec -it npm sh
curl http://cli-proxy-api:8317
```
返回正常即 OK
3. 域名访问测试

```bash
https://域名
```
七、网络机制说明
1. Docker DNS（关键点）
在同一 network 下：

```bash
cli-proxy-api → 自动解析为 172.22.x.x
```
因此 NPM 可以直接：

```bash
http://cli-proxy-api:8317
```
2. 为什么不能用 IP
错误方式：

```bash
http://127.0.0.1:8317
http://66.x.x.x:8317
```
原因：
127.0.0.1 指 NPM 容器自身
公网 IP 走 NAT，不在 Docker network
八、是否会“丢失网络”的判断
1. 永久方式（正确）
compose 中必须有：

```yaml
networks:
  proxy:
    external: true
```
并且 service 中：

```yaml
networks:
  - proxy
```
2. 临时方式（危险）

```bash
docker network connect proxy container
```
特点：
❌ 重建容器会丢失
❌ docker compose down/up 会失效
3. 判断方法
```bash
docker inspect cli-proxy-api
```
看：

```bash
Networks:
  proxy
```
九、安全收敛（建议）

1. 禁止公网端口

```bash
ufw deny 8317
ufw deny 8085
```
2. 只保留：
80
443
22
3. NPM 安全设置
Block Common Exploits ✔
Force SSL ✔
HTTP/2 ✔
十、常见问题排查

1. 502 Bad Gateway
原因：
Docker network 没通
cli-proxy-api 未运行
2. Could not resolve host
原因：
不在同一个 docker network
3. curl 失败
原因：
service 没监听 8317
expose / expose 配置错误
十一、最终稳定结构

```bash
Internet
   ↓ 443
Nginx Proxy Manager
   ↓ proxy network
cli-proxy-api:8317
vaultwarden:80
```
十二、结论
当前你的系统已经达到：
✔ 标准生产级 Docker + NPM 反向代理架构
✔ 无业务端口公网暴露
✔ Docker 内部 DNS 通信正常
✔ 域名统一入口管理
1.安装git
在Windows下直接安装git Windows即可
在Linux/WSL：`sudo apt install git`
2.设置用户名和邮箱（用于提交记录）

```bash
git config --global user.name "你的名字"
git config --global user.email "you@example.com"
```
查看配置：git config --list
3.初始化仓库
在你的项目根目录：

```bash
cd /path/to/your/project
git init
```
这会生成一个 .git 文件夹，Git 会在这里记录版本信息。
**（如果是新建项目，在创建GitHub仓库时，会有详细的流程，教你如何上传相应的代码）
（如果接着别人的项目开发又是不一样，下面详细展开说明）**
4.常用工作流
**查看当前状态**

```bash
git status
```
**添加到暂存区**

```bash
git add 文件名        # 添加指定文件
git add .            # 添加当前目录所有改动
```
**提交版本**

```bash
git commit -m "描述这次修改的内容"
```
**查看提交记录**

```bash
git log --oneline --graph --decorate
```
5.远程仓库（GitHub / Gitee / GitLab）
**关联远程仓库**

```bash
git remote add origin git@github.com:用户名/仓库名.git
```
**首次推送**

```bash
git push -u origin main   # 如果默认分支是 main
```
**拉取远程更新**

```bash
git pull
```
 生成 SSH key（推荐）
运行 ssh-keygen -t ed25519 -C "you@example.com"，然后将 ~/.ssh/id_ed25519.pub 的内容添加到 GitHub/Gitee 的 SSH key 设置中，实现免密操作。
**分支管理**

```bash
创建分支：git branch dev
切换分支：git checkout dev 或 git switch dev
合并分支：git merge dev
删除分支：git branch -d dev
```
**忽略文件**
在项目根目录创建 .gitignore 文件，例如：

```bash
*.pyc
__pycache__/
data/
```
这样这些文件不会被 Git 跟踪。
**实用技巧**

```bash
查看差异：git diff

撤销改动：

撤销未暂存：git checkout -- 文件名

撤销已暂存：git reset 文件名

回退版本：

保留修改：git reset --soft HEAD~1

丢弃修改：git reset --hard HEAD~1
```
**clone别人的仓库作为基准**
本地目录已经是一个完整的 Git 仓库（包含 .git 目录），不需要再 git init。
任务场景一：给原仓库贡献（Pull Request）
**克隆项目**

```bash
git clone git@github.com:原作者/项目名.git
cd 项目名
```
远程 origin 默认指向原作者的仓库。
**创建分支开发**
```bash
git checkout -b my-feature
```
**修改代码 → 提交**

```bash
git add .
git commit -m "feat: 添加某功能"
```
**推送到自己 Fork 的仓库**
你需要先在 GitHub 上 Fork 这个项目，然后修改远程：

```bash
git remote set-url origin git@github.com:你的用户名/项目名.git
git push -u origin my-feature
```
**在 GitHub 上发起 Pull Request，让原作者审核合并。**
任务场景二：当作模板，建自己的仓库
如果你只是想用别人的代码当起点，不打算提交回去，可以这样：
**克隆后解除和原仓库的绑定**

```bash
git clone https://github.com/原作者/项目名.git
cd 项目名
git remote remove origin
```
**在 GitHub/Gitee 创建你自己的空仓库，得到地址，例如：**

```bash
git@github.com:你的用户名/新仓库.git
```
**绑定到新的远程仓库**

```bash
git remote add origin git@github.com:你的用户名/新仓库.git
git push -u origin main   # 或 master
```
之后 git push 就是推送到你自己的仓库了。
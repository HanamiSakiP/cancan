施工中。。。
* [关注塔菲喵](https://koofr.eu/)
* https://app.koofr.net/dav/Koofr 官方 WebDAV URL
* https://app.koofr.net/dav/Dropbox 挂载 Dropbox WebDAV URL

rclone省流
```bash
rclone config
# n , 名字 , 服务
# 回车,回车,n,y,浏览器登陆,y
rclone listremotes
# 测试连通性
rclone lsd dropbox:
# 初始化,备份,恢复
```

初始化交互
```bash
rclone config
```
列出已配置
```bash
rclone listremotes
```
列出 remote 根目录
```bash
rclone ls remote:bucket/path
rclone lsd remote:bucket/path      # 只列目录
rclone lsf remote:bucket/path      # 简洁列表（可用参数定制）
```
从本地复制到 remote（保留源文件）
```bash
rclone copy /local/path remote:bucket/path
```
从 remote 复制到本地
```bash
rclone copy remote:bucket/path /local/path
```
同步（目标被源完全覆盖 — 会删除目标多余文件）
```bash
rclone sync /local/path remote:bucket/path
```
增量同步（仅修改或新增文件）通常由 copy 实现；sync 强制镜像
可加参数：-v（详细），--progress（显示进度），--dry-run（模拟执行），--checksum（使用校验和），--size-only（仅比较大小），--delete-excluded（删除排除项之外的文件）等。
```bash
rclone sync /local/photos remote:backup/photos --progress --dry-run
```
移动
```bash
rclone move /local/path remote:bucket/path
```
在 remote 内重命名（实际上是复制+删除）
```bash
rclone moveto remote:bucket/oldname remote:bucket/newname
```
删除 remote 中的文件或目录
```bash
rclone delete remote:bucket/path        # 删除文件
rclone purge remote:bucket/path         # 删除目录及其内容
rclone rmdirs remote:bucket/path        # 删除空目录
```
比较两端差异（列出不同文件）
```bash
rclone check /local/path remote:bucket/path
rclone md5sum remote:bucket/path > remote_md5.txt   # 生成校验和（视 backend 支持）
```
挂载（FUSE，Linux/macOS/Windows with WinFsp）
常用参数：--vfs-cache-mode full|writes|off，--allow-other（允许其他用户访问），--uid/--gid，--read-only。
```bash
rclone mount remote:bucket /mnt/remote --vfs-cache-mode writes
```
搜索文件名包含关键字
```bash
rclone find remote:bucket/path --name "关键字"
```

## git的初体验

## 0、省流


```bash
# 生成 SSH 密钥并使用
ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"

# 查看生成的密钥（复制到GitHub上）
cat ~/.ssh/id_rsa.pub

# 复制到密钥后
git remote add origin git@github.com:username/repository.git
git add .
git commit -m "Initial commit with all files"
git push -u origin master
```

后续使用
```bash
# 查看当前状态
git status
# 添加更改
# 添加所有更改
git add .
# 添加特定文件
# git add 文件名
# 提交更改
git commit -m "描述你的更改"
# 推送更改到远程仓库
git push origin master
# 查看提交历史
git log
```

## 1、新建仓库

登陆账号后点+号new
 * [新建仓库](https://github.com/new/)

Repository name处输入仓库名

 * Quick setup — if you’ve done this kind of thing before
    + 下方点击ssh后复制（2-2要用）


## 2、生成 SSH 密钥并使用
```bash
ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"
```
##### 会出现以下提示（确认密钥保存的默认路径）
```bash提示
# 回车就好
Enter file in which to save the key (/home/user/.ssh/id_rsa): 
```
回车后输入密码，再确认

```bash
查看生成的密钥（复制到GitHub上）
cat ~/.ssh/id_rsa.pub
```
复制内容登录到 GitHub，进入 "Settings" > "SSH and GPG keys"，点击 "New SSH key"，粘贴公钥并保存。



## 3、git初体验
### 步骤 1: 初始化 Git 仓库

如果您还没有在文件夹中初始化 Git 仓库，请在终端中导航到该文件夹并运行以下命令：

```bash
cd /path/to/your/folder
git init
```

这将创建一个新的 Git 仓库。

<hr>

### 步骤 2: 添加远程仓库

如果您还没有将远程仓库添加到本地仓库，请使用以下命令：

```bash
# 
git remote add origin git@github.com:username/repository.git
```

将 `username` 和 `repository` 替换为您的 GitHub 用户名和仓库名称。

<hr>

### 步骤 3: 添加所有文件和文件夹

要添加文件夹内的所有文件和子文件夹，可以使用以下命令：

```bash
git add .
```

这个命令会将当前目录下的所有文件和文件夹添加到 Git 的暂存区。

<hr>

### 步骤 4: 提交更改

在添加文件后，您需要提交更改：

```bash
git commit -m "Initial commit with all files"
```

您可以根据需要更改提交信息。

<hr>

### 步骤 5: 推送到 GitHub

最后，将更改推送到 GitHub：

```bash
git push -u origin master
```


## 4. 点击创建好的仓库

* [设置](https://github.com/用户名/仓库名/settings/pages)
    * 找到
     GitHub Pages 下的 Branch  下面 None  改为 master  /root

点Save保存

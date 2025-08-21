施工中。。。
* [关注塔菲喵](https://koofr.eu/)
* https://app.koofr.net/dav/Koofr 官方 WebDAV URL
* https://app.koofr.net/dav/Dropbox 挂载 Dropbox WebDAV URL

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
# MariaDB同理

#### 安装mysql、redis、tmux
```bash
apt update
apt install -y mariadb-server redis-server tmux
redis-server /etc/redis/redis.conf --daemonize no
redis-cli ping
service start mariadb
mysql -u root -p
```

#### git
```bash
# 生成 SSH 密钥并使用
# ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"
# 查看生成的密钥（复制到GitHub上）
# cat ~/.ssh/id_rsa.pub
git config -l


# 克隆项目到本地
git clone repo.git # repo.git为实际远程仓库地址
ls

# 为当前目录初始化git
# git init 
# 重命名远程仓库名
# git remote add origin git@github.com:username/repository.git

# 查看远程仓库名称 + 对应 URL
git remote -v
# 查看单个远程仓库的详细信息
git remote show origin  # 替换 origin 为你的远程仓库简称
```

#### tmux后台命令
```bash
cd frontend
tmux new -d -s qd "npm run dev"
cd ../backend/
tmux new -d -s hd "go run main.go"
cd ..
tmux ls
```

#### tmux清空命令
```bash
tmux ls
tmux kill-server 
# killall tmux
```

#### 配置mysql
```bash
sudo mysql_secure_installation
```

#### 配置过程中
```bash
回车
n
y
新密码
确认新密码
n
y
y
Reload privilege tables now? [Y/n] y
```

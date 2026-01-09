# MariaDB同理

#### 安装mysql、redis
```bash
apt update
apt install -y mariadb-server redis-server tmux
redis-server /etc/redis/redis.conf --daemonize no
redis-cli ping
service start mariadb
mysql -u root -p
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

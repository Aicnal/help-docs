参考文章：

> [抛砖引玉，来说下我的fail2ban配置｜LinuxDo] (https://linux.do/t/topic/48677)

官方提供了手动安装和软件包安装：[How to install fail2ban packages](https://github.com/fail2ban/fail2ban/wiki/How-to-install-fail2ban-packages)

```bash
apt install fail2ban -y
```

手动安装似乎有点复杂，可以参考：[【教程】配置Fail2Ban来阻止恶意登录｜NodeSeek](https://www.nodeseek.com/post-16144-1)

我只用来防止 sshd 爆破，故：

```bash
vim /etc/fail2ban/jail.local # 创建一个新的配置文件
```

参考配置文件如下：

```shell
[DEFAULT]
# 配置忽略检测的 IP (段)，添加多个需要用空格隔开
ignoreip = 127.0.0.1/8
[sshd]
enabled = true
# ssh的端口，如果使用非默认端口 22，要修改为实际使用端口
port = 22
# 配置使用的匹配规则文件（位于 /etc/fail2ban/filter.d 目录中）
filter = sshd
# 日志文件路径
logpath = /var/log/ban_sshd.log
# 配置 IP 封禁的持续时间（years/months/weeks/days/hours/minutes/seconds）
bantime  = 1h
# 是否开启增量禁止，可选
bantime.increment = true
# 如果上面为false则不生效，增量禁止的指数因子，这里设置为168的意思就是每次增加 168*(2^ban次数) (封禁时长类似这样 - 1小时 -> 7天 -> 14天 ...):
bantime.factor = 168
# 最大封禁时间，8w 表示8周，可选
bantime.maxtime = 8w
# 配置计算封禁 IP 的具体滑动窗口大小
findtime  = 5m
# 配置在 findtime 时间内发生多少次失败登录然后将 IP 封禁
maxretry = 3
# 配置封禁 IP 的手段（位于 /etc/fail2ban/action.d 目录中），可通过 iptables、firewalld 或者 TCP Wrapper 等，此处设置为 hostsdeny 代表使用 TCP Wrapper
action = hostsdeny
```

但是在实际配置的时候，可能出现如下报错：

```bash
root@VM-ZTbI5T27tsn3:~# systemctl status fail2ban
× fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; disabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Thu 2024-12-05 02:16:52 UTC; 6s ago
       Docs: man:fail2ban(1)
    Process: 1704075 ExecStart=/usr/bin/fail2ban-server -xf start (code=exited, status=255/EXCEPTION)
   Main PID: 1704075 (code=exited, status=255/EXCEPTION)
        CPU: 100ms

Dec 05 02:16:52 VM-ZTbI5T27tsn3 systemd[1]: Started Fail2Ban Service.
Dec 05 02:16:52 VM-ZTbI5T27tsn3 fail2ban-server[1704075]: 2024-12-05 02:16:52,730 fail2ban                [1704075]: ERROR   Failed during configuration: Have not found any log fi>
Dec 05 02:16:52 VM-ZTbI5T27tsn3 fail2ban-server[1704075]: 2024-12-05 02:16:52,735 fail2ban                [1704075]: ERROR   Async configuration of server failed
Dec 05 02:16:52 VM-ZTbI5T27tsn3 systemd[1]: fail2ban.service: Main process exited, code=exited, status=255/EXCEPTION
Dec 05 02:16:52 VM-ZTbI5T27tsn3 systemd[1]: fail2ban.service: Failed with result 'exit-code'.
```

这种情况是因为找不到日志`Have not found any log`：

```bash
touch /var/log/ban_sshd.log
```


重启服务：

```bash
systemctl restart fail2ban
systemctl status fail2ban
```
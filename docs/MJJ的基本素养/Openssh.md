# OpenSSH

> 参考资料：[erpnext.cc](https://erpnext.cc/computer-security/%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8/OpenSSH/OpenSSH%20%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95/)、[opencloudos](https://docs.opencloudos.org/OCS/Network_Guide/OpenSSH_guide/)

## 切换服务器的默认SSH端口

打开服务器的SSH默认配置文件：

```
vim /etc/ssh/sshd_config
```

修改行，为想要的端口：

```
#Port 22
```

跳转防火墙设置：

```
ufw allow 2222/tcp # 选择修改后的端口
```

重启SSH服务：

```
sudo systemctl restart sshd
```

注意事项：应该先去连接新的端口后再断开旧端口的连接，防止出现连不上的情况

## 修改为必须使用密钥进行登陆

关于密钥对加密算法的选择，在cnblog上的这篇文章写的很详细：[RSA、DSA、ECDSA、EdDSA 和 Ed25519 的区别](https://www.cnblogs.com/librarookie/p/15389876.html)

简单来说，考虑兼容性的话可以选择`RSA`，并且建议生成`3072`以上的key，如果不考虑兼容性，只考虑安全性的话，可以生成`Ed25519`的密钥对

生成密钥之后，可以使用`ssh-copy-id`（个人习惯使用直接的复制粘贴）

在服务器上执行以下命令，创建相关路径：

```
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

此时将生成的公钥粘贴进去

给予对应的权限：

```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

之后修改`/etc/ssh/sshd_config`中的内容，将使用密码登陆直接关闭

```
PasswordAuthentication no
```

重启服务：

```
sudo systemctl restart sshd
```
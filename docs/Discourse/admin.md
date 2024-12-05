# Creat admin

在一些特殊的情况下，比如说邮件系统无法使用，可以尝试使用 console 来强制创建 admin ，完成注册流程

参考：[Create an admin account from the console](https://meta.discourse.org/t/create-an-admin-account-from-the-console/17274)

```bash
cd /var/discourse
./launcher enter app
rake admin:create
```
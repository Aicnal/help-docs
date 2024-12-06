# 在 Ubuntu 上配置 SSH key 与签名

参考文章：

> [Git本地环境配置|墨封小记](https://segmentfault.com/a/1190000013154540)
>
> [服务器上的 Git - 生成 SSH 公钥|Git](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)
>
> [ssh-keygen 命令参数|cnblog](https://www.cnblogs.com/ring1992/p/16582952.html)


## 安装 Git 与 SSH

```zsh
apt install git -y
apt install ssh -y
```

## 配置本地 Git Config

```zsh
git config --global user.name "Your Name"
git config --global user.email Your_email@example.com
```

这里的 `email` 似乎会影响 commit 上的记录

## 生成密钥对

```zsh
ssh-keygen -t rsa -C "你的github账号邮箱"
```

## 上传公钥

```zsh
cat /root/.ssh/id_rsa.pub 
```

## SSH 测试

```zsh
ssh -T git@github.com
```

如果测试结果为：

```zsh
Hi Aicnal! You've successfully authenticated, but GitHub does not provide shell access.
```

即为成功

## ssh-keygen 命令参数

```zsh
ssh-keygen -t rsa -C [用户名] -b [长度] -f [文件名]
```

- -t：密钥类型（如：`ED25519`、`RSA`）
- -c：用户名
- -b：密钥对长度，默认为1024bit，推荐不应该短于`3072`，最长为`4096`
- -f：保存密钥的文件名

## 配置 GPG

难度极大，到目前为止还没有成功过。。。

## 配置 SSH 签名

这里可以参考：

> [关于提交签名验证|GitHub](https://docs.github.com/zh/authentication/managing-commit-signature-verification/about-commit-signature-verification#ssh-commit-signature-verification)
> [使用 SSH 签名 Git 提交记录|涛叔](https://taoshu.in/git/ssh-sign.html)

首先，ssh签名和提交的密钥是可以使用同一个密钥的，但是需要在 GitHub 中重新设置一下

```bash
git config gpg.format ssh
```

选择密钥的保存位置：

```bash
git config user.signingKey <位置>
```

设置默认使用密钥：

```bash
git config commit.gpgsign true
git config tag.gpgsign true
```
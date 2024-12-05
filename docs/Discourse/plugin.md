# Plugin

参考 Discourse 的官方文档：[Install plugins on a self-hosted site](https://meta.discourse.org/t/install-plugins-on-a-self-hosted-site/19157)

首先进入文件内容：

```bash
cd /var/discourse

vim containers/app.yml
```

更改 app.yml 的内容：

```app.yml
hooks:
  after_code:
    - exec:
        cd: $home/plugins
        cmd:
          - sudo -E -u discourse git clone https://github.com/discourse/docker_manager.git
          - sudo -E -u discourse git clone https://github.com/discourse/discourse-solved.git
```

重建容器：

```bash
cd /var/discourse
./launcher rebuild app
```
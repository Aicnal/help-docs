# Discourse 安装

## 基础安装

跟着官方的一键脚本来；

```bash
sudo -s
git clone https://github.com/discourse/discourse_docker.git /var/discourse
cd /var/discourse
chmod 700 containers
```

```bash
./discourse-setup
```

## 使用反代

参考官方的文档：[Run other websites on the same machine as Discourse](https://meta.discourse.org/t/run-other-websites-on-the-same-machine-as-discourse/17247)

官方提供的是使用 Nginx 作为反代服务器，这里改为 Caddy ：

```Caddyfile
discourse.example.com {
    reverse_proxy unix//var/discourse/shared/standalone/nginx.http.sock {
        header_up Host {host}
        header_up X-Real-IP {remote}
        header_up X-Forwarded-For {remote}
        header_up X-Forwarded-Proto {scheme}
}
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
    }
}
```

## app.yml 配置

```
## this is the all-in-one, standalone Discourse Docker container template
##
## After making changes to this file, you MUST rebuild
## /var/discourse/launcher rebuild app
##
## BE *VERY* CAREFUL WHEN EDITING!
## YAML FILES ARE SUPER SUPER SENSITIVE TO MISTAKES IN WHITESPACE OR ALIGNMENT!
## visit http://www.yamllint.com/ to validate this file as needed

templates:
  - "templates/postgres.template.yml"
  - "templates/redis.template.yml"
  - "templates/web.template.yml"
  ## Uncomment the next line to enable the IPv6 listener
  #- "templates/web.ipv6.template.yml"
  - "templates/web.ratelimited.template.yml"
  - "templates/web.socketed.template.yml"
  ## Uncomment these two lines if you wish to add Lets Encrypt (https)
  #- "templates/web.ssl.template.yml"
  #- "templates/web.letsencrypt.ssl.template.yml"

## which TCP/IP ports should this container expose?
## If you want Discourse to share a port with another webserver like Apache or nginx,
## see https://meta.discourse.org/t/17247 for details
# expose:
#  - "80:80"   # http
#  - "443:443" # https

params:
  db_default_text_search_config: "pg_catalog.english"

  ## Set db_shared_buffers to a max of 25% of the total memory.
  ## will be set automatically by bootstrap based on detected RAM, or you can override
  db_shared_buffers: "2048MB"

  ## can improve sorting performance, but adds memory usage per-connection
  #db_work_mem: "40MB"

  ## Which Git revision should this container use? (default: tests-passed)
  #version: tests-passed

env:
  LC_ALL: en_US.UTF-8
  LANG: en_US.UTF-8
  LANGUAGE: en_US.UTF-8
  # DISCOURSE_DEFAULT_LOCALE: en

  ## How many concurrent web requests are supported? Depends on memory and CPU cores.
  ## will be set automatically by bootstrap based on detected CPUs, or you can override
  UNICORN_WORKERS: 8

  ## TODO: The domain name this Discourse instance will respond to
  ## Required. Discourse will not work with a bare IP number.
  DISCOURSE_HOSTNAME: 'discourse.example.com'

  ## Uncomment if you want the container to be started with the same
  ## hostname (-h option) as specified above (default "$hostname-$config")
  #DOCKER_USE_HOSTNAME: true

  ## TODO: List of comma delimited emails that will be made admin and developer
  ## on initial signup example 'user1@example.com,user2@example.com'
  DISCOURSE_DEVELOPER_EMAILS: 'admin@example.com'

  ## TODO: The SMTP mail server used to validate new accounts and send notifications
  # SMTP ADDRESS, username, and password are required
  # WARNING the char '#' in SMTP password can cause problems!
  DISCOURSE_SMTP_ADDRESS: smtp.resend.com
  DISCOURSE_SMTP_PORT: 2587
  DISCOURSE_SMTP_USER_NAME: resend
  DISCOURSE_SMTP_PASSWORD: "<password>"
  DISCOURSE_SMTP_ENABLE_START_TLS: true           # (optional, default true)
  DISCOURSE_SMTP_DOMAIN: example.com   # (required by some providers)
  DISCOURSE_NOTIFICATION_EMAIL: noreply@example.com   # (address to send notifications from)

  ## If you added the Lets Encrypt template, uncomment below to get a free SSL certificate
  #LETSENCRYPT_ACCOUNT_EMAIL: me@example.com

  ## The http or https CDN address for this Discourse instance (configured to pull)
  ## see https://meta.discourse.org/t/14857 for details
  #DISCOURSE_CDN_URL: https://discourse-cdn.example.com

  ## The maxmind geolocation IP account ID and license key for IP address lookups
  ## see https://meta.discourse.org/t/-/173941 for details
  #DISCOURSE_MAXMIND_ACCOUNT_ID: 123456
  #DISCOURSE_MAXMIND_LICENSE_KEY: 1234567890123456

## The Docker container is stateless; all data is stored in /shared
volumes:
  - volume:
      host: /var/discourse/shared/standalone
      guest: /shared
  - volume:
      host: /var/discourse/shared/standalone/log/var-log
      guest: /var/log

## Plugins go here
## see https://meta.discourse.org/t/19157 for details
hooks:
  after_code:
    - exec:
        cd: $home/plugins
        cmd:
          - git clone https://github.com/discourse/docker_manager.git
          - git clone https://github.com/discourse/discourse-follow.git
          - git clone https://github.com/discourse/discourse-post-voting.git
          - git clone https://github.com/discourse/discourse-topic-voting.git
          - git clone https://github.com/discourse/discourse-solved.git
          - git clone https://github.com/discourse/discourse-calendar.git
          - git clone https://github.com/discourse/discourse-math.git
          - git clone https://github.com/discourse/discourse-reactions.git
          - git clone https://github.com/discourse/discourse-ai.git
          - git clone https://github.com/discourse/discourse-perspective-api.git
          - git clone https://github.com/discourse/discourse-user-notes.git
          - git clone https://github.com/discourse/discourse-assign.git
          - git clone https://github.com/fokx/discourse-anonymous-post.git
          - git clone https://github.com/ShuiyuanSJTU/retort.git
          - git clone https://github.com/discourse/discourse-signatures.git
          - git clone https://github.com/discourse/discourse-microsoft-auth.git
          - git clone https://github.com/discourse/discourse-steam-login.git
          - git clone https://github.com/discourse/discourse-whos-online.git
          - git clone https://github.com/discourse/discourse-data-explorer.git
          - git clone https://github.com/paviliondev/discourse-journal.git

## Any custom commands to run after building
run:
  - exec: echo "Beginning of custom commands"
  ## If you want to set the 'From' email address for your first registration, uncomment and change:
  ## After getting the first signup email, re-comment the line. It only needs to run once.
  #- exec: rails r "SiteSetting.notification_email='info@unconfigured.discourse.org'"
  - exec: echo "End of custom commands"
```

## With Cloudflare CDN

由于 Discourse 本身内置的 Nginx 有速率限制（针对 CC ），如果不进行设置，直接使用 Cloudflare 的 CDN 可能会出现误伤，好在官方已经给出了很好的解决办法：

可参考：[Using Discourse with Cloudflare: Best Practices](https://community.cloudflare.com/t/using-discourse-with-cloudflare-best-practices/602890)

需要修改 app.yml 中的内容：

```app.yml
templates:
  - "templates/postgres.template.yml"
  - "templates/redis.template.yml"
  - "templates/web.template.yml"
  - "templates/web.ratelimited.template.yml"
## Uncomment these two lines if you wish to add Lets Encrypt (https)
  - "templates/web.ssl.template.yml"
  - "templates/web.letsencrypt.ssl.template.yml"
  - "templates/cloudflare.template.yml" # --> 这里
```
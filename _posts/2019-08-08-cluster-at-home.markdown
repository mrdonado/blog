---
layout: post
title:  "Kubernetes cluster at home"
date:   2019-08-08
category: cloud
backgrounds:
    - https://blog.jdonado.com/assets/images/backgrounds/google-bg.png
thumb: https://blog.jdonado.com/assets/images/kubernetes-logo.png
tags: kubernetes docker docker-compose linux
---

# Commands

```
sudo vim /etc/ssh/sshd_config
systemctl restart sshd.service

vim ~/.ssh/authorized_keys
chmod 700 .ssh/
chmod 640 authorized_keys

sudo firewall-cmd --zone=public --add-port=19999/tcp --permanent
sudo firewall-cmd --reload


sudo vim /etc/systemd/system/sshtunnel.service
sudo systemctl start sshtunnel
sudo systemctl stop sshtunnel
sudo systemctl daemon-reload
sudo systemctl start sshtunnel

cp sshtunnel.service sshtunnel2.service
sudo cp sshtunnel.service sshtunnel2.service
ll
sudo vim sshtunnel2.service
systemctl enable sshtunnel
sudo systemctl enable sshtunnel2
cd -
sudo systemctl start sshtunnel2
sudo systemctl stop sshtunnel
sudo systemctl stop sshtunnel2

setsebool -P httpd_can_network_connect 1

sudo ./certbot-auto certonly --manual --preferred-challenges=dns --email jdonado@jdonado.com --server https://acme-v02.api.letsencrypt.org/directory --agree-tos -d *.jdonado.com

restorecon /etc/nginx/demo.*

43 6 * * * certbot renew --post-hook "systemctl reload nginx"

```

---
title: Portainer——UI for Docker
date: 2017-04-09
category: [BLOG]
tags: [Linux,Docker]
---

[官方网页](http://portainer.io/)

## 本地部署
`docker run -d -p 9000:9000 -v "/var/run/docker.sock:/var/run/docker.sock" portainer/portainer`

## 远程部署
### Client
`docker run -d -p 9000:9000 portainer/portainer -H tcp://SWARM_MANAGER_IP:2375`

> 最好bind `docker.sock` 到 `localhost:4243`再修改上面的命令来使用
> 在我的vultr 上，官方方法无法使用

### Server
`docker run -d -p 9000:9000 portainer/portainer`


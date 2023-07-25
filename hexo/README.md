# hexo 博客环境搭建使用

参考资料：
https://yeasy.gitbook.io/docker_practice/image/build
https://blog.laoda.de/archives/docker-compose-install-hexo-admin-and-twikoo/index.html

Hexo 🐋
============
this tutorial learn from https://github.com/spurin/docker-hexo/commits?author=spurin

[![Follow](https://shields.io/twitter/follow/jamesspurin?label=Follow)](https://twitter.com/jamesspurin)
[![Docker Pulls](https://img.shields.io/docker/pulls/spurin/hexo.svg)](https://hub.docker.com/r/spurin/hexo/)
[![Build Status](https://img.shields.io/docker/cloud/build/spurin/hexo.svg)](https://hub.docker.com/r/spurin/hexo/)

Dockerfile for [Hexo](https://hexo.io/) with [Hexo Admin](https://github.com/jaredly/hexo-admin)

The image is available directly from [Docker Hub](https://hub.docker.com/repository/docker/bendyx/blog/general)

A tutorial is available at [ideameshdyx.com]()


## Getting Started

Create a new blog container, substitute *domain.com* for your domain and specify your blog location with -v target:/app, specify your git user and email address (for deployment):

```
docker create --name=hexo-domain.com \
-e HEXO_SERVER_PORT=8000 \
-e GIT_USER="Your Name" \
-e GIT_EMAIL="your.email@domain.tld" \
-v /blog/domain.com:/app \
-p 8000:4000 \
spurin/hexo
```

```shell
docker create --name=ideamesh.com -e HEXO_SERVER_PORT=8000  -e GIT_USER="ideamesh" -e GIT_EMAIL="ideameshdyx@gmail.com" -v /Users/yixinda/file/blog/ideameshdyx.github.io:/app -p 8000:4000 bendyx/blog:v1.1
```

If a blog is not configured in /app (locally as /blog/domain.com) already, it will be created and Hexo-Admin will be installed into the blog as the container is started

and at the same time if we use 4000:4000,we must face the problem of port using ，such like the following :
```shell
FATAL Port 4000 has been used. Try other port instead.
FATAL {
  err: Error: listen EADDRINUSE: address already in use :::4000
      at Server.setupListenHandle [as _listen2] (net.js:1313:16)
      at listenInCluster (net.js:1361:12)
      at Server.listen (net.js:1447:7)
      at /app/node_modules/hexo-server/lib/server.js:69:12
      at Promise._execute (/app/node_modules/bluebird/js/release/debuggability.js:384:9)
      at Promise._resolveFromExecutor (/app/node_modules/bluebird/js/release/promise.js:518:18)
      at new Promise (/app/node_modules/bluebird/js/release/promise.js:103:10)
      at checkPort (/app/node_modules/hexo-server/lib/server.js:66:10)
      at Hexo.module.exports (/app/node_modules/hexo-server/lib/server.js:18:10)
      at Hexo.tryCatcher (/app/node_modules/bluebird/js/release/util.js:16:23)
      at Hexo.<anonymous> (/app/node_modules/bluebird/js/release/method.js:15:34)
      at /app/node_modules/hexo/lib/hexo/index.js:260:17
      at Promise._execute (/app/node_modules/bluebird/js/release/debuggability.js:384:9)
      at Promise._resolveFromExecutor (/app/node_modules/bluebird/js/release/promise.js:518:18)
      at new Promise (/app/node_modules/bluebird/js/release/promise.js:103:10)
      at Hexo.call (/app/node_modules/hexo/lib/hexo/index.js:256:12)
      at /usr/local/lib/node_modules/hexo-cli/dist/hexo.js:56:21
      at tryCatcher (/usr/local/lib/node_modules/hexo-cli/node_modules/bluebird/js/release/util.js:16:23)
      at Promise._settlePromiseFromHandler (/usr/local/lib/node_modules/hexo-cli/node_modules/bluebird/js/release/promise.js:547:31)
      at Promise._settlePromise (/usr/local/lib/node_modules/hexo-cli/node_modules/bluebird/js/release/promise.js:604:18)
      at Promise._settlePromise0 (/usr/local/lib/node_modules/hexo-cli/node_modules/bluebird/js/release/promise.js:649:10)
      at Promise._settlePromises (/usr/local/lib/node_modules/hexo-cli/node_modules/bluebird/js/release/promise.js:729:18) {
    code: 'EADDRINUSE',
    errno: -98,
    syscall: 'listen',
    address: '::',
    port: 4000
  }
} Something's wrong. Maybe you can find the solution here: %s https://hexo.io/docs/troubleshooting.html
```

what i find is that only if you change the setting of `-e HEXO_SERVER_PORT=8000` and `-p 8000:4000`,then you could find you site in `localhost:8000`


```
docker start hexo-domain.com
```

## Accessing the container

Should you wish to perform further configuration, i.e. installing custom themes, this should be viable from the app specific volume, either directly or via the container (changes to the app volume are persistent).  Accessing the container -

```
docker exec -it hexo-domain.com bash
```

## Deployment keys for use with Github/Gitlab

Deployment keys are configured as part of the initial app configuration, see the .ssh directory within your app volume or, view the logs upon startup for the SSH public key

```
docker logs --follow hexo-domain.com
```

### Installing a theme

Each theme will vary but for example, a theme such as [Hueman](https://github.com/ppoffice/hexo-theme-hueman), clone the repository to the themes directory within the app volume

```
cd /app
git clone https://github.com/ppoffice/hexo-theme-hueman.git themes/hueman
```

Update _config.yml in your app folder, and change theme accordingly

```
theme: hueman
```

Enable the default configuration

```
mv themes/hueman/_config.yml.example themes/hueman/_config.yml
```

Exit the container

```
exit
```

And restart the container

```
docker restart hexo-domain.com
```

## Accessing Hexo

Access the default hexo blog interface at http://< ip_address >:4000

## Accessing Hexo-Admin

Access Hexo-Admin at http://< ip_address >:4000/admin

## Generating Content

```
docker exec -it hexo-domain.com hexo generate
```

## Deploying Generated Content

```
docker exec -it hexo-domain.com hexo deploy
```

## Adding hexo plugins

If you wish to add specific hexo plugins, add them to a requirements.txt file to your app volume, for example (app/requirements.txt) -

```
hexo-generator-json-content
```

During startup, if the requirements.txt file exists, requirements are auto installed
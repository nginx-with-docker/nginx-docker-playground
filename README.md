# Nginx Docker Playground

<img src="assets/logo.png" with="130" height="130" />

Nginx Development Environment based on official docker image and official source code.

## Quick Start

```bash
docker pull soulteary/prebuilt-nginx-modules:base-1.23.0
```

Example, how to quickly build a nginx addon:

```docker
# @see https://github.com/nginx-with-docker/ngx_http_srcache_filter_module/blob/main/docker/0.32/Dockerfile.alpine

ARG NGINX_VERSION=1.19.7
FROM soulteary/prebuilt-nginx-modules:base-${NGINX_VERSION}-alpine AS Builder

ARG MODULE_CHECKSUM=127181f371046cc5ec0e0acf1b45cd478a8a7a5f
ARG MODULE_VERSION=0.32
ARG MODULE_NAME=srcache-nginx-module-src
ARG MODULE_SOURCE=https://github.com/nginx-with-docker/srcache-nginx-module-src

RUN cd /usr/src && \
    curl -L "${MODULE_SOURCE}/archive/refs/tags/v${MODULE_VERSION}.tar.gz" -o "${MODULE_VERSION}.tar.gz" && \
    echo "${MODULE_CHECKSUM}  ${MODULE_VERSION}.tar.gz" | shasum -c && \
    tar -zxC /usr/src -f ${MODULE_VERSION}.tar.gz && \
    cd /usr/src && \
    mv ${MODULE_NAME}-${MODULE_VERSION}/ ${MODULE_NAME} && \
    cd /usr/src/nginx && \
    CONFARGS=$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p') \
    CONFARGS=${CONFARGS/-Os -fomit-frame-pointer -g/-Os} && \
    echo $CONFARGS && \
    ./configure --with-compat $CONFARGS --add-dynamic-module=../${MODULE_NAME}/ && \
    make modules

FROM scratch

COPY --from=Builder /usr/src/nginx/objs/ngx_http_srcache_filter_module.so /
```

## Pre-built Nginx Addons

Pre-built nginx addons binaries based on this project
  - https://github.com/nginx-with-docker/prebuilt-nginx-modules

## Support Nginx Versions

Below are other available nginx versions.

- 1.23.0
- 1.22.0
- 1.21.6
- 1.21.5
- 1.21.4
- 1.21.3
- 1.21.1
- 1.21.0
- 1.20.0
- 1.19.10
- 1.19.9
- 1.19.8
- 1.19.7

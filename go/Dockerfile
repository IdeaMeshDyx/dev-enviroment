# 基础环境依旧是 ubuntu20
FROM ubuntu:20.04

LABEL version="v0" \
      maintainer="ideameshdyx <ideameshdyx@gmail.com>" \
      detail="go dev docker enviroment"

#env 参数设置
ARG GO_VERSION=1.20.5  

ENV TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive

RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo Asia/Shanghai >/etc/timezone

# 镜像内资源路径索引，本文档所给为公共使用，如果涉及公司内部使用环境，请单独进入镜像配置
COPY sources.list /etc/apt/sources.list

RUN apt-get update

#  安装系统依赖文件，在此添加需要自己需要的工具
## 环境依赖 cmake gdb build-essential 
## 系统环境依赖
## docker 远程访问 openssh-server wget git tar
## 文本编辑 vim 
## 
##
RUN  apt install -y --no-install-recommends tzdata build-essential cmake gdb \ 
     openssh-server rsync vim git tar wget ca-certificates lsb-release \
     net-tools iputils-ping

# 安装 go 环境
RUN cd /tmp &&\
    wget -O go.tar.gz https://golang.google.cn/dl/go${GO_VERSION}.linux-amd64.tar.gz && \
    tar --remove-files -C /usr/local/ -zxf go.tar.gz && \
    mkdir -p /go && \
    ln -sv /usr/local/go/bin/go /bin

# 设置环境参数
ENV GOROOT=/usr/local/go
ENV GOPATH=/go
ENV PATH=$GOROOT/bin:$PATH

# 镜像内构建 ssh 远程访问配置文件
RUN mkdir /var/run/sshd
RUN sed -ri 's/^#PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config &&  sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config && sed -ri 's/RSYNC_ENABLE=false/RSYNC_ENABLE=true/g' /etc/default/rsync

# 设置文件同步，开发流程为：将本地的文件映射到容器内运行
COPY rsync.conf /etc
RUN echo 'root:123456' |chpasswd
RUN mkdir /root/sync


# 初始脚本，可添加对环境初始化的操作
COPY entrypoint.sh /sbin
RUN chmod +x /sbin/entrypoint.sh
ENTRYPOINT [ "/sbin/entrypoint.sh" ]

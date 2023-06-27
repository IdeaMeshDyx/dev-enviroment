FROM ubuntu:20.04

LABEL version="v0" \
      maintainer="ideameshdyx <ideameshdyx@gmail.com>" \
      detail="c++ dev docker enviroment"

ARG BOOST_VERSION=1.73.0
ARG BOOST_VERSION_=1_73_0
ENV BOOST_VERSION=${BOOST_VERSION}
ENV BOOST_VERSION_=${BOOST_VERSION_}
ENV BOOST_ROOT=/usr/include/boost

ENV TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive

RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo Asia/Shanghai >/etc/timezone

# 镜像内资源路径索引，本文档所给为公共使用，如果涉及公司内部使用环境，请单独进入镜像配置
COPY sources.list /etc/apt/sources.list

RUN apt-get update

#  安装系统依赖文件，在此添加需要自己需要的工具
## c++环境依赖 cmake gdb build-essential 
## 系统环境依赖
## docker 远程访问 openssh-server wget git 
## 文本编辑 vim 
## 
##
RUN  apt install -y --no-install-recommends tzdata build-essential cmake gdb \ 
     openssh-server rsync vim git wget ca-certificates lsb-release \
     net-tools iputils-ping

# 安装依赖库，可以选择后期逐步安装，减少镜像的大小
RUN apt update \
    && apt install -y \
        libpqxx-dev \
        libmysqlclient-dev \
        libcurl4-openssl-dev \
        libmosquitto-dev \
        libmosquittopp-dev \
        libyaml-cpp-dev \
        libmxml-dev \
        libavutil-dev \
        libswresample-dev \
        libavcodec-dev \
        libavformat-dev \
        libswscale-dev


# 镜像内构建 ssh 远程访问配置文件
RUN mkdir /var/run/sshd
RUN sed -ri 's/^#PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config &&  sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config && sed -ri 's/RSYNC_ENABLE=false/RSYNC_ENABLE=true/g' /etc/default/rsync

# 设置文件同步，开发流程为：将本地的文件映射到容器内运行
COPY rsync.conf /etc
RUN echo 'root:000000' |chpasswd
RUN mkdir /root/sync

# install boost
RUN wget --no-check-certificate https://sourceforge.net/projects/boost/files/boost/${BOOST_VERSION}/boost_${BOOST_VERSION_}.tar.gz
RUN mkdir -p /usr/include/boost && tar zxf boost_${BOOST_VERSION_}.tar.gz -C /usr/include/boost --strip-components=1 && rm *.tar.gz

# 安装

# 初始脚本，可添加对环境初始化的操作
COPY entrypoint.sh /sbin
RUN chmod +x /sbin/entrypoint.sh
ENTRYPOINT [ "/sbin/entrypoint.sh" ]
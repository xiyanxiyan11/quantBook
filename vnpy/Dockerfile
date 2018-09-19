# Ubuntu 16.04 for running vn.py WebTrader

#FROM continuumio/anaconda
FROM hub.c.163.com/public/ubuntu:16.04-tools

MAINTAINER vn.py project <xiyanxiyan10@hotmail.com>

# Use bash
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# 时区设置
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 中文支持
RUN locale-gen zh_CN.UTF-8 \
    && DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales \
    && locale-gen zh_CN.UTF-8
ENV LANG zh_CN.UTF-8
ENV LANGUAGE zh_CN:zh


# 在本地构建镜像时，使用阿里云或163的 apt 源
RUN echo "开始配置 vnpy 环境" \
    && echo "更新 apt 源列表" \
    && echo "" > /etc/apt/sources.list \
    && echo "deb http://mirrors.163.com/ubuntu/ xenial main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.163.com/ubuntu/ xenial-backports main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.163.com/ubuntu/ xenial-proposed main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.163.com/ubuntu/ xenial-security main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.163.com/ubuntu/ xenial-updates main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.163.com/ubuntu/ xenial main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.163.com/ubuntu/ xenial-backports main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.163.com/ubuntu/ xenial-proposed main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.163.com/ubuntu/ xenial-security main multiverse restricted universe" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.163.com/ubuntu/ xenial-updates main multiverse restricted universe" >> /etc/apt/sources.list \
    && apt-get clean \
    && apt-get update \

# Install software
RUN apt-get update
RUN apt-get install wget gcc g++ cmake libboost-all-dev -y
RUN apt-get install git bzip2 vim nano -y 
RUN apt-get install libgl1-mesa-glx libglib2.0 libsm6 libxrender1 -y

# Download Anaconda
RUN wget https://repo.continuum.io/archive/Anaconda2-4.0.0-Linux-x86_64.sh 
RUN wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz

# Install Anaconda
RUN bash Anaconda2-4.0.0-Linux-x86_64.sh -b -p /opt/conda/
RUN source ~/.bashrc

ENV PATH /opt/conda/bin:$PATH

# update conda source
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

# set url show 
RUN conda config --set show_channel_urls yes

# pip source
RUN mkdir -p ~/.pip
COPY ./pip.conf ~/.pip/pip.conf

# update pip
RUN pip install --upgrade pip
RUN pip install future

#install talib
RUN tar xvf ta-lib-0.4.0-src.tar.gz
RUN rm ta-lib-0.4.0-src.tar.gz
RUN cd ta-lib && ./configure --prefix=/usr LDFLAGS="-lm" && make && make install
RUN echo "/usr/local/lib" >> /etc/ld.so.conf && ldconfig
RUN pip install ta-lib

RUN echo "安装 VNC 服务，使用固定密码 123456 登录" \
    && apt-get install -y x11vnc xvfb \
    && mkdir ~/.vnc \
    && x11vnc -storepasswd 123456 ~/.vnc/passwd \
    && apt-get clean

RUN bash -c 'echo "fluxbox &" >> ~/.bashrc'

RUN echo "安装 fluxbox 桌面管理器" \
    && apt-get install -y fluxbox

RUN echo "安装 mongodb 服务" \
    && mkdir -p /data/db \
    && apt-get install -y mongodb \
    && systemctl enable mongodb.service \
    && sed -i 's/bind_ip = 127.0.0.1/\#bind_ip = 127.0.0.1/g' /etc/mongodb.conf

RUN bash -c 'echo "service mongodb restart" >> ~/.bashrc'
# 在客户端登录时自动启动 GUI 程序 (might not be the best way to do it, but it does the trick)
#RUN bash -c 'echo "python /srv/vnpy/examples/VnTrader/run_simple.py" >> ~/.bashrc'
#RUN bash -c 'echo "/usr/bin/xterm" >> ~/.bashrc'
# 设置登录时开启本地 mongodb 服务并激活图形管理界面
#RUN bash -c 'echo "pip install -e /srv/vnpy" >> ~/.bashrc'
# 编译安装相关接口库
#RUN bash -c 'echo "cd /srv/vnpy/vnpy/api/ctp && bash ./build.sh && cp -af ./build/lib/*.so . && cd /srv/vnpy" >> ~/.bashrc'


# mount vnpy project
VOLUME ["/vnpy"]

# export port for web
EXPOSE 80
EXPOSE 22
EXPOSE 5900

# Install vn.py
#RUN git clone https://github.com/vnpy/vnpy.git
#WORKDIR vnpy
#RUN bash install.sh

# Set Entry Point
#WORKDIR vnpy/examples/WebTrader

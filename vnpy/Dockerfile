# Ubuntu 16.04 for running vn.py WebTrader

#FROM continuumio/anaconda
FROM ubuntu:16.04

MAINTAINER vn.py project <vn.py@foxmail.com>

# Use bash
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Install software
RUN apt-get update
RUN apt-get install gcc g++ cmake libboost-all-dev -y
RUN apt-get install git bzip2 vim nano -y 
RUN apt-get install libgl1-mesa-glx libglib2.0 libsm6 libxrender1 -y

# Install Anaconda
ADD https://repo.continuum.io/archive/Anaconda2-4.0.0-Linux-x86_64.sh .
RUN bash Anaconda2-4.0.0-Linux-x86_64.sh -b -p /opt/conda/


ENV PATH /opt/conda/bin:$PATH

# update conda source
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/

# 设置搜索时显示通道地址
RUN conda config --set show_channel_urls yes

# pip 使用豆瓣源
RUN sudo mkdir -p ~/.pip
COPY ./pip.conf ~/.pip/pip.conf

# update pip
RUN pip install --upgrade pip

# Install vn.py
#RUN git clone https://github.com/vnpy/vnpy.git
#WORKDIR vnpy
#RUN bash install.sh

# Set Entry Point
#WORKDIR vnpy/examples/WebTrader

# mount vnpy project
VOLUME ["/mnt"]

# export port for web
EXPOSE 80

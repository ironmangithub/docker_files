FROM nvidia/cuda:11.6.0-cudnn8-runtime-ubuntu18.04

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get -qq update && apt-get -qq -y install curl bzip2 && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh && bash /tmp/miniconda.sh -bfp /usr/local && rm -rf /tmp/miniconda.sh && conda install -y python=3.7 && conda update conda && apt-get -qq -y remove curl bzip2 && apt-get -qq -y autoremove && apt-get autoclean && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log && conda clean --all --yes

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get update && apt-get -y install sudo
RUN apt-get update && apt-get -y install libgl1
RUN apt-get update && apt-get -y install libglib2.0-0

ARG user=azureuser
ARG group=azureuser
ARG uid=1000
ARG gid=1000

RUN groupadd -g ${gid} ${group} && useradd -m -u ${uid} -g ${group} -s /bin/sh ${user} && echo "$user:$group" | chpasswd && adduser ${user} sudo

USER ${user}

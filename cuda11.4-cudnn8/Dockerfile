FROM nvidia/cuda:11.4.0-runtime-ubuntu18.04

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get -qq update && apt-get -qq -y install curl bzip2 && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh && bash /tmp/miniconda.sh -bfp /usr/local && rm -rf /tmp/miniconda.sh && conda install -y python=3.8 && conda update conda && apt-get -qq -y remove curl bzip2 && apt-get -qq -y autoremove && apt-get autoclean && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log && conda clean --all --yes

RUN apt-get update -y && apt-get upgrade -y

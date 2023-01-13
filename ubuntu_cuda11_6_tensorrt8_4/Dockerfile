# --------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
# --------------------------------------------------------------
# Dockerfile to run ONNXRuntime with TensorRT integration

FROM nvidia/cuda:11.6.1-cudnn8-devel-ubuntu20.04


# ONNX Runtime Variables
ARG ONNXRUNTIME_REPO=https://github.com/Microsoft/onnxruntime
ARG ONNXRUNTIME_BRANCH=master
ARG CMAKE_CUDA_ARCHITECTURES=37;50;52;60;61;70;75;80

ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/src/tensorrt/bin:/code/cmake-3.21.0-linux-x86_64/bin:/opt/miniconda/bin:${PATH}

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get update &&\
    apt-get install -y sudo git bash unattended-upgrades wget
RUN unattended-upgrade

# Install python3.8
RUN apt-get install -y --no-install-recommends \
    python3.8 \
    python3-pip \
    python3-dev \
    python3-wheel &&\
    cd /usr/local/bin &&\
    ln -s /usr/bin/python3.8 python &&\
    ln -s /usr/bin/pip3 pip;

RUN python --version
RUN pip install --upgrade pip 
RUN pip install setuptools>=41.0.0

RUN pip install numpy pycuda

# Install TensorRT
RUN v="8.4.3-1+cuda11.6" &&\
    apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub &&\
    apt-get update &&\
    sudo apt-get install -y libnvinfer8=${v} libnvonnxparsers8=${v} libnvparsers8=${v} libnvinfer-plugin8=${v} \
        libnvinfer-dev=${v} libnvonnxparsers-dev=${v} libnvparsers-dev=${v} libnvinfer-plugin-dev=${v} \
        python3-libnvinfer=${v} libnvinfer-samples=${v}

RUN apt-get update -y && apt-get upgrade -y

# Compile trtexec
RUN cd /usr/src/tensorrt/samples/trtexec && make

WORKDIR /

FROM nvidia/cuda:11.6.2-cudnn8-devel-ubuntu20.04 as base

RUN apt-get update --fix-missing
RUN apt upgrade -y
RUN DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get -y install tzdata

RUN apt install -y --no-install-recommends \
    sudo \
    vim \
    bash \
    wget \
    python3 \
    python3-pip \
    python3-wheel \
    python3-venv \
    zip \
    ca-certificates \
    curl

RUN cd /usr/local/bin && \
    ln -s /usr/bin/python3 python && \
    ln -s /usr/bin/pip3 pip;
RUN pip install --upgrade pip
RUN pip install "setuptools>=41.0.0" "wheel>=0.35.1" jinja2 typing-extensions pandas seaborn scipy absl-py numpy packaging

RUN pip3 install torch==1.13.0 --extra-index-url https://download.pytorch.org/whl/cu116

FROM base as build

ARG FASTERTRANSFORMER_REPO=https://github.com/NVIDIA/FasterTransformer.git
ARG FASTERTRANSFORMER_BRANCH=release/v5.1.1

RUN apt install -y --no-install-recommends \
    git \
    git-lfs \
    build-essential \
    cmake \
    python3-dev \
    libz-dev \
    libglib2.0-dev \
    libcurl4-openssl-dev \
    libssl-dev

# Prepare FasterTransformer repository & build FasterTransformer

RUN git clone --single-branch --branch ${FASTERTRANSFORMER_BRANCH} --recursive ${FASTERTRANSFORMER_REPO} FasterTransformer
RUN mkdir -p FasterTransformer/build && \
    cd FasterTransformer/build && \
    git submodule init && git submodule update && \
    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PYT=ON .. && make -j `nproc`

RUN pip install transformers==4.10.0
# Download the model needed
RUN python -c "from transformers import AutoModel; AutoModel.from_pretrained('t5-base');AutoModel.from_pretrained('t5-small'); AutoModel.from_pretrained('gpt2'); AutoModel.from_pretrained('gpt2-medium')"

RUN cd /FasterTransformer/examples/pytorch/t5/utils && \
    python huggingface_t5_ckpt_convert.py -i t5-base -o t5-base-fp32 -i_g 1 -weight_data_type fp32 && \
    python huggingface_t5_ckpt_convert.py -i t5-base -o t5-base-fp16 -i_g 1 -weight_data_type fp16 && \
    python huggingface_t5_ckpt_convert.py -i t5-small -o t5-small-fp32 -i_g 1 -weight_data_type fp32 && \
    python huggingface_t5_ckpt_convert.py -i t5-small -o t5-small-fp16 -i_g 1 -weight_data_type fp16

RUN cd /FasterTransformer/examples/pytorch/gpt/utils && \
    python huggingface_gpt_convert.py -i gpt2 -o gpt2-fp32 -i_g 1 -weight_data_type fp32 && \
    python huggingface_gpt_convert.py -i gpt2 -o gpt2-fp16 -i_g 1 -weight_data_type fp16 && \
    python huggingface_gpt_convert.py -i gpt2-medium -o gpt2-medium-fp32 -i_g 1 -weight_data_type fp32 && \
    python huggingface_gpt_convert.py -i gpt2-medium -o gpt2-medium-fp16 -i_g 1 -weight_data_type fp16

# Clone MLPerf inference & build loadgen & install

RUN git clone --single-branch --branch r2.1 https://github.com/mlcommons/inference.git mlperf_inference
RUN cd mlperf_inference && git submodule update --init
RUN cd mlperf_inference/loadgen &&\
    CFLAGS="-std=c++14 -O3" python setup.py bdist_wheel

FROM base as final
RUN mkdir -p /FasterTransformer/build
COPY --from=build /FasterTransformer/build /FasterTransformer/build
RUN mkdir -p /FasterTransformer/examples
COPY --from=build /FasterTransformer/examples /FasterTransformer/examples
RUN mkdir /mlperf_loadgen
COPY --from=build /mlperf_inference/loadgen/dist /mlperf_loadgen/
RUN pip install --force-reinstall /mlperf_loadgen/mlperf_loadgen-2.1-cp38-cp38-linux_x86_64.whl
RUN pip install transformers==2.5.1

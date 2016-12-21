FROM ubuntu:14.04
MAINTAINER Alex Shepard <alex@meatfreezer.com>

# caffe dependencies, with ipython
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python-dev \
        python-numpy \
        python-pip \
        python-scipy \
        ipython-notebook && \
    rm -rf /var/lib/apt/lists/*

# build caffe - cpu only
ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT
ENV CLONE_TAG=master
RUN git clone -b ${CLONE_TAG} --depth 1 https://github.com/BVLC/caffe.git . && \
    for req in $(cat python/requirements.txt) pydot; do pip install $req; done && \
    mkdir build && cd build && \
    cmake -DCPU_ONLY=1 .. && \
    make -j"$(nproc)"
ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

# digits dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    graphviz \
    python-dev \
    python-flask \
    python-flaskext.wtf \
    python-gevent \
    python-h5py \
    python-pil \
    python-protobuf

# download and install digits
ENV DIGITS_ROOT /opt/digits
RUN git clone https://github.com/NVIDIA/DIGITS.git $DIGITS_ROOT && \
    for req in $(cat $DIGITS_ROOT/requirements.txt) pydot; do pip install $req; done && \
    pip install -e $DIGITS_ROOT
ENV PATH $DIGITS_ROOT:$PATH

WORKDIR /workspace

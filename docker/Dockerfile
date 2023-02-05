FROM nvcr.io/nvidia/pytorch:21.12-py3

# based on work from Eleftherios Avramidis <eleftherios.avramidis@dfki.de>
LABEL description="Basic Marian 1.11.0 nvidia-docker container CUDA and pytorch support"

# Install necessary system packages 
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get upgrade -yq && apt-get install -yq \
                build-essential \
                git-core \
                pkg-config \
                libtool \
                zlib1g-dev \
                libbz2-dev \
                automake \
                python-dev \
                perl \
                libsparsehash-dev \
                libboost-all-dev \
                libprotobuf17 \
		protobuf-compiler \
		libprotobuf-dev \ 
        libtool-bin \
		openssl \ 
		libssl-dev \ 
		libgoogle-perftools-dev \
                wget \
		apt-transport-https ca-certificates gnupg software-properties-common \
                cmake \
                vim nano unzip gzip python3-pip php \
            && rm -rf /var/lib/apt/lists/*

RUN wget -qO- 'https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB' | apt-key add - && sh -c 'echo deb https://apt.repos.intel.com/mkl all main > /etc/apt/sources.list.d/intel-mkl.list' && apt-get update && apt-get install -yq intel-mkl-64bit-2020.0-088


ENV MARIANPATH /home/marian/marian

# Install Marian
# Optional:
# RUN git clone --depth 1 --branch 1.11.0 https://github.com/senisioi/marian
COPY . $MARIANPATH
RUN mkdir -p $MARIANPATH


RUN useradd marian && \
    chown -R marian:marian /home/marian && \
    mkdir -p /home/marian/.ssh


RUN wget https://ftp.gnu.org/gnu/libiconv/libiconv-1.17.tar.gz && \
    tar -xvf libiconv-1.17.tar.gz && \
    cd libiconv-1.17 && \
    ./configure --prefix=/usr/local --enable-silent-rules && \
    make && make install && \
    ldconfig && \
    libtool --finish /usr/local/lib


# Change to non-root privilege
USER marian

WORKDIR $MARIANPATH


RUN mkdir -p build && \
    cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release -DUSE_SENTENCEPIECE=ON -DCOMPILE_CPU=on -DCOMPILE_SERVER=on && \
    make -j8 && \
    echo "Making examples..." && \
    cd ../examples/tools && \
    make all

# Install tools
RUN pip3 install langid


# Direct the user to the examples directory
WORKDIR $MARIANPATH
ENV PATH=$PATH:$MARIANPATH/build


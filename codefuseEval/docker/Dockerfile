FROM pytorch/pytorch:2.0.0-cuda11.7-cudnn8-runtime

RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak \
    && sed -i "s@http://.*archive.ubuntu.com@https://mirrors.aliyun.com@g" /etc/apt/sources.list \
    && sed -i "s@http://.*security.ubuntu.com@https://mirrors.aliyun.comn@g" /etc/apt/sources.list \
    && pip config set global.index-url https://mirrors.aliyun.com/pypi/simple \
    && apt-get update && apt-get install -y curl npm git nano \
    && pip install fire \
    && mkdir /workspace/download

#install golang
RUN curl -o /workspace/download/go.tar.gz -SL https://mirrors.aliyun.com/golang/go1.18.4.linux-amd64.tar.gz \
    && tar -zxf /workspace/download/go.tar.gz -C /usr/local && rm /workspace/download/go.tar.gz
ENV PATH=/bin:/usr/local/go/bin:$PATH

#install nodejs
RUN curl -o /workspace/download/node.tar.gz -SL https://mirrors.aliyun.com/nodejs-release/v16.14.0/node-v16.14.0-linux-x64.tar.gz \
    && mkdir -p /usr/local/lib/nodejs && tar -zxf /workspace/download/node.tar.gz -C /usr/local/lib/nodejs && mv /usr/local/lib/nodejs/node-v16.14.0-linux-x64 /usr/local/lib/nodejs/node \
    && rm /workspace/download/node.tar.gz && npm install -g js-md5@0.7.3
ENV PATH=/usr/local/lib/nodejs/node/bin:$PATH
ENV NODE_PATH=/usr/local/lib/node_modules

#install gcc
    RUN apt-get install -y build-essential && apt-get install -y g++ \
    && curl -o /workspace/download/boost_1_75_0.tar.gz -SL https://boostorg.jfrog.io/artifactory/main/release/1.75.0/source/boost_1_75_0.tar.gz \
    && tar -zxf /workspace/download/boost_1_75_0.tar.gz && rm /workspace/download/boost_1_75_0.tar.gz && cd boost_1_75_0 \
    && ./bootstrap.sh --prefix=/usr/ && ./b2 && ./b2 install \
    && cd .. && rm -r boost_1_75_0

# upgrade openssl
RUN curl -o /workspace/download/openssl.tar.gz -SL https://www.openssl.org/source/old/3.0/openssl-3.0.0.tar.gz \
    && tar -zxf /workspace/download/openssl.tar.gz && cd openssl-3.0.0 && ./Configure && make && make install \
    && rm /workspace/download/openssl.tar.gz && rm -r /workspace/openssl-3.0.0
ENV PATH=/usr/bin/openssl:$PATH

#install rust
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH=/root/.cargo/bin:$PATH

#install jdk
RUN curl -o /workspace/download/jdk.tar.gz -SL https://download.oracle.com/java/18/archive/jdk-18.0.2.1_linux-x64_bin.tar.gz \
    && mkdir /usr/java && tar -zxf /workspace/download/jdk.tar.gz -C /usr/java && rm /workspace/download/jdk.tar.gz \
    && java_path=`ls /usr/java/${path}` && echo "export JAVA_HOME=/usr/java/${java_path}" >> ~/.profile

SHELL ["sh", "-lc"]
RUN update-alternatives --install /usr/bin/java java $JAVA_HOME/bin/java 20000 \
    && update-alternatives --install /usr/bin/javac javac $JAVA_HOME/bin/javac 20000 \
    && rm -r /workspace/download \
    && env

#download benchmark and all dependencies
RUN git clone https://github.com/simaocunha71/codefuse-evaluation.git
RUN cd codefuse-evaluation && pip install -r requirements.txt
RUN pip install git+https://github.com/google-research/bleurt.git

WORKDIR /workspace/codefuse-evaluation/
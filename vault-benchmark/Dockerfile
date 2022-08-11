FROM       debian:bullseye

RUN echo "===> Installing  tools..."  && \
    apt-get -y update && \
    apt-get -y install build-essential curl git && \
    \
    echo "===> Installing wrk" && \
    WRK_VERSION=$(curl -L https://github.com/wg/wrk/raw/master/CHANGES 2>/dev/null | \
                  egrep '^wrk' | head -n 1 | awk '{print $2}') && \
    echo $WRK_VERSION  && \
    mkdir /opt/wrk && \
    cd /opt/wrk && \
    curl -L https://github.com/wg/wrk/archive/$WRK_VERSION.tar.gz | \
       tar zx --strip 1 && \
    make && \
    cp wrk /usr/local/bin/ && \
    \
    echo "===> Cleaning the system" && \
    \
    cd /tmp && \
    git clone https://github.com/hashicorp/vault-guides.git && \
    cp -rp vault-guides/operations/benchmarking/wrk-core-vault-operations /opt/wrk-core-vault-operations && \
    \
    apt-get -f -y --auto-remove remove build-essential curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /opt/wrk/ /tmp/vault-guides

WORKDIR /opt/wrk-core-vault-operations

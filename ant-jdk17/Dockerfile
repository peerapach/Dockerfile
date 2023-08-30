FROM eclipse-temurin:17-jdk-jammy

ENV DEBIAN_FRONTEND=noninteractive \
	  LANG=C.UTF-8 \
	  LC_ALL=C.UTF-8 \
	  ANT_VERSION=1.10.12 \
    ANT_MIRROR=https://downloads.apache.org \
	  ANT_HOME=/opt/ant

WORKDIR /tmp

RUN apt-get update && apt-get install -y --no-install-recommends wget \
    && wget --no-check-certificate --no-cookies ${ANT_MIRROR}/ant/binaries/apache-ant-${ANT_VERSION}-bin.tar.gz \
    && tar -zvxf apache-ant-${ANT_VERSION}-bin.tar.gz -C /opt/ \
    && ln -sf /opt/apache-ant-${ANT_VERSION} /opt/ant \
    && rm -f apache-ant-${ANT_VERSION}-bin.tar.gz* \
    && update-alternatives --install /usr/bin/ant ant /opt/ant/bin/ant 1000 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

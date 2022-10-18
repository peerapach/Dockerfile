FROM debian:bullseye

RUN apt-get update \
    && apt install --no-install-recommends --no-install-suggests -y openssh-server sudo ca-certificates \
    && rm -rf  /etc/apt/sources.list.d/temp.list

COPY --chmod=0554 docker-entrypoint.sh /

ENTRYPOINT ["/docker-entrypoint.sh"]
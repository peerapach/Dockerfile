FROM debian:bullseye

RUN apt-get update \
    && apt install -y openssh-server sudo ca-certificates

COPY --chmod=0554 docker-entrypoint.sh /

ENTRYPOINT ["/docker-entrypoint.sh"]
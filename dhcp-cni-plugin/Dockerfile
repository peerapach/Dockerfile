From centos:7

env VERSION=v0.7.4

WORKDIR /tmp
RUN curl -SL https://github.com/containernetworking/plugins/releases/download/$VERSION/cni-plugins-amd64-$VERSION.tgz | tar xvzf -

From alpine:latest

COPY --from=0 /tmp/dhcp /dhcp
VOLUME ["/run"]
RUN rm -f /run/cni/dhcp.sock

CMD ["/dhcp", "daemon"]

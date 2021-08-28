FROM registry.access.redhat.com/ubi8/ubi AS ubi-micro-build
RUN mkdir -p /mnt/rootfs
RUN yum install --installroot /mnt/rootfs openssl ca-certificates coreutils-single glibc-minimal-langpack --releasever 8 --setopt install_weak_deps=false --nodocs -y; yum --installroot /mnt/rootfs clean all
RUN rm -rf /mnt/rootfs/var/cache/* /mnt/rootfs/var/log/dnf* /mnt/rootfs/var/log/yum.*

FROM scratch

LABEL name="ubi8/ubi-custom"
LABEL version="8.4"

#label for EULA
LABEL com.redhat.license_terms="https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI"

#labels for container catalog
LABEL summary="ubi8 micro image with ca-certificates"
LABEL description="Very small image which doesn't install the package manager."

COPY --from=ubi-micro-build /mnt/rootfs/ /

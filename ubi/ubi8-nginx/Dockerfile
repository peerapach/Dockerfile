FROM registry.access.redhat.com/ubi8/ubi AS ubi-micro-build
RUN mkdir -p /mnt/rootfs
COPY config/nginx.repo /etc/yum.repos.d/nginx.repo
RUN yum install --installroot /mnt/rootfs nginx --releasever 8 --setopt install_weak_deps=false --setopt=nginx.module_hotfixes=true --nodocs -y; yum --installroot /mnt/rootfs clean all
RUN rm -rf /mnt/rootfs/var/cache/* /mnt/rootfs/var/log/dnf* /mnt/rootfs/var/log/yum.*


FROM registry.access.redhat.com/ubi8/ubi-micro

LABEL name="ubi8/ubi-nginx"
LABEL version="8.4"
#label for EULA
LABEL com.redhat.license_terms="https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI"
#labels for container catalog
LABEL summary="ubi8 micro image with nginx from official"
LABEL description="Very small image which doesn't install the package manager."

COPY --from=ubi-micro-build /mnt/rootfs/ /
COPY config/nginx.conf /etc/nginx/nginx.conf

RUN ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log 

EXPOSE 8080
STOPSIGNAL SIGTERM
USER 1001
ENTRYPOINT ["nginx", "-g", "daemon off;"]
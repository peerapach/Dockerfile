FROM debian:bullseye-slim as vault-server

ARG VAULT_VERSION=1.12.1

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates=20210119 \
        gpg=2.2.27* \
        libcap2-bin=1:2.44-* \
        lsb-release=11.1.* \
        wget=1.21-* \
        procps=2:3.3.* \
        gnupg=2.2.27-* \
        unzip=6.0-* \
    && rm -rf /var/lib/apt/lists/*

# Copy license for Red Hat certification.
COPY LICENSE /licenses/mozilla.txt

# Set up certificates, our base tools, and Vault.
RUN set -eux; \
    VAULT_GPGKEY=C874011F0AB405110D02105534365D9472D7468F; \
    found=''; \
    for server in \
        hkps://keys.openpgp.org \
        hkps://keyserver.ubuntu.com \
        hkps://pgp.mit.edu \
    ; do \
        echo "Fetching GPG key $VAULT_GPGKEY from $server"; \
        gpg --batch --keyserver "$server" --recv-keys "$VAULT_GPGKEY" && found=yes && break; \
    done; \
    test -z "$found" && echo >&2 "error: failed to fetch GPG key $VAULT_GPGKEY" && exit 1; \
    mkdir -p /tmp/build && \
    cd /tmp/build && \
    wget https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_linux_amd64.zip && \
    wget https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_SHA256SUMS && \
    wget https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_SHA256SUMS.sig && \
    gpg --batch --verify vault_${VAULT_VERSION}_SHA256SUMS.sig vault_${VAULT_VERSION}_SHA256SUMS && \
    grep vault_${VAULT_VERSION}_linux_amd64.zip vault_${VAULT_VERSION}_SHA256SUMS | sha256sum -c && \
    unzip -d /tmp/build vault_${VAULT_VERSION}_linux_amd64.zip && \
    cp /tmp/build/vault /bin/vault && \
    if [ -f /tmp/build/EULA.txt ]; then mkdir -p /usr/share/doc/vault; mv /tmp/build/EULA.txt /usr/share/doc/vault/EULA.txt; fi && \
    if [ -f /tmp/build/TermsOfEvaluation.txt ]; then mkdir -p /usr/share/doc/vault; mv /tmp/build/TermsOfEvaluation.txt /usr/share/doc/vault/TermsOfEvaluation.txt; fi && \
    cd /tmp && \
    rm -rf /tmp/build && \
    gpgconf --kill dirmngr && \
    gpgconf --kill gpg-agent && \
    rm -rf /root/.gnupg

RUN groupadd --gid 1000 vault && \
    adduser --uid 1000 --system --gid 1000 vault && \
    usermod -a -G root vault

ENV HOME /home/vault
RUN mkdir -p /vault/logs && \
    mkdir -p /vault/file && \
    mkdir -p /vault/config && \
    mkdir -p $HOME && \
    chown -R vault /vault && chown -R vault $HOME && \
    chgrp -R 0 $HOME && chmod -R g+rwX $HOME && \
    chgrp -R 0 /vault && chmod -R g+rwX /vault

WORKDIR /vault
# Expose the logs directory as a volume since there's potentially long-running
# state in there
VOLUME /vault/logs

# Expose the file directory as a volume since there's potentially long-running
# state in there
VOLUME /vault/file

# 8200/tcp is the primary interface that applications use to interact with
# Vault.
EXPOSE 8200

COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
ENTRYPOINT ["docker-entrypoint.sh"]

USER vault

# By default you'll get a single-node development server that stores everything
# in RAM and bootstraps itself. Don't use this configuration for production.
CMD ["server", "-dev"]

# ====================================================================================================================
FROM debian:bullseye-slim as oracle-client

ARG CKSUM=3809844099

RUN apt-get update && apt-get install -y --no-install-recommends \
    && unzip=6.0-* \
    && ca-certificates=20210119 \
    && wget=1.21-* \
    && wget --progress=dot:giga "https://download.oracle.com/otn_software/linux/instantclient/instantclient-basic-linuxx64.zip" \
    && echo "Check sum for verify the dowloaded Oracle client file" \
    && cksum instantclient-basic-linuxx64.zip > /tmp/cksum \
    && grep -q "$CKSUM" /tmp/cksum \
    && mkdir -p /opt/oracle/bin/client \
    && unzip instantclient-basic-linuxx64.zip -d /opt/oracle/bin/client/ \
    && rm instantclient-basic-linuxx64.zip

# ====================================================================================================================
FROM debian:bullseye-slim as vault-oracle-plugin

ARG ORA_PLUGIN_VERSION=0.7.0

RUN apt-get update && apt-get install -y --no-install-recommends gnupg gpg unzip ca-certificates wget=1.21-* \
    && VAULT_GPGKEY=C874011F0AB405110D02105534365D9472D7468F; \
    found=''; \
    for server in \
        hkps://keys.openpgp.org \
        hkps://keyserver.ubuntu.com \
        hkps://pgp.mit.edu \
    ; do \
        echo "Fetching GPG key $VAULT_GPGKEY from $server"; \
        gpg --batch --keyserver "$server" --recv-keys "$VAULT_GPGKEY" && found=yes && break; \
    done; \
    test -z "$found" && echo >&2 "error: failed to fetch GPG key $VAULT_GPGKEY" && exit 1; \
    mkdir -p /tmp/build  \
    && cd /tmp/build \
    && wget https://releases.hashicorp.com/vault-plugin-database-oracle/${ORA_PLUGIN_VERSION}/vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_linux_amd64.zip \
    && wget https://releases.hashicorp.com/vault-plugin-database-oracle/${ORA_PLUGIN_VERSION}/vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_SHA256SUMS \
    && wget https://releases.hashicorp.com/vault-plugin-database-oracle/${ORA_PLUGIN_VERSION}/vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_SHA256SUMS.sig \
    && gpg --batch --verify vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_SHA256SUMS.sig vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_SHA256SUMS \
    && grep vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_linux_amd64.zip vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_SHA256SUMS | sha256sum -c \
    && unzip -d /tmp/build vault-plugin-database-oracle_${ORA_PLUGIN_VERSION}_linux_amd64.zip \
    && mkdir -p /vault/plugins \
    && cp /tmp/build/vault-plugin-database-oracle /vault/plugins/vault-plugin-database-oracle \
    && rm -rf /tmp/build \
    && gpgconf --kill dirmngr \
    && gpgconf --kill gpg-agent \
    && rm -rf /root/.gnupg

# ====================================================================================================================
FROM vault-server as vault-server-oracle-plugin

USER root

COPY --from=vault-oracle-plugin /vault/plugins/vault-plugin-database-oracle /vault/plugins/vault-plugin-database-oracle
# RUN setcap cap_ipc_lock=+ep /vault/plugins/vault-plugin-database-oracle
RUN apt-get update && apt-get install -y libaio1=0.3.112-* \
    && rm -rf /var/lib/apt/lists/*

COPY --from=oracle-client /opt/oracle/bin/client /opt/oracle/bin/client

RUN echo /opt/oracle/bin/client/instantclient_21_8 > /etc/ld.so.conf.d/oracle-instantclient.conf \
    && ldconfig

ENV LD_LIBRARY_PATH=/opt/oracle/bin/client/instantclient_21_8:$LD_LIBRARY_PATH

USER vault

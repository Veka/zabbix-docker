# syntax=docker/dockerfile:1
FROM oraclelinux:8-slim

ARG MAJOR_VERSION=6.4
ARG ZBX_VERSION=${MAJOR_VERSION}.0

ENV TERM=xterm \
    ZBX_VERSION=${ZBX_VERSION} \
    PATH=/usr/local/go/bin:$PATH

LABEL org.opencontainers.image.authors="Alexey Pustovalov <alexey.pustovalov@zabbix.com>" \
      org.opencontainers.image.description="Zabbix build base image contains all required packages to build Zabbix images" \
      org.opencontainers.image.documentation="https://www.zabbix.com/documentation/${MAJOR_VERSION}/manual/installation/containers" \
      org.opencontainers.image.licenses="GPL v2.0" \
      org.opencontainers.image.title="Zabbix build base" \
      org.opencontainers.image.url="https://zabbix.com/" \
      org.opencontainers.image.vendor="Zabbix LLC" \
      org.opencontainers.image.version="${ZBX_VERSION}"

RUN set -eux && \
    INSTALL_PKGS="autoconf \
            automake \
            bash \
            gcc \
            go-toolset \
            patch \
            pcre2-devel \
            libcurl-devel \
            libevent-devel \
            libssh-devel \
            libxml2-devel \
            openldap-devel \
            make \
            mysql-devel \
            net-snmp-devel \
            OpenIPMI-devel \
            openldap-devel \
            sqlite-devel \
            postgresql-devel \
            java-1.8.0-openjdk-devel \
            git \
            gettext \
            unixODBC-devel \
            # openssl-devel \
            openssl3-devel \
            php-devel \
            oracle-instantclient-basic \
            oracle-instantclient-devel \
            oracle-instantclient-sqlplus" && \
    microdnf -y install oracle-epel-release-el8 oracle-instantclient-release-el8 && \
    microdnf -y module enable php:8.0 && \
    microdnf -y module enable mysql && \
    microdnf module enable postgresql:13 && \
    microdnf -y install \
        --disablerepo "*" \
        --enablerepo "ol8_baseos_latest" \
        --enablerepo "ol8_appstream" \
        --enablerepo "ol8_codeready_builder" \
        --enablerepo "ol8_developer_EPEL" \
        --enablerepo "ol8_oracle_instantclient21" \
        --setopt=install_weak_deps=0 \
        --best \
        --nodocs ${INSTALL_PKGS} && \
    curl -fsSL https://pecl.php.net/get/oci8-3.0.1.tgz | tar -zx && \
    cd oci8-3.0.1 && \
    export LD_LIBRARY_PATH=/usr/lib/oracle/21/client64/lib && \
    phpize && \
    ./configure -with-oci8=shared,instantclient,/usr/lib/oracle/21/client64/lib && \
    make install && \
    microdnf -y clean all && \
    rm -rf /var/cache/yum /var/lib/yum/yumdb/* /usr/lib/udev/hwdb.d/* && \
    rm -rf /var/cache/dnf /etc/udev/hwdb.bin /root/.pki

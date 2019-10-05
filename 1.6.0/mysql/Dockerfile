FROM alpine as builder

ENV KEA_DHCP_VERSION=1.6.0
ENV LOG4_CPLUS_VERSION=1.2.1

RUN apk --update add curl make g++ openssl-dev boost-dev mysql-dev && \
    curl -SL https://sourceforge.net/projects/log4cplus/files/log4cplus-stable/${LOG4_CPLUS_VERSION}/log4cplus-${LOG4_CPLUS_VERSION}.tar.gz | tar -zxC /root && \
    cd /root/log4cplus-${LOG4_CPLUS_VERSION} && \
    ./configure && \
    make -s -j$(nproc) && \
    make install && \
    curl -SL https://downloads.isc.org/isc/kea/${KEA_DHCP_VERSION}/kea-${KEA_DHCP_VERSION}.tar.gz | tar -xzC /root && \
    cd /root/kea-${KEA_DHCP_VERSION} && \
    ./configure --enable-shell --with-dhcp-mysql=/usr/bin/mysql_config && \
    make -s -j$(nproc) && \
    make install-strip && \
    apk del --purge .build-deps && \
    rm -rf /root/*

FROM alpine:latest

RUN apk --no-cache add boost openssl boost mysql

COPY --from=builder /usr/local /usr/local/

ENTRYPOINT ["/usr/local/sbin/kea-dhcp4"]
CMD ["-c", "/usr/local/etc/kea/kea-dhcp4.conf"]

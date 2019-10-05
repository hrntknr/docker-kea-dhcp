FROM alpine as builder

ENV KEA_DHCP_VERSION=1.7.0

RUN echo "@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    apk --update add curl make g++ openssl-dev boost-dev log4cplus-dev@testing && \
    curl -SL https://downloads.isc.org/isc/kea/${KEA_DHCP_VERSION}/kea-${KEA_DHCP_VERSION}.tar.gz | tar -xzC /root && \
    cd /root/kea-${KEA_DHCP_VERSION} && \
    ./configure --enable-shell && \
    make -s -j$(nproc) && \
    make install-strip && \
    apk del --purge .build-deps && \
    rm -rf /root/*

FROM alpine:latest

RUN apk --no-cache add boost openssl boost

COPY --from=builder /usr/local /usr/local/

ENTRYPOINT ["/usr/local/sbin/kea-dhcp4"]
CMD ["-c", "/usr/local/etc/kea/kea-dhcp4.conf"]

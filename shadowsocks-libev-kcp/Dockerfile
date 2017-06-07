# Docker Image - Shadowsocks + KCP
# VERSION 1.0

# Use Alpine Linux as the base image
FROM alpine

# Maintainer Information
LABEL maintainer "noreply@randyzhong.me"

# Define Shadowsocks-libev version
ARG SS_LIBEV_VERSION=3.0.6
ARG SS_LIBEV_URL=https://github.com/shadowsocks/shadowsocks-libev/releases/download/v${SS_LIBEV_VERSION}/shadowsocks-libev-${SS_LIBEV_VERSION}.tar.gz

# Define KCPTUN version
ARG KCP_VERSION=20170525
ARG KCP_URL=https://github.com/xtaci/kcptun/releases/download/v${KCP_VERSION}/kcptun-linux-amd64-${KCP_VERSION}.tar.gz

# Define TimeZone
ARG TIMEZONE=Asia/Shanghai

# Install dependencies
RUN set -ex && \
    apk add --no-cache --virtual .build-deps \
                                autoconf \
                                build-base \
                                curl \
                                libev-dev \
                                libtool \
                                linux-headers \
                                udns-dev \
                                libsodium-dev \
                                mbedtls-dev \
                                pcre-dev \
                                tar \
                                tzdata \
                                udns-dev \
    && cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && cd /tmp \
    && curl -sSL ${KCP_URL} | tar xz server_linux_amd64 \
    && mv server_linux_amd64 /usr/bin/ \
    && curl -sSL ${SS_LIBEV_URL} | tar xz --strip 1 \
    && ./configure --prefix=/usr --disable-documentation \
    && make install \
    && cd ../ \
    && runDeps="$( \
        scanelf --needed --nobanner /usr/bin/ss-* \
            | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
            | xargs -r apk info --installed \
            | sort -u \
    )" \
    && apk add --no-cache --virtual .run-deps $runDeps \
    && apk del .build-deps \
    && rm -rf /tmp/* \
       /var/cache/apk/*

# Define Shadowsocks and KCP Ports
ENV SS_SERVER_PORT=5020 \
KCP_PORT=5021

# Expose Shadowsocker & KCP port
EXPOSE ${SS_SERVER_PORT}/tcp ${SS_SERVER_PORT}/udp
EXPOSE ${KCP_PORT}/udp

# Define Shadowsocks & KCP Environment variables
ENV SS_CONFIG="-s 0.0.0.0 \
               -p ${SS_SERVER_PORT} \ 
               -k secret \
               -m aes-256-cfb \
               -t 300 \
               --fast-open \
               -a nobody \ 
               -d 8.8.8.8 \
               -d 8.8.4.4 \
               -u \
               -f /tmp/ss.pid" \
KCP_CONFIG="-t 127.0.0.1:${SS_SERVER_PORT} \
               -l :${KCP_PORT} \ 
                --key kcptun \ 
                --mode fast \ 
                --crypt salsa20 \ 
                --mtu 1350 \ 
                --sndwnd 256 \
                --rcvwnd 1024 \
                --nocomp \
                --dscp 46"

# Start Services
CMD ss-server ${SS_CONFIG} && server_linux_amd64 ${KCP_CONFIG}
ARG BUILD_FROM=ghcr.io/hassio-addons/base:13.2.2
# ARG BUILD_FROM

# First build binary
FROM golang:alpine AS builder
ARG CLOUDFLARE_DYNDNS_VERSION="main"
# ARG CLOUDFLARE_DYNDNS_VERSION
ARG BUILD_ARCH
RUN apk add --no-cache --update \
    git \
    make \
    && mkdir -p /go/src/gitlab.com/_p0l0_ \
    && cd /go/src/gitlab.com/_p0l0_ \
    && git clone https://github.com/Cemu0/cloudflare-dyndns.git \
    && cd cloudflare-dyndns && mkdir -p build
    # && git checkout tags/${CLOUDFLARE_DYNDNS_VERSION} -b ${CLOUDFLARE_DYNDNS_VERSION}-branch
WORKDIR /go/src/gitlab.com/_p0l0_/cloudflare-dyndns/src

# RUN VERSION=${CLOUDFLARE_DYNDNS_VERSION} make \
#     && case "${BUILD_ARCH}" in \
# 		aarch64) export CFARCH='arm64' ;; \
# 		amd64) export CFARCH='amd64' ;; \
# 		i386) export CFARCH='386' ;; \
#         armv7) export CFARCH='arm' ;; \
# 	esac \
#     && cp build/cloudflare-dyndns-${CLOUDFLARE_DYNDNS_VERSION}-linux_$CFARCH build/cloudflare-dyndns
RUN go build -a -installsuffix cgo -o ../build/cloudflare-dyndns


# Build Home Assistant Container
FROM ${BUILD_FROM}
ARG BUILD_VERSION

LABEL version=${BUILD_VERSION} \
    description="Home Assistant Addon for DynDNS using Cloudflare" \
    mantainer="https://github.com/p0l0"

WORKDIR /app 
COPY --from=builder go/src/gitlab.com/_p0l0_/cloudflare-dyndns/build/cloudflare-dyndns cloudflare-dyndns

# Copy data
COPY data/run.sh /
RUN chmod +x /run.sh

CMD [ "/run.sh" ]
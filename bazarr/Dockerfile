ARG BUILD_FROM=ghcr.io/hassio-addons/base:18.0.2
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
ARG BAZARR_VERSION=1.5.2
ARG UNRAR_VERSION=6.2.12
# hadolint ignore=DL3003,DL3042
RUN \
  apk add --no-cache --virtual .build-dependencies \
    autoconf=2.72-r1 \
    automake=1.17-r1 \
    build-base=0.5-r3 \
    linux-headers=6.14.2-r0 \
    musl-dev=1.2.5-r10 \
    py3-pip=25.1.1-r0 \
    python3-dev=3.12.11-r0 \
  \
  && apk add --no-cache \
    7zip=24.09-r0 \
    ffmpeg=6.1.2-r2 \
    mediainfo=25.03-r0 \
    py3-setuptools=80.9.0-r0 \
    python3=3.12.11-r0 \
    unzip=6.0-r15 \
  \
  && curl -J -L -o /tmp/unrar.tar.gz \
      "https://www.rarlab.com/rar/unrarsrc-${UNRAR_VERSION}.tar.gz" \
  && tar zxvf \
      /tmp/unrar.tar.gz \
      -C /tmp \
  \  
  && cd /tmp/unrar \
  && make \
  && install -v -m755 unrar /usr/local/bin \
  && cd - \
  \
  && curl -J -L -o /tmp/bazarr.zip \
      "https://github.com/morpheus65535/bazarr/releases/download/v${BAZARR_VERSION}/bazarr.zip" \
  && unzip \
      /tmp/bazarr.zip \
      -d /opt/bin \
  \
  && echo -e "PackageVersion=${BAZARR_VERSION}\nUpdateMethod=Docker\nBranch=master\nPackageAuthor=Home Assistant Community Add-ons" > "/opt/package_info" \
  \
  && cd /opt/bin \
  && pip3 install -r requirements.txt \
  \
  && apk del --no-cache --purge .build-dependencies \
  && rm -f -r \
    /tmp/*

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

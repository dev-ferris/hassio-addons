ARG BUILD_FROM
ARG HAWSER_VERSION=latest

# Pull the upstream Hawser image to extract the static Go binary
FROM ghcr.io/finsys/hawser:${HAWSER_VERSION} AS upstream

# Build on the Home Assistant base image (s6-overlay + bashio)
FROM ${BUILD_FROM}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Copy the Hawser binary from the upstream image
COPY --from=upstream /usr/local/bin/hawser /usr/local/bin/hawser

# Install Docker CLI + tzdata + ca-certificates so the agent can issue
# compose/swarm commands and reach the Dockhand server over TLS
RUN apk add --no-cache \
        ca-certificates \
        docker-cli \
        docker-cli-compose \
        tzdata \
        wget \
    && chmod +x /usr/local/bin/hawser \
    && mkdir -p /data/stacks

# Service supervisor scripts
COPY rootfs/ /
RUN chmod a+x /etc/services.d/hawser/run \
              /etc/services.d/hawser/finish

ENV PORT=2376 \
    DOCKER_SOCKET=/var/run/docker.sock \
    STACKS_DIR=/data/stacks

EXPOSE 2376

ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version="${BUILD_VERSION}" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.created="${BUILD_DATE}" \
    org.opencontainers.image.revision="${BUILD_REF}" \
    org.opencontainers.image.version="${BUILD_VERSION}"

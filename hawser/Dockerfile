ARG BUILD_FROM
# Pinned by build.yaml. No default on purpose: an unpinned "latest" would make
# the image non-reproducible and pull in untested upstream changes.
ARG HAWSER_VERSION

# Pull the upstream Hawser image to extract the static Go binary.
# The binary is built with CGO_ENABLED=0, so it runs unmodified on musl/Alpine.
# hadolint ignore=DL3006
FROM ghcr.io/finsys/hawser:${HAWSER_VERSION} AS upstream

# Build on the Home Assistant add-on base image (s6-overlay v3 + bashio)
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Docker CLI + compose plugin so the agent can drive containers and stacks.
# buildx is needed for stacks that use `build:`: without the plugin, Compose
# falls back to the classic daemon builder, which rejects BuildKit features
# such as build secrets, additional contexts and SSH forwarding. The upstream
# Hawser image ships it for the same reason.
# ca-certificates is needed to verify the Dockhand server in Edge mode.
# bash, curl, jq and tzdata already ship with the add-on base image.
RUN apk add --no-cache \
        ca-certificates \
        docker-cli \
        docker-cli-buildx \
        docker-cli-compose

# Copy the Hawser binary from the upstream image
COPY --from=upstream /usr/local/bin/hawser /usr/local/bin/hawser

# s6-overlay service definitions
COPY rootfs/ /

# The empty user-bundles.d/user2/contents.d is created here rather than shipped
# in rootfs/: s6-overlay's own `top` bundle references user2, so it has to exist
# even though this add-on starts nothing late - but git cannot track an empty
# directory, and a placeholder file inside contents.d would be read as a
# service name.
RUN chmod a+x \
        /usr/local/bin/hawser \
        /usr/bin/hawser-health \
        /etc/s6-overlay/s6-rc.d/hawser/run \
        /etc/s6-overlay/s6-rc.d/hawser/finish \
        /etc/s6-overlay/s6-rc.d/init-hawser/run \
    && mkdir -p \
        /data/stacks \
        /etc/s6-overlay/user-bundles.d/user2/contents.d

ENV DOCKER_SOCKET=/run/docker.sock \
    STACKS_DIR=/data/stacks

EXPOSE 2376

# The Supervisor turns an unhealthy container into an add-on restart when the
# user enables the Watchdog. The probe reads the s6 container environment, so
# it follows the configured PORT, BIND_ADDRESS and TLS settings.
HEALTHCHECK --start-period=30s --interval=30s --timeout=10s --retries=3 \
    CMD ["/usr/bin/hawser-health"]

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
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/hawser/DOCS.md" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.created="${BUILD_DATE}" \
    org.opencontainers.image.revision="${BUILD_REF}" \
    org.opencontainers.image.version="${BUILD_VERSION}"

# docker run \
#    --rm \
#    -v /etc/localtime:/etc/localtime:ro \
#    -v /etc/machine-id:/etc/machine-id:ro \
#    -v /tmp/.X11-unix:/tmp/.X11-unix \
#    -v ${HOME}/.stellarium:/home/stellarium/.stellarium \
#    -e "DISPLAY=unix${DISPLAY}" \
#    -e GDK_SCALE \
#    -e GDK_DPI_SCALE \
#    -v /dev/shm:/dev/shm \
#    --device /dev/snd \
#    --device /dev/dri \
#    --device /dev/video0 \
#    --group-add audio \
#    --group-add video \
#    --name stellarium \
#    "${DOCKER_REPO_PREFIX}/stellarium" "$@"


ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM alpine:latest AS builder

ARG GIT_REF
ARG VERSION
ENV VERSION="${VERSION}"

RUN apk update && apk --no-cache add --virtual .build-dependencies \
    cmake \
    make \
    g++ \
    git \
    && apk --no-cache add \
    mesa-gl \
    mesa-dri-intel \
    lua5.3-dev \
    lua-dev \
    ca-certificates \
    ttf-dejavu \
    qt5-qtbase-dev \
    qt5-qttools-dev \
    qt5-qtlocation-dev \
    qt5-qtserialport-dev \
    qt5-qtscript-dev \
    && git clone --depth 1 --branch "${GIT_REF}" --recurse-submodules https://github.com/Stellarium/stellarium \
    && cd stellarium \
    && cmake -DCMAKE_BUILD_MODE=Release \
    -DENABLE_MEDIA=0 \
    -DUSE_PLUGIN_TELESCOPECONTROL=0 \
    -DUSE_PLUGIN_ANGLEMEASURE=0 \
    -DUSE_PLUGIN_ARCHAEOLINES=0 \
    -DUSE_PLUGIN_COMPASSMARKS=0 \
    -DUSE_PLUGIN_EXOPLANETS=0 \
    -DUSE_PLUGIN_TELESCOPECONTROL=0 \
    -DUSE_PLUGIN_EQUATIONOFTIME=0 \
    -DUSE_PLUGIN_FOV=0 \
    -DUSE_PLUGIN_METEORSHOWERS=0 \
    -DUSE_PLUGIN_NOVAE=0 \
    -DUSE_PLUGIN_OCULARS=0 \
    -DUSE_PLUGIN_REMOTECONTROL=0 \
    -DUSE_PLUGIN_REMOTESYNC=0 \
    . \
    && make -j2 \
    && make install \
    && cd .. \
    && rm -rf stellarium \
    && apk del .build-dependencies \
    && rm -rf /var/cache/*

FROM alpine:latest

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED

COPY --from=builder /usr/local/share/stellarium /usr/local/share/stellarium
COPY --from=builder /usr/local/bin/stellarium /usr/local/bin/stellarium

RUN apk update && apk --no-cache add \
    mesa-gl \
    mesa-dri-intel \
    lua5.3 \
    lua \
    ca-certificates \
    ttf-dejavu \
    qt5-qtbase \
    qt5-qttools \
    qt5-qtlocation \
    qt5-qtserialport \
    qt5-qtscript

RUN addgroup stellarium \
    && adduser -G stellarium -s /bin/sh -D stellarium

USER stellarium

ENTRYPOINT ["stellarium"]

LABEL org.opencontainers.image.title="Stellarium" \
    org.opencontainers.image.description="Stellarium in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"


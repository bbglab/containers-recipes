FROM debian:buster-slim AS build

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        git \
        unzip && \
    rm -rf /var/lib/apt/lists/*

ADD --chown=user:group http://genetics.bwh.harvard.edu/cbase/CBaSE_v1.1.zip /tmp/cbase.zip

RUN --mount=type=bind,target=/files \
    unzip /tmp/cbase.zip -d /tmp/cbase && \
    cd /tmp/cbase && \
    git config --global user.name "BBGLab Build System" && \
    git config --global user.email "bbglab-build@irbbarcelona.org" && \
    git init && \
    git add . && \
    git commit -m "init_commit" && \
    cp /files/cbase.patch / && \
    git apply /cbase.patch

FROM debian:buster-slim AS final

RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        procps \
        python-mpmath \
        python-scipy \
        python-statsmodels && \
    rm -rf /var/lib/apt/lists/*

RUN --mount=type=bind,from=build,target=/build \
    mkdir -p /cbase && \
    cp -r /build/tmp/cbase/CBaSE_v1.1/* /cbase/

WORKDIR /cbase


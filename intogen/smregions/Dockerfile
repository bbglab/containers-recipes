FROM python:3.12-slim

RUN pip install --no-cache-dir --upgrade pip

ENV DEBIAN_FRONTEND=noninteractive
ENV BBGLAB_HOME=/smregions

RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        build-essential=12.9 \
        git \
        procps \
        zlib1g-dev=1:1.2.13.dfsg-1 && \
    rm -rf /var/lib/apt/lists/*

# hadolint ignore=DL3042
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install https://github.com/bbglab/smregions/archive/c7f4a22.zip

RUN smregions --help

ENTRYPOINT [ "/usr/local/bin/smregions" ]
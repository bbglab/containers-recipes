FROM debian:bullseye-slim AS build

ENV LC_ALL=C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get -y install --no-install-recommends --no-install-suggests \
        ca-certificates \
        git && \
    rm -rf /var/lib/apt/lists/*

# Clone SEISMIC repository and apply patch
RUN --mount=type=bind,target=/files \
    git config --global http.postBuffer 524288000 && \
    git config --global http.lowSpeedLimit 0 && \
    git config --global http.lowSpeedTime 999999 && \
    git config --global http.retry 5 && \
    git clone --filter=blob:none https://github.com/larsson-lab/SEISMIC.git seismic && \
    cd seismic && \
    git checkout 688e9611c3047d214abd597ee6913b8e591e51c0 && \
    git fetch --depth 1 && \
    cp /files/seismic.patch / && \
    git apply /seismic.patch


FROM rocker/r-ver:4.1 AS final

ENV LC_ALL=C.UTF-8
ENV R_BASE_VERSION=4.1.3


RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests \
        apt-utils \
        libcairo2-dev=1.16.0-4ubuntu1 \
        libcurl4-openssl-dev \
        libfontconfig1-dev=2.13.1-2ubuntu3 \
        libfreetype6-dev \
        libfribidi-dev=1.0.8-2ubuntu0.1 \
        libharfbuzz-dev \
        libjpeg-dev=8c-2ubuntu8 \
        libmagick++-dev \
        libpng-dev=1.6.37-2 \
        libssl-dev \
        libtiff5-dev \
        libxml2-dev \
        libxt-dev=1:1.1.5-1 \
        locales-all \
        procps=2:3.3.16-1ubuntu2.4 \
        r-base-html=3.6.3-2 \
        r-bioc-biostrings=2.54.0-1 \
        r-bioc-genomicranges=1.38.0+dfsg-1 \
        r-bioc-rsamtools=2.2.3-1 \
        r-cran-ade4=1.7-15-1 \
        r-cran-devtools=2.2.2-2 \
        r-cran-seqinr=3.6-1-1 \
        r-doc-html=3.6.3-2 && \
    rm -rf /var/lib/apt/lists/*

RUN --mount=type=bind,from=build,target=/build \
    Rscript /build/seismic/demo/install_R_dependencies.R && \
    mkdir -p /seismic && \
    cp -r /build/seismic/data /seismic/data && \
    cp -r /build/seismic/config /seismic/config && \ 
    cp /build/seismic/SEISMIC.R /seismic/SEISMIC.R

RUN chmod -R a+rx /seismic/

LABEL version="0.1.1"
LABEL description="SEISMIC: A tool for the analysis of somatic mutations in cancer adapted for IntOGen use."
LABEL author="Larsson Lab"
LABEL maintainer="BBGLab"
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
    git clone --filter=blob:none https://github.com/larsson-lab/SEISMIC.git /seismic && \
    cp /files/seismic.patch /seismic.patch


WORKDIR /seismic

RUN git checkout 688e9611c3047d214abd597ee6913b8e591e51c0 && \
    git fetch --depth 1 && \
    git apply /seismic.patch && \
    git status


FROM rocker/r-ver:4.1 AS final

ENV LC_ALL=C.UTF-8
ENV R_BASE_VERSION=4.1.3


RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests \
        apt-utils \
        curl \
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
        libwebp-dev \
        libxml2-dev \
        libxt-dev=1:1.1.5-1 \
        locales-all \
        pkg-config \
        procps=2:3.3.16-1ubuntu2.4 \
        r-base-html=3.6.3-2 \
        r-doc-html=3.6.3-2 && \
    rm -rf /var/lib/apt/lists/*

RUN install2.r --error --skipinstalled --ncpus -1 -r "https://cloud.r-project.org" -r getOption \
    BiocManager \
    cowplot \
    R.utils \
    textshaping \
    ragg \
    yaml \
    tidyverse \
    foreach \
    doSNOW \
    stringi \
    inline \
    Rcpp \
    fitdistrplus \
    data.table \
    seqinr \
    devtools \
    ade4

RUN --mount=type=bind,from=build,target=/build \
    Rscript /build/seismic/demo/install_R_dependencies.R && \
    mkdir -p /seismic && \
    cp -r /build/seismic/data /seismic/data && \
    cp -r /build/seismic/config /seismic/config && \ 
    cp /build/seismic/SEISMIC.R /seismic/SEISMIC.R

RUN curl -fLO https://bioconductor.org/packages/release/data/annotation/src/contrib/BSgenome.Hsapiens.UCSC.hg19_1.4.3.tar.gz && \
    R CMD INSTALL BSgenome.Hsapiens.UCSC.hg19_1.4.3.tar.gz && \
    rm BSgenome.Hsapiens.UCSC.hg19_1.4.3.tar.gz

RUN curl -fLO https://bioconductor.org/packages/3.14/data/annotation/src/contrib/BSgenome.Hsapiens.UCSC.hg38_1.4.4.tar.gz && \
    R CMD INSTALL BSgenome.Hsapiens.UCSC.hg38_1.4.4.tar.gz && \
    rm BSgenome.Hsapiens.UCSC.hg38_1.4.4.tar.gz


RUN chmod -R a+rx /seismic/

# This test is essential to store the annotated regions to speed up seismic runs
RUN --mount=type=bind,from=build,target=/build \
    cp /build/seismic/demo/TCGA_UCEC_SNVs.tsv /tmp/TCGA_UCEC_SNVs.tsv && \
    Rscript /seismic/SEISMIC.R /seismic/config/config.yaml /tmp/TCGA_UCEC_SNVs.tsv UCEC WXS hg38 1 || true && \
    rm /tmp/TCGA_UCEC_SNVs.tsv && \
    if [ ! -f /tmp/regions_output.tsv ] || [ ! -s /tmp/regions_output.tsv ]; then \
        echo "ERROR: Regions were not saved"; \
        exit 1; \
    fi


LABEL version="0.1.1"
LABEL description="SEISMIC: A tool for the analysis of somatic mutations in cancer adapted for IntOGen use."
LABEL author="Larsson Lab"
LABEL maintainer="BBGLab"
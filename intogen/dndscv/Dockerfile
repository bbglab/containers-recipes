FROM debian:trixie-slim AS build

ENV LC_ALL=C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get -y install --no-install-recommends --no-install-suggests \
        ca-certificates \
        git && \
    rm -rf /var/lib/apt/lists/*

# dndscv updated to May 15, 2025 commit 69007c2bbd2d6dae003a30dcfe5dda3df722b2f8
RUN --mount=type=bind,target=/files \
    git config --global http.postBuffer 524288000 && \
    git config --global http.lowSpeedLimit 0 && \
    git config --global http.lowSpeedTime 999999 && \
    git config --global http.retry 5 && \
    git clone --filter=blob:none https://github.com/im3sanger/dndscv.git && \
    cd dndscv && \
    git checkout 69007c2bbd2d6dae003a30dcfe5dda3df722b2f8 && \
    git fetch --depth 1  && \
    cp /files/dndscv.patch / && \
    git apply /dndscv.patch && \
    cd .. && \
    tar --exclude='.git' -cvzf dndscv.tar.gz dndscv

FROM rocker/r-ver:4.1 AS final

ENV LC_ALL=C.UTF-8
ENV R_BASE_VERSION=4.1.3


RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        procps=2:3.3.16-1ubuntu2.4 \
        libcurl4-openssl-dev \
        libssl-dev \
        libxml2-dev \
        zlib1g-dev \
        libbz2-dev \
        liblzma-dev && \
    rm -rf /var/lib/apt/lists/*

RUN install2.r --error --skipinstalled --ncpus -1 -r "https://cloud.r-project.org" -r getOption \
    BiocManager

# Install dependencies and dndscv
RUN --mount=type=bind,from=build,target=/build R --vanilla <<EOF
BiocManager::install(c("seqinr", "MASS", "GenomicRanges", "Biostrings", "IRanges", "Rsamtools", "poilog", "plyr"))
install.packages("/build/dndscv.tar.gz", repos=NULL, type="source")
if (!library("dndscv", logical.return=TRUE)) {
    quit(status=1, save="no")
}
EOF

RUN rm -rf /tmp/downloaded_packages \
    && strip /usr/local/lib/R/site-library/*/libs/*.so

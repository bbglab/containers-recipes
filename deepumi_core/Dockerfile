FROM python:3.11-slim

# Prevent interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# System dependencies needed for pysam, matplotlib, PDF backend, etc.
RUN apt-get update && apt-get install -y \
    build-essential \
    gcc \
    g++ \
    make \
    zlib1g-dev \
    libbz2-dev \
    liblzma-dev \
    libcurl4-openssl-dev \
    libssl-dev \
    libfreetype6-dev \
    libpng-dev \
    libjpeg-dev \
    pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Python dependencies
RUN pip install --no-cache-dir \
    numpy \
    pandas \
    matplotlib \
    seaborn \
    pysam \
    click

# Use non-interactive matplotlib backend
ENV MPLBACKEND=Agg

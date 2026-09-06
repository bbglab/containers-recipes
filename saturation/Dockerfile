FROM ghcr.io/astral-sh/uv:0.5-python3.12-bookworm-slim

# Set environment variables
ENV UV_COMPILE_BYTECODE=1


RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y git libcurl4-openssl-dev procps

RUN mkdir -p /bgdatacache/ /home/user/.config/bbglab/

RUN pip install joblib==1.4.2 statsmodels matplotlib

# Install dependencies and build the project
RUN mkdir -p /root/.cache/uv \
    && git clone https://github.com/bbglab/omega.git \
    && cd omega \
    && git checkout main-dev \
    && uv sync --frozen --no-dev \
    && uv build \
    && pip install dist/*.tar.gz \
    # Clean up unnecessary files
    && rm -rf /root/.cache/uv

RUN omega --help


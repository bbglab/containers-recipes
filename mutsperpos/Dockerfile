FROM ubuntu:22.04

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get install -y python3 python3-pip

# pysam 0.23.3
RUN pip install --no-cache-dir \
    pysam==0.23.3 \
    numpy \
    pandas \
    matplotlib

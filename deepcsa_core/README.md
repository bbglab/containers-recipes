# DeepCSA Core Docker Container

## Overview

This Docker container is designed for running the DeepCSA nextflow pipeline bin scripts within a conda environment.
It facilitates a consistent and reproducible environment, it also includes bgdata genome reference datasets hg38 and mm39.

## Container Details

- **Base Image**: mambaorg/micromamba:2.0.5-debian12-slim
- **Maintainer**: bbglab <bbglab@irbbarcelona.org>
- **Version**: 0.0.2-alpha
- **Description**: A Docker image with a conda environment for DeepCSA bin scripts and hg38 and mm39 bgdata references.
- **Source**: [GitHub Repository](https://github.com/bbglab/containers/deepcsa_core)

## Key Features

- **Conda Environment**: The container uses micromamba for managing the conda environment, ensuring that all dependencies are correctly installed and managed.
- **BGDATA Variables**: Sets up and configures the BGDATA variables for efficient dataset management.
- **Genome Reference Datasets**: Downloads and tests access to human (hg38) and mouse (mm39) genome reference datasets using bgdata.

## Usage

To run the container, use the following command:

```sh
docker run -it bbglab/deepcsa_core:0.0.1-alpha
```

## BGDATA Variables

The container sets up several BGDATA-related environment variables to manage dataset access and caching efficiently:

- `BGDATA_LOCAL`: Specifies the local directory for BGDATA cache (/home/bgdatacache). This directory is used to store cached dataset files, reducing the need to download them on runtime.
- `BGDATA_OFFLINE`: When set to True, this variable ensures that the container operates in offline mode, utilizing cached datasets without attempting to download new data.

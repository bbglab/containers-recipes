# DeepCSA Core Docker Container

## Overview

This Docker container is designed for running the DeepCSA nextflow pipeline bin scripts within a conda environment.
It facilitates a consistent and reproducible environment, it also includes bgdata genome reference datasets hg38 and mm39.

## Container Details

- **Base Image**: mambaorg/micromamba:2.0.5-debian12-slim
- **Maintainer**: bbglab <bbglab@irbbarcelona.org>
- **Version**: 0.1.0
- **Description**: A Docker image with a conda environment for DeepCSA bin scripts and hg38 and mm39 bgdata references.
- **Source**: [GitHub Repository](https://github.com/bbglab/containers/deepcsa_core)

## Key Features

- **Conda Environment**: The container uses micromamba for managing the conda environment, ensuring that all dependencies are correctly installed and managed.
- **BGDATA Variables**: Sets up and configures the BGDATA variables for efficient dataset management.
- **Genome Reference Datasets**: Downloads and tests access to human (hg38) and mouse (mm39) genome reference datasets using bgdata.

## Usage

To run the container, use the following command:

```sh
docker run -it bbglab/deepcsa_core:0.1.0
```

## BGDATA Variables

The container sets up several BGDATA-related environment variables to manage dataset access and caching efficiently:

- `BGDATA_LOCAL`: Specifies the local directory for BGDATA cache (/home/bgdatacache). This directory is used to store cached dataset files, reducing the need to download them on runtime.
- `BGDATA_OFFLINE`: When set to True, this variable ensures that the container operates in offline mode, utilizing cached datasets without attempting to download new data.

## Development

To add new dependencies to the container and ensure full reproducibility and installation of all required packages, this are the steps to follow:

1. Create a new conda environment with the `deepcsa.txt` file as the environment specification. This will ensure that all dependencies are correctly installed locally.

    ```sh
      micromamba create -n deepcsa --file deepcsa.txt -c conda-forge -c bioconda -c defaults
    ```

2. Install pip packages from the `requirements.txt` file into the conda environment.

    ```sh
      micromamba activate deepcsa
      pip install -r requirements.txt
    ```

3. Install the new packages in the environment such that the dependancies resolves correctly. This can be done via conda install or pip install.
4. Export the updated conda environment to the `deepcsa.txt` file to capture all installed packages and their versions.

    ```sh
      micromamba env export --explicit -n deepcsa| grep -v "prefix: " > deepcsa.txt
    ```

    - Alternatively, if you used pip to install new packages, update the `requirements.txt` file accordingly. Do not generate it automatically, as it may include unnecessary packages.

5. Rebuild the Docker container to include the updated conda environment. Make sure you bump the version number in the Dockerfile before rebuilding.

    ```sh
      docker build -t bbglab/deepcsa_core:<NEW_VERSION> .
    ```

6. Test the new container to ensure that all dependencies are correctly installed and that the DeepCSA bin scripts function as expected.

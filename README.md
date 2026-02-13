# BBGLab Containers Repository

[![License](https://img.shields.io/badge/License-Custom-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Enabled-2496ED?logo=docker)](https://hub.docker.com/u/bbglab)

A curated collection of Docker containers for cancer genomics, mutation analysis, and bioinformatics workflows. This repository provides reproducible, containerized environments for various computational biology tools used in cancer research and genomic analysis.

## Table of Contents

- [Overview](#overview)
- [Container Categories](#container-categories)
- [Quick Start](#quick-start)
- [Usage Guide](#usage-guide)
- [Development](#development)
- [Contributing](#contributing)

## Overview

This repository contains Docker container recipes for a comprehensive suite of bioinformatics tools, primarily focused on:

- **Cancer Driver Detection**: Statistical methods to identify genes under positive selection
- **Core Tools for somatic mutations analysis**: Basic tools for the processing of somatic mutations and performing basic analysis
- **Mutational Signature Analysis**: Tools for extracting and analyzing mutational signatures
- **Mutation and Genomic Analysis**: Various utilities for processing and analyzing genomic data

All containers are designed to provide:

- Reproducible computational environments
- Pre-configured dependencies and genome references
- Easy integration into computational workflows
- Support for both Docker and Singularity runtimes

## Container Categories

### Cancer Driver Discovery

- **intogen/** - Containers used in intogen pipeline:
  - `dndscv` - dN/dS ratio analysis for driver gene detection
  - `cbase` - Bayesian method for driver detection
  - `mutpanning` - Pan-cancer driver discovery tool
  - `oncodriveclustl` - Clustering-based driver detection
  - `oncodrivefml` - Functional mutation load analysis
  - `smregions` - Significantly mutated domains detection
  - `seismic` - Context-specific driver detection

- **dnds/** - dN/dS analysis tools
- **oncodriveclustl/** - OncodriveCLUSTL standalone
- **oncodrivefml/** - OncodriveFML standalone  
- **oncodrive3d/** - 3D protein structure-based driver detection
- **omega/** - dnds-like driver detection method

### Core Tools for somatic mutations analysis

- **deepcsa_core/** - DeepCSA pipeline core environment with genome references
- **bgreference/** - BBGLab genome reference in-house tool
- **bgtools/** - BBGLab utility tools

### Mutational Signatures

- **sigprofiler_official/** - Official SigProfiler suite
- **sigprofiler_matrix/** - SigProfiler matrix generation
- **sigprofilerassignment/** - Mutational signature assignment
- **hdp_sig_extraction/** - Hierarchical Dirichlet Process signature extraction

### Mutation and Genomic Analysis

- **expected_mutrate/** - Expected mutation rate calculation
- **mutsperpos/** - Position-specific mutation analysis
- **mutated_genomes_from_vaf/** - Genome reconstruction from VAF
- **o3d_stefano/** - O3D tools (Stefano's version)

### Additional containers

All containers not listed above are likely not maintained nor properly built and should not be used.

## Quick Start

### Prerequisites

- Docker (≥ 20.10) or Singularity (≥ 3.0)
- Sufficient disk space for genome references (varies by container)

### Building a Container

```bash
# Navigate to the container directory
cd containers/<container_name>

# Build the Docker image
docker build -t bbglab/<container_name>:<version> .

# Example: Build dndscv container
cd intogen/dndscv
docker build -t bbglab/intogen-dndscv:latest .
```

### Running a Container

```bash
# Run container interactively
docker run -it bbglab/<container_name>:<version>

# Run with mounted data directory
docker run -v /path/to/data:/data bbglab/<container_name>:<version> <command>

# Example: Run dndscv analysis
docker run -v $(pwd):/data bbglab/dndscv:latest Rscript /data/analysis.R
```

### Using with Singularity

```bash
# Pull Docker image as Singularity image
singularity pull docker://bbglab/<container_name>:<version>

# Run with Singularity
singularity exec <container_name>.sif <command>

# Example
singularity pull docker://bbglab/dndscv:latest
singularity exec dndscv_latest.sif Rscript analysis.R
```

## Usage Guide

### Working with Genome References

Many containers include pre-downloaded genome references (hg19, hg38, mm10, mm39) using the BBGLab bgdata system:

```bash
# Containers with bgdata support
docker run bbglab/deepcsa_core:latest python -c "from bgreference import hg38; print(hg38(1, 100000, 1000))"

# Mount custom reference directory
docker run -v /path/to/references:/bgdatacache bbglab/<container> <command>
```

### Environment Variables

Common environment variables used across containers:

```bash
# BGData configuration
BGDATA_LOCAL="/bgdatacache"           # Local cache directory
BGDATA_OFFLINE="True"                 # Offline mode (use cached data)
BBGLAB_HOME="/home/user/.config/bbglab/"

# R environment
R_BASE_VERSION="4.1.3"
LC_ALL="C.UTF-8"

# Python environment
PYTHONPATH="/usr/local/lib/python3.x/site-packages"
```

### Example Workflows

#### Running dN/dS Analysis

```bash
# Prepare mutation data
cat > mutations.tsv << EOF
sampleID	chr	pos	ref	mut
sample1	1	12345	C	T
sample1	1	67890	G	A
EOF

# Run dndscv analysis
docker run -v $(pwd):/data bbglab/dndscv:latest Rscript - << 'RSCRIPT'
library(dndscv)
mutations <- read.table("/data/mutations.tsv", header=TRUE)
result <- dndscv(mutations)
print(result)
RSCRIPT
```

#### Extracting Mutational Signatures

```bash
# Run SigProfiler matrix generation
docker run -v $(pwd):/data bbglab/sigprofiler_matrix:latest \
  SigProfilerMatrix.py \
  --project MyProject \
  --reference-genome GRCh38 \
  --path-to-input-files /data/vcfs/
```

### Data Mounting Strategies

```bash
# Mount current directory
docker run -v $(pwd):/data <container> <command>

# Mount multiple directories
docker run \
  -v /path/to/input:/input \
  -v /path/to/output:/output \
  -v /path/to/refs:/refs \
  <container> <command>

# Read-only mounts
docker run -v $(pwd):/data:ro <container> <command>
```

## Development

### Container Structure

Standard directory structure for containers:

```sh
container_name/
├── Dockerfile          # Main container definition
├── README.md          # Container-specific documentation
├── requirements.txt   # Python dependencies (if applicable)
├── conda.yml         # Conda environment (if applicable)
├── *.patch           # Patches for source code
└── test.*            # Test scripts
```

### Creating Singularity Images

```bash
# Build from Docker Hub
singularity build <container_name>.sif docker://bbglab/<container_name>:<version>

# Build from local Docker image
singularity build <container_name>.sif docker-daemon://bbglab/<container_name>:<version>

# Run Singularity container
singularity exec <container_name>.sif <command>
```

## Common Commands

### Docker Operations

```bash
# List local images
docker images | grep bbglab

# Remove container
docker rmi bbglab/<container_name>:<version>

# Clean up unused images
docker system prune -a

# Inspect container
docker inspect bbglab/<container_name>:<version>

# View container history
docker history bbglab/<container_name>:<version>
```

### Debugging Containers

```bash
# Run interactive shell
docker run -it --entrypoint /bin/bash bbglab/<container_name>:<version>

# Check installed packages (Python)
docker run bbglab/<container_name>:<version> pip list

# Check installed packages (R)
docker run bbglab/<container_name>:<version> Rscript -e "installed.packages()[,c('Package','Version')]"

# View logs
docker logs <container_id>
```

## Contributing

We welcome contributions! To add a new container or improve existing ones:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-container`)
3. Add your container with proper documentation
4. Test thoroughly with sample data
5. Submit a pull request

### Container Guidelines

- Use official base images when possible
- Pin specific versions for reproducibility
- Include README.md with usage examples
- Add metadata labels (maintainer, version, description)
- Minimize image size (multi-stage builds, cleanup)
- Document all environment variables
- Include test data or test commands

## License

This repository is maintained by BBGLab. Individual tools within containers may have their own licenses. Please refer to respective tool documentation.

## Contact

**BBGLab** - Biomedical Genomics Lab  
Email: [bbglab@irbbarcelona.org](mailto:bbglab@irbbarcelona.org)
Website: [https://bbglab.irbbarcelona.org](https://bbglab.irbbarcelona.org)

## Citation

If you use these containers in your research, please cite:

- The relevant tools (see individual container documentation)
- This repository: `bbglab/containers-recipes` (GitHub)

---

**Note**: This repository is under active development. Container versions and availability may change. For production use, always pin specific versions.

FROM mambaorg/micromamba:1.5.8-lunar

COPY --chown=$MAMBA_USER:$MAMBA_USER conda.yml /tmp/conda.yml

RUN micromamba install -y -n base -f /tmp/conda.yml && \
    micromamba install -y -n base \
        conda-forge::procps-ng \
        bioconda::bioconductor-genomicranges \
        bioconda::bioconductor-biomart \
        conda-forge::r-dplyr \
        conda-forge::r-devtools \
        conda-forge::r-optparse && \
    micromamba clean -a -y

ENV PATH="$MAMBA_ROOT_PREFIX/bin:$PATH"

USER root
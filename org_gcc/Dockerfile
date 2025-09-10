# syntax=docker/dockerfile:1
FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive
ARG OPENFOAM_VERSION="12"

# Install build dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      git build-essential cmake flex bison zlib1g-dev libopenmpi-dev openmpi-bin ca-certificates \
      python3 wget libfl-dev libxt-dev gnuplot && \
    rm -rf /var/lib/apt/lists/*

# Create user
RUN useradd -ms /bin/bash openfoam
USER openfoam
WORKDIR /home/openfoam

# Clone source
RUN git clone https://github.com/OpenFOAM/OpenFOAM-${OPENFOAM_VERSION}.git OpenFOAM-$OPENFOAM_VERSION && \
    git clone https://github.com/OpenFOAM/ThirdParty-${OPENFOAM_VERSION}.git ThirdParty-${OPENFOAM_VERSION}

WORKDIR /home/openfoam/OpenFOAM-${OPENFOAM_VERSION}

# Source OpenFOAM environment and build
RUN bash -lc "\
    source etc/bashrc && \
    cd ../ThirdParty-${OPENFOAM_VERSION} && \
    ./Allwmake && \
    cd ../OpenFOAM-${OPENFOAM_VERSION} && \
    ./Allwmake -j $(nproc)"

# Expose environment via entrypoint
ENTRYPOINT ["/bin/bash", "-lc", "source /home/openfoam/OpenFOAM-${OPENFOAM_VERSION}/etc/bashrc && exec bash"]

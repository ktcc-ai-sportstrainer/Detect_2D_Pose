FROM mcr.microsoft.com/devcontainers/base:ubuntu

USER root

# NVIDIA GPGキーとリポジトリの追加
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y --no-install-recommends \
        wget \
        bzip2 \
        ca-certificates \
        gnupg2 \
        libyaml-dev \
        ninja-build \
    && wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb \
    && dpkg -i cuda-keyring_1.0-1_all.deb \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        cuda-minimal-build-12-1 \
        cuda-libraries-dev-12-1 \
        cuda-command-line-tools-12-1 \
        cuda-libraries-12-1 \
        cuda-toolkit-12-1 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Mambaforge
ENV CONDA_DIR=/opt/conda
RUN wget -q "https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-$(uname)-$(uname -m).sh" -O /tmp/mambaforge.sh \
    && chmod +x /tmp/mambaforge.sh \
    && bash /tmp/mambaforge.sh -b -p "${CONDA_DIR}" \
    && rm /tmp/mambaforge.sh

# CUDA環境変数の設定（12.1に変更）
ENV PATH=/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:${LD_LIBRARY_PATH}
ENV PATH="${CONDA_DIR}/bin:${PATH}"

# Initialize conda for shell interaction
RUN mamba init bash

# Create conda environments
RUN mamba create -n dev python=3.10 -y \
    && mamba create -n alphapose python=3.10 -y \
    && echo "conda activate dev" >> ~/.bashrc

# AlphaPose環境のセットアップ
RUN conda run -n alphapose pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121 \
    && conda run -n alphapose pip3 install cython

# Change ownership of conda directory
RUN chown -R vscode:vscode "${CONDA_DIR}"

# Switch back to non-root user
USER vscode
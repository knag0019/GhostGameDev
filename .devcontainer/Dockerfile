# CUDA 12.1 + cuDNN8 + Ubuntu 22.04
FROM nvidia/cuda:12.1.1-cudnn8-devel-ubuntu22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

# 1. システムパッケージのインストール
# v4l-utils: カメラデバッグ用
# libgl1-mesa-glx, x11-apps: GUI表示用
RUN apt-get update && apt-get install -y --no-install-recommends \
    sudo git curl ca-certificates wget \
    build-essential cmake pkg-config \
    python3 python3-venv python3-pip python3-dev \
    libgl1 libglib2.0-0 ffmpeg \
    v4l-utils \
    x11-apps \
 && rm -rf /var/lib/apt/lists/*

# 2. ユーザー作成 (vscode)
RUN groupadd --gid ${USER_GID} ${USERNAME} \
 && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
 && echo "${USERNAME} ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/${USERNAME} \
 && chmod 0440 /etc/sudoers.d/${USERNAME}

# 3. Python環境設定
ENV VENV_PATH=/opt/venv
RUN python3 -m venv ${VENV_PATH}
ENV PATH=${VENV_PATH}/bin:${PATH}

# フォルダ権限修正
RUN chown -R ${USERNAME}:${USERNAME} ${VENV_PATH}

# 4. ライブラリインストール
# pipアップグレード
RUN pip install --upgrade pip

# PyTorch (CUDA 12.1対応)
RUN pip install --index-url https://download.pytorch.org/whl/cu121 \
    torch torchvision torchaudio

# AI & 画像処理系ライブラリ
# streamdiffusion: 高速生成
# mediapipe: 人物検出
# opencv-python: 画像処理 & GUI表示
RUN pip install \
    streamdiffusion \
    diffusers \
    transformers \
    accelerate \
    mediapipe \
    opencv-python \
    numpy scipy matplotlib ipykernel jupyterlab tqdm einops

# 開発用ツール
RUN pip install black flake8 isort

# 5. 環境変数設定
ENV CUDA_HOME=/usr/local/cuda
ENV CUDACXX=/usr/local/cuda/bin/nvcc
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:${LD_LIBRARY_PATH}

# ユーザー切り替え
USER vscode
WORKDIR /workspaces

# コンテナ維持
CMD ["sleep", "infinity"]

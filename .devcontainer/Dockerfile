FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y build-essential git ruby wget gdb file flex bison bc cpio \
    qemu-system-arm qemu-system-aarch64 sed make binutils diffutils gcc g++ bash patch gzip bzip2 \
    perl tar unzip rsync file findutils libncurses5-dev \
    && apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Install CMake 3.25.1
RUN wget https://github.com/Kitware/CMake/releases/download/v3.25.1/cmake-3.25.1-linux-x86_64.sh \
    && bash cmake-3.25.1-linux-x86_64.sh --skip-license --prefix=/usr/local \
    && rm cmake-3.25.1-linux-x86_64.sh

ARG USERNAME
ENV USERNAME=${USERNAME}

ENV USER_HOME_DIR=/home/$USERNAME

# Install cross-compile toolchain
WORKDIR /usr/local/arm-cross-compiler/
ARG GCC_ARM_VERSION=13.3.rel1
RUN wget -O gcc-arm.tar.xz \
    https://developer.arm.com/-/media/Files/downloads/gnu/$GCC_ARM_VERSION/binrel/arm-gnu-toolchain-$GCC_ARM_VERSION-x86_64-aarch64-none-linux-gnu.tar.xz && \
    mkdir install && \
    tar x -C install -f gcc-arm.tar.xz && \
    rm -r gcc-arm.tar.xz

ENV PATH="${PATH}:/usr/local/arm-cross-compiler/install/arm-gnu-toolchain-$GCC_ARM_VERSION-x86_64-aarch64-none-linux-gnu/bin"

RUN  sed -i "/^# If not running interactively, don't do anything.*/i export PATH=\$PATH:$(find /usr/local/arm-cross-compiler/install -maxdepth 2 -type d -name bin)" \
            /root/.bashrc
RUN  sed -i "/^# If not running interactively, don't do anything.*/i export PATH=\$PATH:$(find /usr/local/arm-cross-compiler/install -maxdepth 2 -type d -name bin)" \
            $USER_HOME_DIR/.bashrc


USER $USERNAME

# Create the ${HOME}/.dl directory
RUN mkdir -p ${USER_HOME_DIR}/.dl

# Set the BR2_DL_DIR environment variable
ENV BR2_DL_DIR=${USER_HOME_DIR}/.dl

WORKDIR ${USER_HOME_DIR}
FROM ubuntu:focal

# Increase this to force a rebuild of the Docker image (in Cirrus, in particular).
ENV IMAGE_VERSION=1

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

CMD ["sh"]
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates gnupg2 less sudo \
 # clang-12
 && echo 'deb http://apt.llvm.org/focal/ llvm-toolchain-focal-12 main' > /etc/apt/sources.list.d/llvm.list \
 && curl -o - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - \
 && apt update \
 && apt install -y --no-install-recommends clang-12 \
 # Spicy build and test dependencies.
 && apt-get install -y --no-install-recommends git cmake ninja-build ccache bison flex g++ libfl-dev zlib1g-dev jq locales-all make python3 \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 # Install a recent CMake.
 && mkdir -p /opt/cmake \
 && curl -L https://github.com/Kitware/CMake/releases/download/v3.18.0/cmake-3.18.0-Linux-x86_64.tar.gz | tar xzvf - -C /opt/cmake --strip-components 1

ENV PATH="/opt/cmake/bin:${PATH}"
ENV LD_LIBRARY_PATH /usr/lib/llvm-12/lib/clang/12.0.1/lib/linux
ENV CC clang-12
ENV CXX clang++-12

WORKDIR /root

COPY Makefile .
COPY fuzz.cc .

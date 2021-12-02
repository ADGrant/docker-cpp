# Build and run:
#   docker build -t clion/ubuntu/cpp-env:1.0 -f Dockerfile.cpp-env-ubuntu .

FROM ubuntu:20.04

ENV CMAKE_VER_MAJOR 3
ENV CMAKE_VER_MINOR 17
ENV CMAKE_VER_PATCH 5

ENV CMAKE_VER "${CMAKE_VER_MAJOR}.${CMAKE_VER_MINOR}"
ENV CMAKE_VERSION "${CMAKE_VER}.${CMAKE_VER_PATCH}"

RUN DEBIAN_FRONTEND="noninteractive" apt-get update && apt-get -y install tzdata

RUN apt-get update \
  && apt-get install -y build-essential \
      gcc \
      g++ \
      gdb \
      clang \
      make \
      curl \
      ninja-build \
      autoconf \
      automake \
      locales-all \
      dos2unix \
      rsync \
      tar \
      python \
      python-dev \
  && apt-get clean

RUN mkdir -p /tmp/build && cd /tmp/build


RUN curl -sSL https://cmake.org/files/v${CMAKE_VER}/cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz > cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
  && tar -v -zxf cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
  && rm -f cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
  && cd cmake-${CMAKE_VERSION}-Linux-x86_64 \
  && cp -rp bin/* /usr/bin/ \
  && cp -rp share/* /usr/local/share/ \
  && cd / && rm -rf /tmp/build \
  && mkdir -p /tmp/build/json && mkdir -p /tmp/build/aws && cd /tmp/build
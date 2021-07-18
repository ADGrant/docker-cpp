FROM ubuntu:20.04 as AWS_CPP_SDK

ARG AWS_SDK_CPP_VER_MAJOR
ENV AWS_SDK_CPP_VER_MAJOR ${AWS_SDK_CPP_VER_MAJOR:-1}
ARG AWS_SDK_CPP_VER_MINOR
ENV AWS_SDK_CPP_VER_MINOR ${AWS_SDK_CPP_VER_MINOR:-3}
ARG AWS_SDK_CPP_VER_PATCH
ENV AWS_SDK_CPP_VER_PATCH ${AWS_SDK_CPP_VER_PATCH:-47}

ENV AWS_SDK_CPP_VERSION="1.8.160"

ENV CMAKE_VER_MAJOR 3
ENV CMAKE_VER_MINOR 17
ENV CMAKE_VER_PATCH 5

ENV CMAKE_VER "${CMAKE_VER_MAJOR}.${CMAKE_VER_MINOR}"
ENV CMAKE_VERSION "${CMAKE_VER}.${CMAKE_VER_PATCH}"

ARG ENABLE_UNITY_BUILD
ENV ENABLE_UNITY_BUILD "${ENABLE_UNITY_BUILD}:-OFF"

ENV DEBIAN_FRONTEND noninteractive

# These libs are required by the AWS C++ SDK Or Boost and should remain inside
# of any child container using this image as a base image.
ENV AWS_SDK_CPP_REQUIRED_LIBS \
	libssl-dev \
	libcurl4-openssl-dev \
	curl \
	libxml2 \
	libc-dev \
	libpulse-dev \
	libboost-all-dev \
	ca-certificates 

# These installs are required for compiling the AWS C++ SDK or Boost. It is probable
# that any child image will also need these to compile their projects. But as
# with USEFUL_TOOLS below, could be removed from the final release build image
# to save image space.
ENV AWS_SDK_CPP_BUILD_TOOLS \
	build-essential \
	autoconf \
	g++ \
	gcc \
	make 
	
# If you use this container as a base image then the following
# installs can be removed from the final image to save image space.
# I install them here as they are useful during development.
ENV AWS_SDK_CPP_USEFUL_TOOLS \
	apt-utils \
	wget \
	file \
	tcsh \
	unzip \
	git

RUN apt-get update \
	&& apt-get install -y \
	$AWS_SDK_CPP_BUILD_TOOLS \
	$AWS_SDK_CPP_REQUIRED_LIBS \
	$AWS_SDK_CPP_USEFUL_TOOLS \
	--no-install-recommends 

RUN mkdir -p /tmp/build && cd /tmp/build


RUN curl -sSL https://cmake.org/files/v${CMAKE_VER}/cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz > cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
	&& tar -v -zxf cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
	&& rm -f cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
	&& cd cmake-${CMAKE_VERSION}-Linux-x86_64 \
	&& cp -rp bin/* /usr/local/bin/ \
	&& cp -rp share/* /usr/local/share/ \
	&& cd / && rm -rf /tmp/build \
	&& mkdir -p /tmp/build/json && mkdir -p /tmp/build/aws && cd /tmp/build

RUN cd /tmp/build/json \
	&& git clone https://github.com/ADGrant/json.git \
	&& cmake json \
	&& make install

RUN cd /tmp/build/aws \
	&& curl -sSL https://github.com/aws/aws-sdk-cpp/archive/${AWS_SDK_CPP_VERSION}.zip > aws-sdk-cpp-${AWS_SDK_CPP_VERSION}.zip \
	&& unzip aws-sdk-cpp-${AWS_SDK_CPP_VERSION}.zip \
	&& rm -f aws-sdk-cpp-${AWS_SDK_CPP_VERSION}.zip \
	&& cmake \
		-DCMAKE_BUILD_TYPE=Release \
		-DENABLE_TESTING=OFF \
		-DAUTORUN_UNIT_TESTS=OFF \
		-DBUILD_SHARED_LIBS=OFF \
		-DENABLE_UNITY_BUILD=${ENABLE_UNITY_BUILD} \
		aws-sdk-cpp-${AWS_SDK_CPP_VERSION} \
	&& make \
	&& make install 
#	&& cd / \
#	&& rm -rf /tmp/build \
#	&& rm -rf /var/lib/apt/lists/* 
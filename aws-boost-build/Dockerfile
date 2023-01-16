FROM andrewdgrant/clion-ubuntu:1.2.0 as AWS_CPP_SDK

ARG AWS_SDK_CPP_VER_MAJOR
ENV AWS_SDK_CPP_VER_MAJOR ${AWS_SDK_CPP_VER_MAJOR:-1}
ARG AWS_SDK_CPP_VER_MINOR
ENV AWS_SDK_CPP_VER_MINOR ${AWS_SDK_CPP_VER_MINOR:-3}
ARG AWS_SDK_CPP_VER_PATCH
ENV AWS_SDK_CPP_VER_PATCH ${AWS_SDK_CPP_VER_PATCH:-47}

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
	libboost-all-dev \
	ca-certificates 
	
# If you use this container as a base image then the following
# installs can be removed from the final image to save image space.
# I install them here as they are useful during development.
ENV AWS_SDK_CPP_USEFUL_TOOLS \
	apt-utils \
	wget \
	file \
	unzip

RUN apt-get update \
	&& apt-get install -y \
	$AWS_SDK_CPP_BUILD_TOOLS \
	$AWS_SDK_CPP_REQUIRED_LIBS \
	$AWS_SDK_CPP_USEFUL_TOOLS \
	--no-install-recommends 

RUN mkdir -p /tmp/build && cd /tmp/build


RUN mkdir -p /tmp/build/json && mkdir -p /tmp/build/aws && cd /tmp/build \
	&& cd /tmp/build/json \
	&& git clone https://github.com/ADGrant/json.git \
	&& cmake json \
	&& make install

RUN cd /tmp/build/aws \
	&& git clone https://github.com/aws/aws-sdk-cpp.git \
	&& cd /tmp/build/aws/aws-sdk-cpp \
	&& git submodule update --init --recursive

RUN cd /tmp/build/aws \
	&& cmake \
		-DCMAKE_BUILD_TYPE=Release \
		-DENABLE_TESTING=OFF \
		-DAUTORUN_UNIT_TESTS=OFF \
		-DBUILD_SHARED_LIBS=ON \
		-DENABLE_UNITY_BUILD=${ENABLE_UNITY_BUILD} \
		aws-sdk-cpp

RUN cd /tmp/build/aws \
	&& make 
RUN cd /tmp/build/aws \
	&& make install 

RUN cd / \
	&& rm -rf /tmp/build \
	&& rm -rf /var/lib/apt/lists/* 

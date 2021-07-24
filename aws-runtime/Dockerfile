FROM ubuntu:20.04 as AWS_CPP_SDK

# These libs are required by the AWS C++ SDK Or Boost and should remain inside
# of any child container using this image as a base image.
ENV AWS_SDK_CPP_REQUIRED_LIBS \
	libssl-dev \
	libcurl4-openssl-dev \
	curl \
	libxml2 \
	libc-dev \
	libpulse-dev \
	ca-certificates 


RUN apt-get update \
	&& apt-get install -y \
	$AWS_SDK_CPP_REQUIRED_LIBS \
	--no-install-recommends 


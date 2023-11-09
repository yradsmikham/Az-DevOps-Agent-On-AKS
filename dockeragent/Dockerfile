# Azure DevOps Pipeline Agent : 11-07-2023

FROM --platform=linux/amd64 ubuntu:22.04

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update
RUN apt-get dist-upgrade 

RUN apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        jq \
        git \
        iputils-ping \
	libcurl4 \ 
        libunwind8 \
        netcat \
        zip \
        unzip \
        wget \
        apt-transport-https \
	gnupg \
        gnupg-agent \
        apt-utils \
        software-properties-common

WORKDIR /azp

COPY ./start.sh .
RUN chmod +x start.sh

CMD ["./start.sh"]

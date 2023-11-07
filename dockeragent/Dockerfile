# Azure DevOps Pipeline Agent : 11-07-2023
#
# Description: This dockerfile builds an Azure DevOps self-hosted agent. It has been modified to include the following build tools
#   - docker-ce-cli
#   - Aqua Trivy, an open source Container image scanner (ID09272019)
#   - Azure CLI
#   - kubectl
#   - helm
#

FROM --platform=linux/amd64 ubuntu:22.04

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update
RUN apt-get dist-upgrade 
RUN apt-get install -y software-properties-common
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
        apt-utils

RUN wget http://security.ubuntu.com/ubuntu/pool/main/i/icu/libicu66_66.1-2ubuntu2_amd64.deb

# Set up Docker's apt repository
RUN apt-get install ca-certificates curl gnupg
RUN install -m 0755 -d /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
RUN chmod a+r /etc/apt/keyrings/docker.gpg

RUN echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

# Setup Aqua Trivy public key and repo. - ID09272019
RUN wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | apt-key add -
RUN echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main | tee -a /etc/apt/sources.list.d/trivy.list

# Update Ubuntu and install docker CLI, Aqua Trivy
RUN apt-get update && apt-get install docker-ce-cli trivy

# Install all build tools in /build-tools
WORKDIR /build-tools

# Install Azure CLI - ID09262019
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# Install kubectl (latest)
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl \
&& chmod +x ./kubectl \
&& mv ./kubectl /usr/local/bin/kubectl

# Install helm v3 (latest)
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
&& chmod 700 get_helm.sh \
&& ./get_helm.sh

WORKDIR /azp

COPY ./start.sh .
RUN chmod +x start.sh

CMD ["./start.sh"]

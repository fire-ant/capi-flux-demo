# Note: You can use any Debian/Ubuntu based image you want. 
FROM mcr.microsoft.com/vscode/devcontainers/base:0-buster

# Options
ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="false"
ARG ENABLE_NONROOT_DOCKER="true"
ARG SOURCE_SOCKET=/var/run/docker-host.sock
ARG TARGET_SOCKET=/var/run/docker.sock
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
#  Customisations
ARG KIND_VERSION=0.9.0
ARG CLUSTERCTL_VERSION=0.3.14

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
    # Use Docker script from script library to set things up
    && /bin/bash /tmp/library-scripts/docker-debian.sh "${ENABLE_NONROOT_DOCKER}" "${SOURCE_SOCKET}" "${TARGET_SOCKET}" "${USERNAME}" \
    # Clean up
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts/

# Install kubectl
RUN curl -sSL -o /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl \
    && chmod +x /usr/local/bin/kubectl

# Install Helm
RUN curl -s https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash -

# Script copies localhost's ~/.kube/config file into the container and swaps out 
# localhost for host.docker.internal on bash/zsh start to keep them in sync.
COPY copy-kube-config.sh /usr/local/share/
RUN chown ${USERNAME}:root /usr/local/share/copy-kube-config.sh \
    && echo "source /usr/local/share/copy-kube-config.sh" | tee -a /root/.bashrc /root/.zshrc /home/${USERNAME}/.bashrc >> /home/${USERNAME}/.zshrc

# Install GPG
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends gnupg

# install kind
RUN curl -sSL -o /usr/local/bin/kind "https://kind.sigs.k8s.io/dl/v$KIND_VERSION/kind-linux-amd64" \
    && chmod +x /usr/local/bin/kind

# install flux cli
RUN curl -s https://raw.githubusercontent.com/fluxcd/flux2/main/install/flux.sh | sudo bash

#  install clusterctl
RUN curl -sSL -o /usr/local/bin/clusterctl "https://github.com/kubernetes-sigs/cluster-api/releases/download/v${CLUSTERCTL_VERSION}/clusterctl-linux-amd64" \
    && chmod +x /usr/local/bin/clusterctl

    https://gitea.com/api/v1/user/repos?access_token=TOKEN
    https://gitlab.com/api/v3/projects?private_token=<your token>
    https://api.github.com/user/repos
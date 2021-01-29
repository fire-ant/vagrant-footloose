# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.154.2/containers/ruby/.devcontainer/base.Dockerfile

# [Choice] Ruby version: 2, 2.7, 2.6, 2.5
ARG VARIANT="2.7"
FROM mcr.microsoft.com/vscode/devcontainers/ruby:0-${VARIANT}

# pin footloose version
ARG FOOTLOOSE="0.6.3"
#  pin vagrant version
ARG VAGRANT="2.2.14"
# pin vagrant-managed-servers
ARG V_SSH_PLUG="0.8.0"
#  pin ansible
ARG ANSIBLE_V="2.10.0"
# pin cinc-auditor
ARG CINC_V="4.26.4"
# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"
# [Option] Enable non-root Docker access in container
ARG ENABLE_NONROOT_DOCKER="true"
# [Option] Use the OSS Moby CLI instead of the licensed Docker CLI
ARG USE_MOBY="true"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your
# own dependencies. A user of "automatic" attempts to reuse an user ID if one already exists.
ARG USERNAME=automatic
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
    # Use Docker script from script library to set things up
    && /bin/bash /tmp/library-scripts/docker-debian.sh "${ENABLE_NONROOT_DOCKER}" "/var/run/docker-host.sock" "/var/run/docker.sock" "${USERNAME}" "${USE_MOBY}" \
    # Clean up
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts/
# install bsdtar (for vagrant) and ansible 
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-add-repository --yes --update ppa:ansible/ansible \
    && apt-get install -y bsdtar software-properties-common ansible="${ANSIBLE_V}"

# [Optional] Uncomment this line to install additional gems.
RUN rm /usr/local/lib/ruby/gems/2.7.0/specifications/default/bundler-2.1.4.gemspec \
  && rm -rf  /usr/local/lib/ruby/gems/2.7.0/gems/bundler-2.1.4 \
  && gem uninstall bundler:2.1.4 \
  && gem install bundler:2.2.4

# Install footloose
RUN curl -Lo /usr/local/bin/footloose "https://github.com/weaveworks/footloose/releases/download/${FOOTLOOSE}/footloose-${FOOTLOOSE}-linux-x86_64" \
  && chmod +x /usr/local/bin/footloose

# install vagrant
RUN curl -O "https://releases.hashicorp.com/vagrant/${VAGRANT}/vagrant_${VAGRANT}_x86_64.deb" \
  && apt install ./vagrant_${VAGRANT}_x86_64.deb

# install vagrant managed ssh plugin
RUN vagrant plugin install vagrant-managed-servers --plugin-version "${V_SSH_PLUG}"

# install cinc auditor
RUN curl -L https://omnitruck.cinc.sh/install.sh | sudo bash -s -- -P cinc-auditor -v "${CINC_V}"

ENTRYPOINT [ "/usr/local/share/docker-init.sh" ]
CMD [ "sleep", "infinity" ]
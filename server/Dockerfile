FROM quay.io/vektorlab/ctop:0.7.1 as ctop
FROM lsiobase/ubuntu:bionic

# set version label
ARG BUILD_DATE
ARG VERSION
ARG CODE_RELEASE

LABEL build_version="Linuxserver.io version:- ${VERSION} Build-date:- ${BUILD_DATE}"
LABEL image="frank30941/code-server"
LABEL maintainer="frank30941 <frank30941@gmail.com>"
LABEL github="https://github.com/frank30941"
LABEL registry="https://hub.docker.com/u/frank30941"

# environment settings
ENV HOME="/config"

RUN \
 echo "**** install node repo ****" && \
 apt-get update && \
 apt-get install -y \
	gnupg && \
 curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - && \
 echo 'deb https://deb.nodesource.com/node_12.x bionic main' \
	> /etc/apt/sources.list.d/nodesource.list && \
 curl -s https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
 echo 'deb https://dl.yarnpkg.com/debian/ stable main' \
	> /etc/apt/sources.list.d/yarn.list && \
 echo "**** install build dependencies ****" && \
 apt-get update && \
 apt-get install -y \
	build-essential \
	libx11-dev \
	libxkbfile-dev \
	libsecret-1-dev \
	pkg-config && \
 echo "**** install runtime dependencies ****" && \
 apt-get install -y \
	git \
	jq \
	net-tools \
	nodejs \
	sudo \
	yarn && \
 echo "**** install code-server ****" && \
 if [ -z ${CODE_RELEASE+x} ]; then \
	CODE_RELEASE=$(curl -sX GET "https://api.github.com/repos/cdr/code-server/releases/latest" | awk '/tag_name/{print $4;exit}' FS='[""]'); \
 fi && \
 CODE_VERSION=$(echo "$CODE_RELEASE" | awk '{print substr($1,1); }') && \
 yarn --production global add code-server@"v$CODE_VERSION" && \
 yarn cache clean && \
 ln -s /node_modules/.bin/code-server /usr/bin/code-server && \
 echo "**** clean up ****" && \
 apt-get purge --auto-remove -y \
	build-essential \
	libx11-dev \
	libxkbfile-dev \
	libsecret-1-dev \
	pkg-config && \
 apt-get clean && \
 rm -rf \
	/tmp/* \
	/var/lib/apt/lists/* \
	/var/tmp/*

# add local files
COPY /root /

# Install dependencies
RUN set -ex; \
    apt-get update && apt-get install -y --no-install-recommends \
    bash \
    ca-certificates \
    curl \
    dumb-init \
    gcc \
    gnupg \
    htop \
    less \
    vim \
    openssh-client \
    tzdata \
    util-linux \
    zsh; \
    \
    rm -rf /var/lib/apt/lists/*

COPY --from=ctop /ctop /usr/bin/ctop-bin

RUN \
 # Oh My Zsh!
 sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" ; \
 git clone https://github.com/zsh-users/zsh-autosuggestions.git ${HOME}/.oh-my-zsh/plugins/zsh-autosuggestions ; \
 sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="ys"/g' ${HOME}/.zshrc ; \
 sed -i "s/(git)/(git zsh-autosuggestions)/g" ${HOME}/.zshrc ; \
 cp -a ${HOME}/.oh-my-zsh /root/ ; \
 cp -a ${HOME}/.zshrc /root/ ; \
 \
 # ctop
 echo '#!/bin/bash' >> /usr/bin/ctop ; \
 echo 'sudo ctop-bin' >> /usr/bin/ctop ; \
 chmod +x /usr/bin/ctop ; \
 \
 # gpg purposes
 echo 'export GPG_TTY=$(tty)' >> /root/.zshrc ; \
 echo 'export GPG_TTY=$(tty)' >> ${HOME}/.zshrc ;

# ports and volumes
EXPOSE 8080

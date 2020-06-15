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
 echo "**** install dependencies ****" && \
 apt-get update && \
 apt-get install -y \
	git \
	jq \
	vim \
	net-tools \
	ca-certificates \
	iputils-ping \
	gnupg \
	htop \
	less \
	openssh-client \
	tzdata \
	util-linux \
	zsh \
	dumb-init \
	bash \
	sudo;
RUN \
 echo "**** install node repo ****" && \
 curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - && \
 echo 'deb https://deb.nodesource.com/node_12.x bionic main' \
	> /etc/apt/sources.list.d/nodesource.list && \
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
	nodejs && \
 echo "**** install code-server ****" && \
 if [ -z ${CODE_RELEASE+x} ]; then \
	CODE_RELEASE=$(curl -sX GET "https://api.github.com/repos/cdr/code-server/releases/latest" \
	| awk '/tag_name/{print $4;exit}' FS='[""]'); \
 fi && \
 CODE_VERSION=$(echo "$CODE_RELEASE" | awk '{print substr($1,2); }') && \
 npm install --unsafe-perm -g code-server@"$CODE_VERSION" && \
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

# add local files
COPY /root /

# ports and volumes
EXPOSE 8080
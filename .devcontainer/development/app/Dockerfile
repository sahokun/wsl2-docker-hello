# python official 3.12 bullseye
# https://github.com/docker-library/python/tree/16e96e15aa5c7fec8ca41436159d62b22a733daf/3.12/bookworm

FROM python:3.12-bookworm

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Japanese: https://www.server-world.info/query?os=Debian_11&p=japanese
ENV TZ=Asia/Tokyo
RUN apt-get update && apt-get install --no-install-recommends -y \
	locales \
	&& apt-get clean && rm -rf /var/lib/apt/lists/*
RUN locale-gen ja_JP.UTF-8
ENV LANG=ja_JP.UTF-8
ENV LANGUAGE=ja_JP:ja
ENV LC_ALL=ja_JP.UTF-8
RUN localedef -f UTF-8 -i ja_JP ja_JP.utf8

# 非特権ユーザー
RUN apt-get update && apt-get install --no-install-recommends -y \
	sudo \
	&& apt-get clean && rm -rf /var/lib/apt/lists/*

ARG HOST_UID
ARG HOST_GID
RUN groupadd -g ${HOST_GID} user \
  && useradd -d /home/user -m -s /bin/bash -u ${HOST_UID} -g ${HOST_GID} user \
  && echo 'user     ALL=NOPASSWD: ALL' >> /etc/sudoers

# additional packages
RUN apt-get update && apt-get install --no-install-recommends -y \
  vim less psmisc zip unzip git \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

# vim
COPY ./.vimrc /etc/skel/
RUN cp /etc/skel/.vimrc /root/. \
  && cp /root/.vimrc /home/user/. \
  && chown user:user /home/user/.vimrc
# install direnv command.
# - https://github.com/direnv/direnv
# - https://github.com/direnv/direnv/releases
ARG DEV_DIRENV_VERSION=v2.30.3
RUN wget -O direnv https://github.com/direnv/direnv/releases/download/$DEV_DIRENV_VERSION/direnv.linux-amd64 && \
    mv direnv /usr/local/bin/ && \
    chmod +x /usr/local/bin/direnv && \
    echo 'eval "$(direnv hook bash)"' >> ~/.bashrc

# pip更新
RUN pip install --upgrade pip
RUN python -m pip install --upgrade pip

# HOMEはVSCodeでRemoteDevelopmentするのに必要
ENV HOME=/home/user
ENV HOST 0.0.0.0
EXPOSE 3000

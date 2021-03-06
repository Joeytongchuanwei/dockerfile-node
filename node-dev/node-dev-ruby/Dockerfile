#
# Node-dev-ruby Dockerfile
# 安装ruby环境，及sass和compass
#
# https://github.com/ibbd/dockerfile-node/node-dev/node-dev-ruby
#
# sudo docker build -t ibbd/node-dev-ruby ./
#

# Pull base image.
FROM ibbd/node-dev

MAINTAINER Alex Cai "cyy0523xc@gmail.com"


###########################################################
######### BEGIN Ruby环境 ##################################
######### Copy From: hub.docker.com/_/ruby/  2.2-slim  ####
######### 1. 将RubyGems的镜像替换为淘宝的              ####
######### 2. 安装sass                                  ####
######### 3. 安装compass                               ####
###########################################################
# && gem sources --remove https://rubygems.org/ \
# && gem sources -a https://ruby.taobao.org/ \
# && gem install sass \
# && gem install compass \

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        bzip2 \
        ca-certificates \
        curl \
        libffi-dev \
        libgdbm3 \
        libssl-dev \
        libyaml-dev \
        procps \
        zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

ENV RUBY_MAJOR 2.3
ENV RUBY_VERSION 2.3.0
ENV RUBY_DOWNLOAD_SHA256 ba5ba60e5f1aa21b4ef8e9bf35b9ddb57286cb546aac4b5a28c71f459467e507
ENV RUBYGEMS_VERSION 2.5.1

# skip installing gem documentation
RUN echo 'install: --no-document\nupdate: --no-document' >> "$HOME/.gemrc"

# some of ruby's build scripts are written in ruby
# we purge this later to make sure our final image uses what we just built
RUN buildDeps=' \
        autoconf \
        bison \
        gcc \
        libbz2-dev \
        libgdbm-dev \
        libglib2.0-dev \
        libncurses-dev \
        libreadline-dev \
        libxml2-dev \
        libxslt-dev \
        make \
        ruby \
    ' \
    && set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends $buildDeps \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /usr/src/ruby \
    && curl -fSL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
    && echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
    && tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
    && rm ruby.tar.gz \
    && cd /usr/src/ruby \
    && autoconf \
    && ./configure --disable-install-doc \
    && make -j"$(nproc)" \
    && make install \
    && gem install sass \
    && gem install compass \
    && gem sources --remove https://rubygems.org/ \
    && gem sources -a https://ruby.taobao.org/ \
    && gem update --system $RUBYGEMS_VERSION \
    && rm -r /usr/src/ruby \
    && apt-get purge -y --auto-remove $buildDeps \
    && rm -rf /var/lib/apt/lists/*

# install things globally, for great justice
ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH

ENV BUNDLER_VERSION 1.11.2

RUN gem install bundler --version "$BUNDLER_VERSION" \
    && bundle config --global path "$GEM_HOME" \
    && bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

###########################################################
######### END Ruby环境 ####################################
###########################################################



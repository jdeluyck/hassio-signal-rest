ARG BUILD_FROM

#FROM golang:1.13-buster
FROM ${BUILD_FROM}

ARG SIGNAL_CLI_VERSION
ENV GIN_MODE=release

# Fix for error in jre
# see https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=863199
RUN mkdir -p /usr/share/man/man1

RUN apt-get update \
	&& apt-get install -y --no-install-recommends wget default-jre software-properties-common git locales golang \
	&& rm -rf /var/lib/apt/lists/* 

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8 

# https://github.com/AsamK/signal-cli/issues/259 is not yet in a release, so we need to check out the repository

RUN cd /tmp/ \
  && git clone https://github.com/bbernhard/signal-cli-rest-api 

RUN cd /tmp/ \
  && git clone https://github.com/AsamK/signal-cli.git signal-cli-${SIGNAL_CLI_VERSION} \
  && cd signal-cli-${SIGNAL_CLI_VERSION} \
  && ./gradlew build \
  && ./gradlew installDist \
  && ln -s /tmp/signal-cli-${SIGNAL_CLI_VERSION}/build/install/signal-cli/bin/signal-cli /usr/bin/signal-cli

RUN mkdir -p /signal-cli-config/
#RUN mkdir -p /home/.local/share/signal-cli
RUN ln -s /home/.local/share/signal-cli /config
RUN cd /tmp/signal-cli-rest-api/src && go get -d ./... && go build main.go

ENV PATH /tmp/signal-cli-rest-api/src:/usr/bin/signal-cli-${SIGNAL_CLI_VERSION}/bin/:$PATH

ENTRYPOINT ["main"]

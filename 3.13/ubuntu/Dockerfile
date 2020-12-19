FROM ubuntu:20.04
LABEL maintainer="dicehub.com"

ENV PROTOC_VERSION="3.14.0"
ENV WORKDIR="/data"

RUN \
  set -ex \
  && apt-get update \
  && apt-get -qy install \
      unzip \
      wget \
  && apt -y upgrade \
  \
  # cleanup
  && apt-get clean \
  && cd /var/lib/apt/lists && rm -fr *Release* *Sources* *Packages* \
  && truncate -s 0 /var/log/*log

RUN \
  set -ex \
  # add proto group and user
  && addgroup -gid 1000 proto \
  && adduser -u 1000  -gid 1000 --shell /bin/sh --disabled-password proto \
  && mkdir ${WORKDIR} \
  && chown proto:proto ${WORKDIR} \
  \
  # Get and install protoc
  && wget https://github.com/protocolbuffers/protobuf/releases/download/v${PROTOC_VERSION}/protoc-${PROTOC_VERSION}-linux-x86_64.zip \
  && unzip *.zip \
  && rm *.zip \
  \
  && chmod 755 /bin/protoc \
  && protoc --version

WORKDIR ${WORKDIR}
USER proto
ENTRYPOINT [ "/bin/protoc" ]

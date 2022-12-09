###########
# Builder #
###########

FROM ubuntu:20.04 as builder
LABEL maintainer="dicehub.com"

ENV PROTOC_VERSION="3.21.11"

RUN \
  set -ex \
  && apt-get update \
  && apt-get -qy install \
      autoconf \
      automake \
      curl \
      g++ \
      git \
      libtool \
      make \
      unzip \
      wget \
  && apt -y upgrade \
  \
  # cleanup
  && apt-get clean \
  && cd /var/lib/apt/lists && rm -fr *Release* *Sources* *Packages* \
  && truncate -s 0 /var/log/*log

# Patch for es6 from 
# https://gist.github.com/gunters63/3c8798de46fa469e1ce06966df017ca8#file-gistfile1-txt
COPY es6.patch /tmp/

RUN \
    set -ex \
    \
    # Get the source code and all submodules
    && git clone https://github.com/protocolbuffers/protobuf.git \
    && cd protobuf \
    && git checkout tags/v${PROTOC_VERSION} \
    && git submodule update --init --recursive \
    && ./autogen.sh \
    \
    # Apply patch
    && git apply /tmp/es6.patch \
    \
    # Configure and install
    && ./configure --prefix=/ --disable-shared \
    && make -j4 \
    # && make check \
    && make install \
    # && ldconfig \
    \
    && protoc --version

# CMD tail -f /dev/null

##################
# Main container #
##################

FROM ubuntu:20.04
LABEL maintainer="dicehub.com"

ENV WORKDIR="/data"

RUN \
  set -ex \
  # add proto group and user
  && addgroup -gid 1000 proto \
  && adduser -u 1000  -gid 1000 --shell /bin/sh --disabled-password proto \
  && mkdir ${WORKDIR} \
  && chown proto:proto ${WORKDIR}

COPY --from=builder /bin/protoc /bin/

RUN protoc --version

WORKDIR ${WORKDIR}
USER proto
ENTRYPOINT [ "/bin/protoc" ]
FROM alpine:3.10.3
LABEL maintainer="dicehub.com"

ENV VERSION="3.10.1"
ENV WORKDIR="/data"

RUN \
  set -ex \
  # add proto group and user
  && addgroup -g 1000 proto \
  && adduser -u 1000 -G proto -s /bin/sh -D proto \
  && mkdir ${WORKDIR} \
  && chown proto:proto ${WORKDIR} \
  \
  # Fix known issue for alpine (https://github.com/bazelbuild/rules_closure/issues/228#issuecomment-342658456)
  && wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub \
  && wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.30-r0/glibc-2.30-r0.apk \
  && apk add glibc-2.30-r0.apk \
  \
  # Get and install protoc
  && wget https://github.com/protocolbuffers/protobuf/releases/download/v${VERSION}/protoc-${VERSION}-linux-x86_64.zip \
  && unzip *.zip \
  && rm *.zip

WORKDIR ${WORKDIR}
USER proto
ENTRYPOINT [ "/bin/protoc" ]

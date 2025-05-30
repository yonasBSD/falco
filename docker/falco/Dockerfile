FROM cgr.dev/chainguard/wolfi-base

LABEL org.opencontainers.image.authors='The Falco Authors https://falco.org' \
      org.opencontainers.image.url='https://falco.org' \
      org.opencontainers.image.source='https://github.com/falcosecurity/falco' \
      org.opencontainers.image.vendor='Falco Organization' \
      org.opencontainers.image.licenses='Apache-2.0' \
      maintainer="cncf-falco-dev@lists.cncf.io"

LABEL usage="docker run -i -t --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /proc:/host/proc:ro  -v /etc:/host/etc:ro falcosecurity/falco:latest"
# NOTE: for the "least privileged" use case, please refer to the official documentation

ARG FALCO_VERSION
ARG VERSION_BUCKET=bin

ENV FALCO_VERSION=${FALCO_VERSION}
ENV VERSION_BUCKET=${VERSION_BUCKET}
ENV HOST_ROOT /host
ENV HOME /root

RUN apk update && apk add curl ca-certificates jq libstdc++

WORKDIR /

RUN FALCO_VERSION_URLENCODED=$(echo -n ${FALCO_VERSION}|jq -sRr @uri) && \
    curl -L -o falco.tar.gz \
    https://download.falco.org/packages/${VERSION_BUCKET}/$(uname -m)/falco-${FALCO_VERSION_URLENCODED}-$(uname -m).tar.gz && \
    tar -xvf falco.tar.gz && \
    rm -f falco.tar.gz && \
    mv falco-${FALCO_VERSION}-$(uname -m) falco && \
    rm -rf /falco/usr/src/falco-* && \
    cp -r /falco/* / && \
    rm -rf /falco

# Change the falco config within the container to enable ISO 8601 output.
ADD ./config/falco.iso8601_timeformat.yaml /etc/falco/config.d/

# Falcoctl is not included here.
RUN rm -rf /usr/bin/falcoctl /etc/falcoctl/

CMD ["/usr/bin/falco"]

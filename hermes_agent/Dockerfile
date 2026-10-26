ARG BUILD_VERSION=dev
ARG BUILD_ARCH=amd64
FROM nousresearch/hermes-agent:stable

RUN python3 -c "from pm.install import uv_launcher; import subprocess; uv=uv_launcher('uv'); subprocess.run([uv, 'pip', 'install', 'pyyaml'], check=True)"

ARG BUILD_VERSION
ARG BUILD_ARCH

RUN apt-get update \
    && apt-get install -y --no-install-recommends curl nginx \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /run/nginx

ARG TARGETARCH
RUN ARCH=$(echo ${TARGETARCH:-$(dpkg --print-architecture)} | sed 's|arm64|aarch64|;s|amd64|x86_64|') \
    && curl -fsSL "https://github.com/tsl0922/ttyd/releases/download/1.7.7/ttyd.${ARCH}" -o /usr/local/bin/ttyd \
    && chmod +x /usr/local/bin/ttyd

LABEL \
  io.hass.version="${BUILD_VERSION}" \
  io.hass.type="app" \
  io.hass.arch="${BUILD_ARCH}"

ENV HERMES_HOME=/data

COPY ingress.conf /etc/nginx/nginx.conf
COPY landing.html /usr/share/nginx/html/index.html
COPY addon-run.sh /addon-run.sh
COPY run.sh /etc/cont-init.d/01-addon-setup.sh
RUN chmod a+x /etc/cont-init.d/01-addon-setup.sh /addon-run.sh

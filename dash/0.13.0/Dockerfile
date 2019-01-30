FROM debian:stretch-slim

RUN groupadd -r bitcoin && useradd -r -m -g bitcoin bitcoin

RUN set -ex \
	&& apt-get update \
	&& apt-get install -qq --no-install-recommends ca-certificates dirmngr gosu gpg wget \
	&& rm -rf /var/lib/apt/lists/*

ENV DASH_VERSION 0.13.0.0
ENV DASH_URL https://github.com/dashpay/dash/releases/download/v0.13.0.0/dashcore-0.13.0.0-x86_64-linux-gnu.tar.gz
ENV DASH_SHA256 99b4309c7f53b2a93d4b60a45885000b88947af2f329e24ca757ff8cf882ab18
ENV DASH_ASC_URL https://github.com/dashpay/dash/releases/download/v0.13.0.0/SHA256SUMS.asc
ENV DASH_PGP_KEY 63a96b406102e091

# install dash binaries
RUN set -ex \
	&& cd /tmp \
	&& wget -qO dash.tar.gz "$DASH_URL" \
	&& echo "$DASH_SHA256 dash.tar.gz" | sha256sum -c - \
	&& gpg --keyserver keyserver.ubuntu.com --recv-keys "$DASH_PGP_KEY" \
	&& wget -qO dash.asc "$DASH_ASC_URL" \
	&& gpg --verify dash.asc \
	&& tar -xzvf dash.tar.gz -C /usr/local --strip-components=1 --exclude=*-qt \
	&& rm -rf /tmp/*

# create data directory
ENV BITCOIN_DATA /data
RUN mkdir "$BITCOIN_DATA" \
	&& chown -R bitcoin:bitcoin "$BITCOIN_DATA" \
	&& ln -sfn "$BITCOIN_DATA" /home/bitcoin/.dashcore \
	&& chown -h bitcoin:bitcoin /home/bitcoin/.dashcore
VOLUME /data

COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 9998 9999 19998 19999
CMD ["dashd"]

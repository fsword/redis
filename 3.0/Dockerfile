FROM debian:wheezy

RUN apt-get update && apt-get install -y --no-install-recommends \
		ca-certificates \
		curl \
	&& rm -rf /var/lib/apt/lists/*

ENV REDIS_VERSION 3.0.4
ENV REDIS_DOWNLOAD_URL http://download.redis.io/releases/redis-3.0.4.tar.gz
ENV REDIS_DOWNLOAD_SHA1 cccc58b2b8643930840870f17280fcae57ed7675

# for redis-sentinel see: http://redis.io/topics/sentinel
RUN buildDeps='gcc libc6-dev make' \
	&& set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
	&& rm -rf /var/lib/apt/lists/* \
	&& mkdir -p /usr/src/redis \
	&& curl -sSL "$REDIS_DOWNLOAD_URL" -o redis.tar.gz \
	&& echo "$REDIS_DOWNLOAD_SHA1 *redis.tar.gz" | sha1sum -c - \
	&& tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
	&& rm redis.tar.gz \
	&& make -C /usr/src/redis \
	&& make -C /usr/src/redis install \
	&& rm -r /usr/src/redis \
	&& apt-get purge -y --auto-remove $buildDeps

RUN mkdir /data
VOLUME /data
WORKDIR /data

COPY docker-entrypoint.sh /entrypoint.sh
COPY redis.conf /etc/redis.conf
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 6379
CMD [ "redis-server /etc/redis.conf" ]

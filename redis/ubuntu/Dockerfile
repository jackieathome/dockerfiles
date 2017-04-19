FROM ubuntu:14.04

MAINTAINER Jackie "www.jackieathome.net"

# Install Redis.
RUN \
    BUILD_ROOT=/tmp/redis \
    && cp /etc/apt/sources.list /etc/apt/sources.list.bak \
    && sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list \
    && apt-get update \
    && apt-get -y install make gcc g++ wget \
    && mkdir -p $BUILD_ROOT \
    && cd $BUILD_ROOT \
    && wget http://download.redis.io/redis-stable.tar.gz \
    && tar xvzf redis-stable.tar.gz \
    && cd redis-stable \
    && make \
    && make install \
    && mkdir -p /etc/redis \
    && cp -f *.conf /etc/redis \
    && cp -f src/redis-sentinel /usr/local/bin \
    && sed -i 's/^\(bind .*\)$/# \1/' /etc/redis/redis.conf \
    && sed -i 's/^\(daemonize .*\)$/# \1/' /etc/redis/redis.conf \
    && sed -i 's/^\(dir .*\)$/# \1\ndir \/data/' /etc/redis/redis.conf \
    && sed -i 's/^\(logfile .*\)$/# \1/' /etc/redis/redis.conf \
    && echo "" >> /etc/redis/redis.conf \
    && echo "include /data/redis-customized.conf" >> /etc/redis/redis.conf \
    && rm -rf $BUILD_ROOT \
    && apt-get -y remove make gcc g++ wget \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/* \
    && mv /etc/apt/sources.list.bak /etc/apt/sources.list \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/*

EXPOSE 6379
VOLUME ["/data"]
WORKDIR /data
CMD ["redis-server", "/etc/redis/redis.conf"]


FROM centos

MAINTAINER Jackie "www.jackieathome.net"

# Install Redis.
RUN \
    BUILD_ROOT=/tmp/redis \
    && yum -y install curl \
    && mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
    && curl http://mirrors.163.com/.help/CentOS7-Base-163.repo -o /etc/yum.repos.d/CentOS7-Base-163.repo \ 
    && yum clean all \
    && yum makecache \
    && yum -y install \
        wget \
        gcc \
        gcc-c++ \
        make \
        perl \
        zip \
        unzip \
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
    && yum -y remove \
        wget \
        gcc \
        gcc-c++ \
        make \
        perl \
        zip \
        unzip \
    && yum clean all \
    && rm -f /etc/yum.repos.d/CentOS7-Base-163.repo \
    && mv /etc/yum.repos.d/CentOS-Base.repo.backup /etc/yum.repos.d/CentOS-Base.repo \
    && yum clean all

EXPOSE 6379
VOLUME ["/data"]
WORKDIR /data
CMD ["redis-server", "/etc/redis/redis.conf"]


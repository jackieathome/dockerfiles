FROM ubuntu:14.04

MAINTAINER Jackie "www.jackieathome.net"

# Docker Build Arguments
ARG RESTY_VERSION="1.11.2.2"
ARG RESTY_LUAROCKS_VERSION="2.3.0"
ARG RESTY_OPENSSL_VERSION="1.0.2k"
ARG RESTY_PCRE_VERSION="8.40"
ARG RESTY_J="1"
#ARG MODULE_PAGESPEED_VERSION="1.12.34.2"
ARG MODULE_PAGESPEED_VERSION="1.8.31.4"

RUN \
    cp /etc/apt/sources.list /etc/apt/sources.list.bak \
    && sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list \
    && apt-get update \
    && apt-get -y install \
        build-essential \
        ca-certificates \
        curl \
        libgd-dev \
        libgeoip-dev \
        libncurses5-dev \
        libperl-dev \
        libreadline-dev \
        libxslt1-dev \
        make \
        perl \
        unzip \
        zlib1g-dev \
        wget \
    && BUILD_ROOT="/tmp/openresty" \
    && mkdir -p ${BUILD_ROOT} \
    && cd ${BUILD_ROOT} \
    && wget https://github.com/pagespeed/ngx_pagespeed/archive/v${MODULE_PAGESPEED_VERSION}-beta.zip \
    && unzip v${MODULE_PAGESPEED_VERSION}-beta.zip \
    && cd ngx_pagespeed-${MODULE_PAGESPEED_VERSION}-beta \
    && wget https://dl.google.com/dl/page-speed/psol/${MODULE_PAGESPEED_VERSION}.tar.gz \
    && tar vxfz ${MODULE_PAGESPEED_VERSION}.tar.gz \
    && cd ${BUILD_ROOT} \
    && curl -fSL https://www.openssl.org/source/openssl-${RESTY_OPENSSL_VERSION}.tar.gz -o openssl-${RESTY_OPENSSL_VERSION}.tar.gz \
    && tar xzf openssl-${RESTY_OPENSSL_VERSION}.tar.gz \
    && curl -fSL https://ftp.pcre.org/pub/pcre/pcre-${RESTY_PCRE_VERSION}.tar.gz -o pcre-${RESTY_PCRE_VERSION}.tar.gz \
    && tar xzf pcre-${RESTY_PCRE_VERSION}.tar.gz \
    && curl -fSL https://openresty.org/download/openresty-${RESTY_VERSION}.tar.gz -o openresty-${RESTY_VERSION}.tar.gz \
    && tar xzf openresty-${RESTY_VERSION}.tar.gz  \
    && BUILD_CONFIG="\
        --with-file-aio \
        --with-http_addition_module \
        --with-http_auth_request_module \
        --with-http_dav_module \
        --with-http_flv_module \
        --with-http_geoip_module=dynamic \
        --with-http_gunzip_module \
        --with-http_gzip_static_module \
        --with-http_image_filter_module=dynamic \
        --with-http_mp4_module \
        --with-http_random_index_module \
        --with-http_realip_module \
        --with-http_secure_link_module \
        --with-http_slice_module \
        --with-http_ssl_module \
        --with-http_stub_status_module \
        --with-http_sub_module \
        --with-http_v2_module \
        --with-http_xslt_module=dynamic \
        --with-ipv6 \
        --with-md5-asm \
        --with-pcre-jit \
        --with-sha1-asm \
        --with-stream \
        --with-stream_ssl_module \
        --with-threads \
        --with-luajit \
        --with-openssl=${BUILD_ROOT}/openssl-${RESTY_OPENSSL_VERSION} \
        --with-pcre=${BUILD_ROOT}/pcre-${RESTY_PCRE_VERSION} \
        --add-module=${BUILD_ROOT}/ngx_pagespeed-${MODULE_PAGESPEED_VERSION}-beta/ \
        " \
    && cd ${BUILD_ROOT}/openresty-${RESTY_VERSION} \
    && ./configure -j${RESTY_J} ${BUILD_CONFIG} \
    && make -j${RESTY_J} \
    && make -j${RESTY_J} install \
    && cd ${BUILD_ROOT} \
    && curl -fSL http://luarocks.org/releases/luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz -o luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && tar xzf luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && cd luarocks-${RESTY_LUAROCKS_VERSION} \
    && ./configure \
        --prefix=/usr/local/openresty/luajit \
        --with-lua=/usr/local/openresty/luajit \
        --lua-suffix=jit-2.1.0-beta2 \
        --with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1 \
    && make build \
    && make install \
    && rm -rf ${BUILD_ROOT} \
    && ln -sf /dev/stdout /usr/local/openresty/nginx/logs/access.log \
    && ln -sf /dev/stderr /usr/local/openresty/nginx/logs/error.log \
    && apt-get -y remove \
        build-essential \
        ca-certificates \
        curl \
        libgd-dev \
        libgeoip-dev \
        libncurses5-dev \
        libperl-dev \
        libreadline-dev \
        libxslt1-dev \
        make \
        perl \
        unzip \
        zlib1g-dev \
        wget \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/* \
    && mv /etc/apt/sources.list.bak /etc/apt/sources.list \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/*

# Add additional binaries into PATH for convenience
ENV PATH=$PATH:/usr/local/openresty/luajit/bin/:/usr/local/openresty/nginx/sbin/:/usr/local/openresty/bin/

#ENTRYPOINT ["/usr/local/openresty/bin/openresty", "-g", "daemon off;"]

FROM alpine:3.4

MAINTAINER Jackie "www.jackieathome.net"
RUN NGINX_VERSION=1.11.13 \
        && OPENSSL_VERSION=1.0.2k \
        && BUILD_ROOT=/usr/local/src/nginx \
        && BUILD_CONFIG="\
            --prefix=/etc/nginx \
            --sbin-path=/usr/sbin/nginx \
            --conf-path=/etc/nginx/nginx.conf \
            --error-log-path=/var/log/nginx/error.log \
            --http-log-path=/var/log/nginx/access.log \
            --pid-path=/var/run/nginx.pid \
            --lock-path=/var/run/nginx.lock \
            --http-client-body-temp-path=/var/cache/nginx/client_temp \
            --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
            --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
            --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
            --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
            --user=nginx \
            --group=nginx \
            --with-openssl=$BUILD_ROOT/openssl-$OPENSSL_VERSION \
            --with-http_ssl_module \
            --with-http_v2_module \ 
            --with-threads \
            --with-http_realip_module \
            --with-http_addition_module \
            --with-http_sub_module \
            --with-http_dav_module \
            --with-http_flv_module \
            --with-http_mp4_module \
            --with-http_gunzip_module \
            --with-http_gzip_static_module \
            --with-http_random_index_module \
            --with-http_secure_link_module \
            --with-http_stub_status_module \
            --with-http_auth_request_module \
            --with-http_xslt_module=dynamic \
            --with-http_image_filter_module=dynamic \
            --with-http_geoip_module=dynamic \
            --with-http_perl_module=dynamic \
            --with-stream \
            --with-stream_ssl_module \
            --with-stream_ssl_preread_module \
            --with-stream_realip_module \
            --with-stream_geoip_module=dynamic \
            --with-http_slice_module \
            --with-compat \
            --with-file-aio \
            " \
        && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories \
        && addgroup -S nginx \
        && adduser -D -S -h /var/cache/nginx -s /sbin/nologin -G nginx nginx \
        && apk add --no-cache --virtual .build-deps \
            gcc \
            libc-dev \
            make \
            pcre-dev \
            zlib-dev \
            linux-headers \
            curl \
            gnupg \
            libxslt-dev \
            gd-dev \
            geoip-dev \
            perl-dev \
            automake \
        && mkdir -p $BUILD_ROOT \
        && cd $BUILD_ROOT \
        && curl https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz -o $BUILD_ROOT/openssl-$OPENSSL_VERSION.tar.gz \
        && curl https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz -o $BUILD_ROOT/nginx-$NGINX_VERSION.tar.gz \
        && tar vxzf nginx-$NGINX_VERSION.tar.gz \
        && tar vxfz openssl-$OPENSSL_VERSION.tar.gz \
        && cd nginx-$NGINX_VERSION \
        && ./configure $BUILD_CONFIG \
        && make -j$(getconf _NPROCESSORS_ONLN) \
        && make install \
        && rm -rf /etc/nginx/html/ \
        && mkdir /etc/nginx/conf.d/ \
        && mkdir -p /usr/share/nginx/html/ \
        && install -m644 html/index.html /usr/share/nginx/html/ \
        && install -m644 html/50x.html /usr/share/nginx/html/ \
        && ln -s ../../usr/lib/nginx/modules /etc/nginx/modules \
        && strip /usr/sbin/nginx* \
        && rm -rf $BUILD_ROOT \
        && apk add --no-cache --virtual .gettext gettext \
        && mv /usr/bin/envsubst /tmp/ \
        \
        && runDeps="$( \
            scanelf --needed --nobanner /usr/sbin/nginx /usr/lib/nginx/modules/*.so /tmp/envsubst \
                | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                | sort -u \
                | xargs -r apk info --installed \
                | sort -u \
        )" \
        && apk add --no-cache --virtual .nginx-rundeps $runDeps \
        && apk del .build-deps \
        && apk del .gettext \
        && mv /tmp/envsubst /usr/local/bin/ \
        \
        # forward request and error logs to docker log collector
        && ln -sf /dev/stdout /var/log/nginx/access.log \
        && ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
#CMD ["/bin/sh"]


FROM alpine:3.13

LABEL maintainer="Bardi Harborow <bardi@bardiharborow.com>"

ENV H2O_VERSION 2.2.6
ENV H2O_DOWNLOAD_SHA256 f8cbc1b530d85ff098f6efc2c3fdbc5e29baffb30614caac59d5c710f7bda201
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
COPY ./h2o.tar.gz h2o.tar.gz
RUN \
        # create user and group
        addgroup -S h2o \
        && adduser -D -S -h /var/www -s /sbin/nologin -G h2o h2o \
        # create static gid per alpine package .pre-install
        && addgroup -S -g 82 www-data \
        && addgroup h2o www-data \
        # install build dependencies
        && apk add --no-cache --virtual .build-deps \
                bison \
                build-base \
                cmake \
                curl \
                libuv-dev \
                linux-headers \
                openssl-dev \
                ruby \
                ruby-dev \
                wslay-dev \
                yaml-dev \
                zlib-dev \
        # download H2O source code and verify integrity
        # && curl -fSL https://github.com/h2o/h2o/archive/v$H2O_VERSION.tar.gz  -o h2o.tar.gz \
        && echo "$H2O_DOWNLOAD_SHA256 *h2o.tar.gz" | sha256sum -c - \
        # extract archive
        && mkdir -p /usr/src \
        && tar -zxC /usr/src -f h2o.tar.gz \
        && rm h2o.tar.gz \
        # build H2O
        && cd /usr/src/h2o-$H2O_VERSION \
        && cmake -DWITH_BUNDLED_SSL=on \
                 -DCMAKE_INSTALL_PREFIX=/usr \
                 -DWITH_MRUBY=on \
        && make -j$(getconf _NPROCESSORS_ONLN) \
        && make install \
        && install -m644 -D /usr/src/h2o-$H2O_VERSION/examples/doc_root/index.html /var/www/index.html \
        && rm -rf /usr/src/h2o-$H2O_VERSION \
        # install runtime dependencies
        && apk add --no-cache --virtual .runtime-deps \
                libstdc++ \
                openssl \
                perl \
                zlib \
        && apk del .build-deps \
        # forward request and error logs to the docker log collector
        && mkdir -p /var/log/h2o/ \
        && ln -sf /dev/stdout /var/log/h2o/access.log \
        && ln -sf /dev/stderr /var/log/h2o/error.log

COPY h2o.conf /etc/h2o.conf

EXPOSE 80 443

CMD ["h2o", "--conf", "/etc/h2o.conf"]
ARG PHP_VERSION
FROM ${PHP_VERSION}

ARG TZ
ARG PHP_EXTENSIONS
ARG CONTAINER_PACKAGE_URL


RUN sed -i "s/dl-cdn.alpinelinux.org/${CONTAINER_PACKAGE_URL}/g" /etc/apk/repositories



ENV LD_LIBRARY_PATH=/usr/local/instantclient
ENV ORACLE_HOME=/usr/local/instantclient
ENV NLS_LANG=AMERICAN_AMERICA.AL32UTF8

RUN apk update && apk upgrade
RUN apk add --no-cache musl-dev libaio autoconf libnsl && apk add --update make && pecl channel-update pecl.php.net
# RUN pecl channel-update pecl.php.net

COPY ./extensions /tmp/extensions
WORKDIR /tmp/extensions

# RUN unzip -d /usr/local/ /tmp/extensions/instantclient_11_2.zip && \
#     chmod +x -R /usr/local/instantclient_11_2/* && \
#     ln -s /usr/local/instantclient_11_2 /${ORACLE_HOME} && \
#     ln -s /${ORACLE_HOME}/libclntsh.so.* /${ORACLE_HOME}/libclntsh.so && \
#     ln -s /${ORACLE_HOME}/libocci.so.* /${ORACLE_HOME}/libocci.so && \
#     ln -s /${ORACLE_HOME}/lib* /usr/lib && \
#     ln -s /${ORACLE_HOME}/sqlplus /usr/bin/sqlplus
# RUN ls -al /usr/local/instantclient/
# RUN apk add gcc; exit 0
# RUN echo "instantclient,/usr/local/instantclient" | pecl install oci8-2.0.12 && \
#     echo 'extension=oci8.so' > /usr/local/etc/php/conf.d/30-oci8.ini

RUN chmod +x install.sh \
    && sh install.sh \
    && rm -rf /tmp/extensions

ADD ./extensions/install-php-extensions  /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions

RUN apk --no-cache add tzdata \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone

# Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv libstdc++ --no-cache --repository http://${CONTAINER_PACKAGE_URL}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php


# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer

# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

WORKDIR /www
RUN chmod -R 777 /www

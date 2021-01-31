FROM php:7.4-fpm

# docker-compose 변수
ARG WEB_USER
ARG WEB_GROUP
ARG PHP_ROOT_DIR 

RUN apt-get update && apt-get install -y apt-transport-https

# php package 설치를 위해 필요한 library 설치
RUN apt-get install -y curl libpng-dev zlib1g-dev libzip-dev libonig-dev \
    libcurl4-openssl-dev pkg-config libxml2-dev libssl-dev vim lib32z1 libxslt-dev supervisor unzip cron \
    locales locales-all

RUN docker-php-ext-install zip pdo_mysql mysqli xsl pcntl soap

# 이미지 편집 lib
RUN apt-get install -y libjpeg62-turbo-dev libfreetype6-dev
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install gd

# logging
RUN mkdir /var/log/php7
RUN touch /var/log/php7/access.log
RUN touch /var/log/php7/error.log
RUN touch /var/log/php-fpm.log
RUN touch /var/log/php7/laravel-worker.log
RUN sed -i 's/;error_log = log\/php-fpm.log/error_log = \/var\/log\/php-fpm.log/g' /usr/local/etc/php-fpm.conf

RUN usermod -u 1000 ${WEB_USER} \
 && groupmod -g 1000 ${WEB_GROUP} \
 && chgrp -R staff ${PHP_ROOT_DIR}/php-fpm.d/www.conf

# vue-cli 설정
ENV HOME /root
ENV NVM_DIR $HOME/.nvm
ENV NODE_VERSION 12.14.1

# Install nvm with node and npm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default \
    && npm install -g yarn

ENV NODE_PATH $NVM_DIR/versions/node/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH


# mysql, php-fpm port 외부 노출
EXPOSE 9000
EXPOSE 8090
#CMD ["php-fpm"]
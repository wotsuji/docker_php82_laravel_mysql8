
FROM php:8.2-apache

# composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# update/install
RUN apt-get update 
RUN apt-get install -y git vim curl zip unzip

# apache module activation
RUN a2enmod deflate expires rewrite

# install-php-extensions をインストール
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions

# PHP のエクステンションをインストール
# opcache ← PHPコードの変更の反映が遅れる場合があるため開発環境に入れない
RUN install-php-extensions  intl gd exif imagick mysqli pdo_mysql redis xdebug zip

# SSL証明書コピー
COPY ./ssl/server.crt  /etc/ssl/certs/
COPY ./ssl/server.key /etc/ssl/private/
COPY ./ssl/cacert.pem /etc/ssl/certs/
RUN sed -i 's!/etc/ssl/certs/ssl-cert-snakeoil.pem!/etc/ssl/certs/server.crt!g' /etc/apache2/sites-available/default-ssl.conf \
    && sed -i 's!/etc/ssl/private/ssl-cert-snakeoil.key!/etc/ssl/private/server.key!g' /etc/apache2/sites-available/default-ssl.conf
RUN a2enmod ssl \
    && a2ensite default-ssl.conf

# composerのインストール
WORKDIR /usr/bin
RUN curl -s http://getcomposer.org/installer | php
# すでに作成されていた
#RUN ln -s /usr/bin/composer.phar /usr/bin/composer

# Laravelで必要になるmodRewriteを有効化
# これをしておかないと、Laravelのリダイレクト処理が正しく行われなくなってしまう
RUN mv /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled
RUN /bin/sh -c a2enmod rewrite

# コンテナ内で作業する際のカレンとディレクトリの指定
WORKDIR /var/www/html


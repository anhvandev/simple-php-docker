FROM php:8.1.9-fpm

# Set working directory
WORKDIR /var/www
ARG PUID
ARG PUSER

# Install dependencies
RUN apt-get update
RUN apt-get install -y \
        build-essential \
        curl \
        libicu-dev \
        libz-dev \
        libjpeg-dev \
        libjpeg62-turbo-dev \
        #libpng12-dev \
        libpng-dev \
        libfreetype6-dev \
        libwebp-dev \
        libssl-dev \
        libmcrypt-dev \
        libxml2-dev \
        libbz2-dev \
		libonig-dev \
		libxslt-dev \
		libsqlite3-dev \
        libc-client-dev \
        libkrb5-dev \
        #php-pear \
        libmagickwand-dev --no-install-recommends \
        locales \
        libzip-dev \
        zip \
        jpegoptim optipng pngquant gifsicle \
        vim \
        unzip \
        git \
        subversion \
	&& pecl install imagick redis xdebug uploadprogress \
	&& rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install bcmath \
                           calendar \
                           dom \
                           exif \
                           gettext \
                           mysqli \
                           fileinfo \
                           simplexml \
                           intl \
                           xml \
                           posix \
                           pdo_sqlite \
                           pdo_mysql \
                           sockets \
                           zip \
                           opcache \
    && docker-php-ext-configure bcmath --enable-bcmath \
    && docker-php-ext-configure gd --with-freetype --with-jpeg -with-webp \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql \
    && docker-php-ext-enable calendar dom exif gettext mysqli fileinfo simplexml intl posix \
        pdo_sqlite sockets zip uploadprogress \
        imagick redis opcache xdebug

RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl
RUN docker-php-ext-install imap
# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

COPY zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf

RUN chown www-data:www-data /var/www
SHELL ["/bin/bash", "--login", "-c"]
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
RUN nvm install 16.16.0

# RUN useradd -G www-data,root -u ${PUID} -d /home/${PUSER} ${PUSER}
# RUN mkdir -p /home/${PUSER}/.composer && \
#     chown -R ${PUSER}:${PUSER} /home/${PUSER}

# RUN groupmod -o -g 319816193 www-data
# RUN usermod -o -u 319820906 www-data
# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]

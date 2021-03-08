FROM php:8.0.2-apache
LABEL Patrick Williams <patrick.williams@akapumkin.com>
ARG COCKPIT_VERSION="master"

RUN apt-get update \
    && apt-get install -y \
	wget zip unzip \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        sqlite3 libsqlite3-dev \
        libssl-dev \
        libzip-dev \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) iconv gd pdo zip opcache pdo_sqlite pdo_mysql \
    && a2enmod rewrite expires

# install cockpit
RUN wget https://github.com/agentejo/cockpit/archive/${COCKPIT_VERSION}.zip -O /tmp/cockpit.zip; unzip /tmp/cockpit.zip -d /tmp/; rm /tmp/cockpit.zip \
    && mv /tmp/cockpit-${COCKPIT_VERSION}/.htaccess /var/www/html/.htaccess \
    && mv /tmp/cockpit-${COCKPIT_VERSION}/* /var/www/html/ \
    && rm -R /tmp/cockpit-${COCKPIT_VERSION}/ 

# install monderation addon for workflows
RUN wget https://github.com/pauloamgomes/CockpitCMS-Moderation/archive/master.zip -O /tmp/CockpitCMS-Moderation.zip; unzip /tmp/CockpitCMS-Moderation.zip -d /tmp/; rm /tmp/CockpitCMS-Moderation.zip \
    && mkdir -p /var/www/html/addons/Moderation \
    && mv /tmp/CockpitCMS-Moderation-master/* /var/www/html/addons/Moderation/ \
    && rm -R /tmp/CockpitCMS-Moderation-master/

# install cloud storage addon for AWS, Azure or Google storage
RUN wget https://github.com/agentejo/CloudStorage/archive/master.zip -O /tmp/CloudStorage.zip; unzip /tmp/CloudStorage.zip -d /tmp/; rm /tmp/CloudStorage.zip \
    && mkdir -p /var/www/html/addons/CloudStorage \
    && mv /tmp/CloudStorage-master/* /var/www/html/addons/CloudStorage/ \
    && rm -R /tmp/CloudStorage-master/ 

RUN echo "\n\nphp_value post_max_size 256M" >> /var/www/html/.htaccess \
    && echo "\nphp_value  upload_max_filesize 256M" >> /var/www/html/.htaccess

COPY ./configs/php.ini /usr/local/etc/php/php.ini

RUN chown -R www-data:www-data /var/www/html
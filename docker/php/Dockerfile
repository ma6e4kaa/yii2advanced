FROM php:8.1-fpm

# Установка зависимостей
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libzip-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Установка PHP расширений
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

# Установка XDebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

# Копирование конфига XDebug
COPY docker/php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Установка Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Настройка рабочей директории
WORKDIR /var/www/html

# Копирование зависимостей
COPY composer.json composer.lock ./
RUN composer install --prefer-dist --no-interaction --no-scripts

# Копирование всего проекта
COPY . .

# Права на runtime
RUN mkdir -p backend/runtime frontend/runtime console/runtime \
    && chmod -R 777 backend/runtime frontend/runtime console/runtime

# Инициализация приложения
RUN php init --env=Development --overwrite=All
RUN php yii migrate --interactive=0

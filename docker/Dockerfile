# --------------------------------------------------
# Single-stage PHP-FPM + Node for Laravel + Vite
# --------------------------------------------------
FROM php:8.3-fpm-alpine

WORKDIR /app

# --------------------------------------------------
# System dependencies + Node
# --------------------------------------------------
RUN apk add --no-cache \
    bash \
    curl \
    git \
    unzip \
    nodejs npm \
    libzip-dev \
    oniguruma-dev \
    autoconf \
    build-base \
    libxml2-dev \
    bash-completion \
    nginx \
    supervisor \
    shadow \
    && docker-php-ext-install pdo_mysql zip bcmath opcache xml \
    && docker-php-ext-enable opcache

# --------------------------------------------------
# Composer
# --------------------------------------------------
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# --------------------------------------------------
# Copy Laravel app
# --------------------------------------------------
COPY . .

# Ensure .env exists
RUN cp .env.example .env

# --------------------------------------------------
# PHP dependencies
# --------------------------------------------------
RUN composer install --no-dev --optimize-autoloader

# Run post-install scripts
RUN php artisan package:discover --ansi

# --------------------------------------------------
# Node / Vite frontend build
# --------------------------------------------------
RUN npm install
RUN npm run build

# --------------------------------------------------
# Permissions
# --------------------------------------------------
RUN chown -R www-data:www-data /app \
    && chmod -R 775 storage bootstrap/cache

# --------------------------------------------------
# Nginx configuration
# --------------------------------------------------
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf
COPY ./nginx/app.conf /etc/nginx/conf.d/app.conf

# --------------------------------------------------
# Expose port
# --------------------------------------------------
EXPOSE 80

# --------------------------------------------------
# Start PHP-FPM + Nginx
# --------------------------------------------------
CMD ["sh", "-c", "php-fpm & nginx -g 'daemon off;'"]

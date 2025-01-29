# Stage 1: Install dependencies and Composer
ARG PHP_VERSION=7.3
FROM php:${PHP_VERSION}-cli-alpine AS builder

# Install required dependencies to compile PHP extensions
RUN apk add --no-cache curl bash git unzip

# Download PHP extension installer
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions xdebug curl soap pcov

# Securely download and verify Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Set working directory
WORKDIR /app

# Copy project files (if needed)
COPY . /app/

# Install Composer dependencies (Optional: `--no-dev` for production)
RUN composer install --no-dev --optimize-autoloader


# Stage 2: Optimized final image
FROM php:${PHP_VERSION}-fpm-alpine

# Copy only necessary files from the builder stage
COPY --from=builder /usr/local/bin/composer /usr/local/bin/composer
COPY --from=builder /usr/local/lib/php/extensions /usr/local/lib/php/extensions
COPY --from=builder /app /app

# Set environment variables
ENV COMPOSER_ALLOW_SUPERUSER=1

# Set working directory
WORKDIR /app/xsolla-sdk-php

# Expose PHP-FPM port
EXPOSE 9000

# Add a HEALTHCHECK to monitor php-fpm status
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD curl --fail http://localhost:9000 || exit 1

CMD ["php-fpm"]

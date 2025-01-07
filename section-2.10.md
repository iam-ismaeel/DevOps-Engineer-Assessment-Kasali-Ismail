Question
Write a Dockerfile to containerize a Laravel application.




A Dockerfile for containerizing a Laravel application:

```
# Use an official PHP image with Apache
FROM php:8.2-apache

# Set environment variables
ENV APACHE_DOCUMENT_ROOT /var/www/html/public

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    git \
    curl \
    nano \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql mbstring exif pcntl bcmath xml

# Enable Apache rewrite module
RUN a2enmod rewrite

# Set the working directory
WORKDIR /var/www/html

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Copy Laravel application code to the container
COPY . /var/www/html

# Set permissions for Laravel
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 775 /var/www/html/storage \
    && chmod -R 775 /var/www/html/bootstrap/cache

# Expose the default Apache port
EXPOSE 80

# Start the Apache server
CMD ["apache2-foreground"]
```

Explanation of the Dockerfile:

1. Base Image: The php:8.2-apache image includes PHP and Apache, perfect for running Laravel applications.
2. Environment Variables: Sets the Apache document root to Laravel's public folder.
3. Dependencies: Installs necessary PHP extensions and system tools required by Laravel.
4. Composer: Adds Composer to manage PHP dependencies.
5. File Copying: Copies the Laravel application files into the container.
6. Permissions: Sets the correct permissions for Laravel's storage and bootstrap/cache directories.
7. Expose Port: Exposes port 80 to serve the application.
8. CMD: Runs the Apache server.

Build and Run:
1. Build the Docker image:
```
docker build -t laravel-app .
```
2. Run the container:
```
docker run -p 8080:80 laravel-app
```


Access Laravel application at http://localhost:8080.

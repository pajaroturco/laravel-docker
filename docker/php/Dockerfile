# Imagen base de PHP
FROM php:8-fpm

# Copiar el archivo de configuración de PHP
COPY php.ini /usr/local/etc/php/

# Agregar usuario laravel
RUN groupadd -g 1000 laravel && \
    useradd -u 1000 -g laravel -m -s /bin/bash laravel


# Instalar dependencias requeridas para Laravel
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        supervisor \
        libonig-dev \
        libzip-dev \
        zip \
        unzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Instalar extensiones de PHP requeridas para Laravel
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install zip

# Instalar Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
&& php -r "unlink('composer-setup.php');"


# Copiar el archivo de configuración de supervisord
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Copiar los archivos de la aplicación Laravel
COPY --chown=laravel:laravel . /var/www/html

RUN groupmod -o -g 1000 www-data && \
    usermod -o -u 1000 -g www-data www-data

# Establecer el directorio de trabajo
WORKDIR /var/www/html

# Exponer el puerto de Nginx
EXPOSE 80

# Ejecutar supervisord para iniciar los servicios de PHP y Nginx
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

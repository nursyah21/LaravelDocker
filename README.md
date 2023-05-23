# Readme Test Laravel

this is tutorial to create laravel and mysql with docker from scratch

## requirement

- docker desktop
- php >= 8.2
- composer
- node >= 18

for windows need to install wsl (windows sub linux) to run command bellow

## step by step #1

> create laravel and mysql with docker

1. create laravel project


```bash
composer create-project laravel/laravel <my-project>
```

2. create Dockerfile for laravel project

```bash
cat > Dockerfile << EOF
FROM php:8.2.1-fpm

RUN docker-php-ext-install pdo pdo_mysql sockets
RUN curl -sS https://getcomposer.org/installer​ | php -- \
     --install-dir=/usr/local/bin --filename=composer

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /app
COPY . .
RUN composer install
EOF
```

3. create compose docker

```bash
cat > docker-compose.yml << EOF
version: '3'
services: 
    test-laravel:
        build: 
            context: .
            dockerfile: Dockerfile
        command: 'php artisan serve --host=0.0.0.0'
        restart: unless-stopped
        volumes:
            - .:/app
        networks:
            - sail
        ports:
            - 8000:8000
        depends_on:
            - mysql
    mysql:
        image: mysql:5.7
        restart: unless-stopped
        command: --default-authentication-plugin=mysql_native_password
        environment:
            MYSQL_DATABASE: '${DB_DATABASE}'
            MYSQL_USER: 'user'
            MYSQL_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ROOT_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ALLOW_EMPTY_PASSWORD: 1
        ports:
            - '3306:3306'
        networks:
            - sail
        expose:
            - '3306'
        volumes:
            - my-db:/var/lib/mysql
networks:
    sail:
        driver: bridge
volumes:
    my-db:
EOF
```

4. run docker compose 

```bash
docker compose up -d
```

5. testing server laravel and mysql

- `check server` laravel open [localhost:8000](http://localhost:8000)
- `check mysql` 

```bash
php artisan migrate
```

6. stop docker compose

```bash
docker compose down
```

## step by step #2

> install breeze in laravel

1. composer install breeze

```bash
composer require laravel/breeze --dev
```

2. install pnpm

```bash
npm install -g pnpm
```

3. install breeze

```bash
php artisan breeze:install
 
php artisan migrate
pnpm install
pnpm run dev
```

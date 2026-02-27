# laravel-apache.dev

Laravel + Apache + MySQL development environment with Docker and Docker Compose. Based on a [dev.to Article](https://dev.to/veevidify/docker-compose-up-your-entire-laravel-apache-mysql-development-environment-45ea) and [Laravel + Apache + Docker](https://github.com/veevidify/laravel-apache-docker) by [veevidify](https://github.com/veevidify).

## Status

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/rolodoom/laravel-apache.dev/main/LICENSE)

## Contents:

- [Requirements](#requirements)
- [Usage](#usage)
  - [Up and Running](#up-and-running)
  - [Setup Laravel Project](#setup-laravel-project)
- [phpMyAdmin](#phpmyadmin)
- [Helper Scripts](#helper-scripts)
  - [composer](#composer)
  - [container](#container)
  - [database](#database)
  - [database_backup](#database_backup)
  - [fix-permissions](#fix-permissions)
  - [php-artisan](#php-artisan)
  - [phpunit](#phpunit)
- [Bugs and Issues](#bugs-and-issues)
- [License](#license)

## Requirements

- Latest versions of **Docker** and **Docker Compose** installed.
- On Linux, you need [to add your user to the docker group](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user).

### Versions included in this template

- **PHP**: 8.3-apache (via `laravel-apache/dev:latest`)
- **MySQL**: 8.0 (via `mysql:8.0`)
- **phpMyAdmin**: 5.2.2 (via `phpmyadmin/phpmyadmin:5.2.2`)
- **Composer**: latest (copied into the PHP container)

## Usage

### Up and Running

Clone the repo and create the folder structure for the laravel source code:

```bash
git clone https://github.com/rolodoom/laravel-apache.dev.git
cd laravel-apache.dev
mkdir -p app db
```

Copy `env.example` to `.env` and modify it to your needs:

```bash
cp env.example .env
```

Build the images and start the services:

```bash
docker compose build --no-cache
docker compose up -d
```

### Setup Laravel Project

> In this template, the `app/` folder is ignored by Git. When creating a new project, Laravel should be installed inside this folder in your local clone.

Create Laravel Project:

```bash
./composer create-project laravel/laravel .
```

Modify Laravel `src/.env` file to match your container credentials:

```
APP_URL=http://localhost:<APP_PORT>

DB_CONNECTION=mysql
DB_HOST=mysql-db
DB_PORT=<MYSQL_PORT>
DB_DATABASE=laravel
DB_USERNAME=dbuser
DB_PASSWORD=secret
```

Fix permissions:

```bash
./fix-permissions
```

Initialize Laravel:

```bash
./php-artisan key:generate
./php-artisan migrate
./php-artisan config:cache
```

**NOTE:** To regenrate DB use `./php-artisan migrate:fresh --seed`

The project can be accessed at http://localhost:<APP_PORT>

## phpMyAdmin

You can also visit http://localhost:<PHPMYADMIN_PORT> to access phpMyAdmin after starting the containers.

The default username is `root`, and the password is the same as supplied in the `.env` file.

## Helper Scripts

### composer

Run any `composer` command inside the `${PROJECT_NAME}-app` container, example:

```bash
./composer create-project laravel/laravel .
./composer dump-autoload
```

### container

Running `./container` takes you inside the `${PROJECT_NAME}-app` container under user `uid(1000)` (same with host user)

### database

Running `./database` will connect to your `${PROJECT_NAME}-db` container's daemon using mysql client.

### database_backup

Run `./database_backup` to backup your database inside the `${PROJECT_NAME}-db` container's daemon using mysqldump client. The backup file will be stored inside the `./backup` folder as `<DB_DATABASE>_backup_YYYYMMDD_HHMMSS.sql`.

```bash
./database_backup
```

### fix-permissions

Running `./fix-permissions` will fix storage and bootstrap/cache permissions in `/var/www/html` folder inside the container.

### php-artisan

Running `./php-artisan` will take you inside the `${PROJECT_NAME}-app` container and run any `php artisan` command, example:

```bash
./php-artisan make:controller BlogPostController --resource
```

### phpunit

Run `./vendor/bin/phpunit` to execute tests inside the `${PROJECT_NAME}-app` container, example:

```bash
./phpunit --group=failing
```

## Bugs and Issues

Have a bug or an issue with this template? [Open a new issue](https://github.com/rolodoom/laravel-apache.dev/issues) here on GitHub.

## License

This code in this repository is released under the [MIT](https://raw.githubusercontent.com/rolodoom/laravel-apache.dev/main/LICENSE) license, which means you can use it for any purpose, even for commercial projects. In other words, do what you want with it. The only requirement with the MIT License is that the license and copyright notice must be provided with the software.

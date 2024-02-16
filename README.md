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
  - [container](#container)
  - [db](#db)
  - [composer](#composer)
  - [php-artisan](#php-artisan)
  - [phpunit](#phpunit)
- [Bugs and Issues](#bugs-and-issues)
- [License](#license)

## Requirements

- Latest versions of **Docker** and **Docker Compose** installed.
- On Linux you need [to add your user to the docker group](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user).

## Usage

### Up and Running

Clone the repo and create the folder structure for the laravel source code:

```bash
git clone https://github.com/rolodoom/laravel-apache.dev.git
cd laravel-apache.dev
mkdir -p src run/var
```

Copy `.env.example` to `.env`

```bash
cp .env.example .env
```

Build the images and start the services:

```bash
docker-compose build --no-cache
docker-compose up -d
```

### Setup Laravel Project

Create Laravel Project:

```bash
./composer create-project laravel/laravel .
```

Modify Laravel `src/.env` file to match your container credentials:

```
APP_URL=http://localhost:<APP_PORT>

DB_HOST=mysql-db
DB_PORT=<MYSQL_PORT>
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=<DB_PASSWORD>
```

Initialize Laravel:

```bash
./composer install
./php-artisan key:generate
./php-artisan migrate
./php-artisan config:cache
```

**NOTE:** To regenrate DB use `./php-artisan migrate:fresh --seed`

## phpMyAdmin

You can also visit http://localhost:8088 to access phpMyAdmin after starting the containers.

The default username is `root`, and the password is the same as supplied in the `.env` file.

## Helper Scripts

### container

```bash
#!/bin/bash

docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash"
```

Running `./container` takes you inside the `laravel-app` container under user `uid(1000)` (same with host user)

### db

```bash
#!/bin/bash

docker exec -it mysql-db bash -c "mysql -u dbuser -psecret db"
```

Running `./db` will connect to your database container's daemon using mysql client.

### composer

```bash
#!/bin/bash

args="$@"
command="composer $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\""
```

Run any `composer` command, example:

```bash
$ ./composer dump-autoload
```

### php-artisan

```bash
#!/bin/bash

args="$@"
command="php artisan $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\""
```

Run any `php artisan` command, example:

```bash
$ ./php-artisan make:controller BlogPostController --resource
```

### phpunit

```bash
#!/bin/bash

args="$@"
command="vendor/bin/phpunit $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\""
```

Run `./vendor/bin/phpunit` to execute tests, example:

```bash
$ ./phpunit --group=failing
```

## Bugs and Issues

Have a bug or an issue with this template? [Open a new issue](https://github.com/rolodoom/laravel-apache.dev/issues) here on GitHub.

## License

This code in this repository is released under the [MIT](https://raw.githubusercontent.com/rolodoom/laravel-apache.dev/main/LICENSE) license, which means you can use it for any purpose, even for commercial projects. In other words, do what you want with it. The only requirement with the MIT License is that the license and copyright notice must be provided with the software.

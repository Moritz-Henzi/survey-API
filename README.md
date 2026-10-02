# Survey API

A RESTful API for creating surveys, collecting responses and evaluating results. Three roles **respondent**, **author** and **admin** all share the ability to respond to surveys. **author** and **admin** can create surveys and the coresponding questions. **author** can only execute actions and view results of their own surveys while **admin** can execute actions and view results of every survey.

## Features

## Tech stack

- PHP 8.3+ (Sail and CI run 8.5), Laravel 13
- MySQL 8.4 (local tests run on in-memory SQLite)
- [Laravel Sail](https://laravel.com/docs/sail) — Docker dev environment with MySQL and Mailpit
- [Laravel Sanctum](https://laravel.com/docs/sanctum) `^4.0` — bearer-token API auth
- [spatie/laravel-permission](https://spatie.be/docs/laravel-permission) `^8.3` — roles & permissions
- [spatie/laravel-query-builder](https://spatie.be/docs/laravel-query-builder) `^7.3` — filtering, sorting, includes
- [Pest](https://pestphp.com/) `^5.0` — testing; [Pint](https://laravel.com/docs/pint) — formatting; [Larastan](https://github.com/larastan/larastan)

## Getting started

The project runs on Laravel Sail. Every PHP/Artisan/Composer command goes through `./vendor/bin/sail`.

```bash
cp .env.example .env
```

In `.env`, point the app at the Sail services:

```dotenv
APP_URL=http://localhost
DB_HOST=mysql
DB_USERNAME=sail
DB_PASSWORD=password

# Deliver mail to Mailpit instead of the log
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
```

Install dependencies then start the containers:

```bash
composer install
./vendor/bin/sail up -d
./vendor/bin/sail artisan key:generate
./vendor/bin/sail artisan migrate --seed
```

| Service | URL |
|---|---|
| API | `http://localhost/api/v1` (`APP_PORT`, default 80) |
| Mailpit | `http://localhost:8025` |
| MySQL | `localhost:3306` (`FORWARD_DB_PORT`) |

## Authentification

## Endpoints 

## Testing

## Project structure / architecture


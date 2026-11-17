# Laravel10-Orchid

A Laravel 10 application for exploring the Orchid admin panel: custom CRUD screens for clients, points and posts next to Orchid's own example screens.

## Context

A learning project built by Oleksandr Karabudka in July and August 2023 (Docker setup in July, main work 13 to 16 August). It was used to learn how Orchid builds back-office interfaces from PHP classes (screens, layouts, filters, modals) instead of hand-written templates.

## Features

- **Clients** screen: paginated table with sortable status, a phone filter and a hidden-by-default creation date column; clients are linked to services.
- **Points** screen: list with a modal form to create a point (with validation) and a delete button with confirmation.
- **Posts** screens: list with sortable and text-filterable title, plus a create/edit/remove form with a title, an author picked from users (`Relation` field) and a rich-text body (`Quill` editor).
- Orchid's built-in user and role management, profile screen and example screens (form fields, layouts, charts, cards, text editors).
- Seeders for services, clients (phone numbers from a JSON file, normalised to E.164 with laravel-phone) and 50 demo posts.

## Tech stack

- PHP 8.1+, Laravel 10
- Orchid Platform 14
- propaganistas/laravel-phone
- MariaDB 10.5, Redis, MailHog
- Docker Compose (nginx, PHP-FPM 8.2), Makefile helpers

## Architecture

- All admin UI lives in `app/Orchid`: `Screens` define queries, command bars and actions; `Layouts` define tables and forms; `Filters` and `Presenters` support lists.
- Admin routes are registered in `routes/platform.php`, the menu in `app/Orchid/PlatformProvider.php`. The panel is served under the `/admin` prefix (`config/platform.php`).
- Models: `Client`, `Service`, `Point`, `Post`, `User`. `Client` uses Orchid's `Filterable` trait with an explicit list of allowed filters.
- The public site is just the default welcome page; all work happens in the admin panel.

## Getting started

Requires Docker, Docker Compose and make.

```bash
sh docker/conf/ssl/generate.sh   # create the local self-signed TLS certificate
make install-backend             # copies .env, starts containers, composer install, storage:link, key:generate
docker compose up -d db          # start the database container
```

Adjust the `DB_*` values in `.env` (with the `db` container, use `DB_HOST=db`), then run inside the `php` container (`make php` opens a shell):

```bash
php artisan migrate
php artisan orchid:admin <name> <email> <password>
php artisan db:seed --class=ServiceSeeder
php artisan db:seed --class=ClientSeeder
php artisan db:seed --class=PostSeeder
```

Open http://localhost/admin and log in with the admin account. `make down` stops the containers, `make clear-cache` runs `php artisan optimize:clear`.

## Testing

Only the default Laravel example tests are included.

```bash
docker compose exec php php artisan test
```

## Status

Finished learning project (2023), not maintained.

## License

MIT, see [LICENSE](LICENSE).

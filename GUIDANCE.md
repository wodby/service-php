# PHP on Wodby

What Wodby sets up for an application that runs on this service. Check it before adding connection settings to the code.

## Linked services

Links to other services reach the application as environment variables. Read them in code; do not hardcode hosts or credentials.

| Link | Variables |
| --- | --- |
| Database (MariaDB, MySQL or PostgreSQL) | `DB_HOST`, `DB_PORT`, `DB_NAME` (also `DB_DATABASE`), `DB_USERNAME`, `DB_PASSWORD`, `DB_DRIVER` (also `DB_CONNECTION`) |
| Mail | `MSMTP_HOST`, `MSMTP_PORT` |

Mail needs no code: PHP's `mail()` is delivered through the linked mail service. An SMTP library or credentials in the codebase are not required.

A variable is present only while its link exists and the linked service is enabled.

## Generated configuration

On every start the container writes `$CONF_DIR/wodby.settings.php` (normally `/var/www/conf/wodby.settings.php`), a `$wodby` array with the environment's hosts and files directory. Framework services built on this one extend it and include it for the application. It is outside the codebase and rewritten on start: never edit it or copy its values into the repository. In a plain PHP application, read the variables above.

## PHP settings

PHP and PHP-FPM are configured through environment variables on the service, such as `PHP_MEMORY_LIMIT`, `PHP_MAX_EXECUTION_TIME` or `PHP_FPM_PM_MAX_CHILDREN`, not through a `php.ini` in the repository. A change applies with the next deployment of the service.

## Environment

- `WODBY_HOSTS` is a JSON array of the environment's hosts; `WODBY_PRIMARY_HOST` and `WODBY_PRIMARY_URL` are the canonical ones for links generated outside a request.
- `WODBY_ENV_TYPE` tells a development environment from a production-like one.

## In a development workspace

- The code is served from the checkout as it is on disk. PHP checks files for changes on every request, so an edit needs no restart.
- Dependencies are installed by workspace setup with `composer install`; `/vendor/` is kept out of Git status.
- A change to variables or linked services still needs a deployment of the environment.

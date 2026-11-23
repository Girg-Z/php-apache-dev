# PHP with Apache for Development

Apache + PHP image for local development (Magento friendly). One parameterized Dockerfile (`common/Dockerfile`), one image tag per PHP version: `8.2`, `8.3`, `8.4`, `8.5`.

Build locally:

```sh
docker build -f common/Dockerfile --build-arg PHP_VERSION=8.5 -t php-apache-dev:8.5 .
```

## Environment variables

| Variable | Default | Description |
|---|---|---|
| `WEB_DOCUMENT_ROOT` / `APACHE_DOCUMENT_ROOT` | `/app` | Apache document root |
| `PHP_MEMORY_LIMIT` | `2G` | PHP `memory_limit` |
| `XDEBUG_MODE` | *(empty)* | Xdebug modes, e.g. `debug`, `profile`, `debug,profile`. Empty = Xdebug is not loaded at all (zero overhead) |
| `XDEBUG_CLIENT_HOST` | `host.docker.internal` | IDE host (on Linux add `extra_hosts: ["host.docker.internal:host-gateway"]`) |
| `XDEBUG_IDEKEY` | `docker` | IDE key |
| `XDEBUG_OUTPUT_DIR` | `/tmp/xdebug` | Where profiler files are written |
| `SMTP_HOST` | `mailhog` | SMTP host used by PHP `mail()` / sendmail |
| `SMTP_PORT` | `1025` | SMTP port |
| `COMPOSER_CACHE_DIR` | `/dev/null` | Composer cache is disabled by default; set a path to enable it |

Fixed settings: `realpath_cache_size=10M`, `realpath_cache_ttl=7200`, opcache enabled for web only (timestamps validated on every request, so edits show up immediately).

## Xdebug

- **Step debugging**: set `XDEBUG_MODE=debug`. Sessions start on trigger (`XDEBUG_SESSION` cookie/param, e.g. via the browser extension).
- **Profiler**: set `XDEBUG_MODE=profile` and add `XDEBUG_TRIGGER=1` as cookie/GET/POST/env var to profile a single request. Cachegrind files land in `XDEBUG_OUTPUT_DIR`.
- **CLI**: `debug script.php` (or `debug php bin/console ...`) starts a debug session immediately, even when `XDEBUG_MODE` is empty.

## Mail

PHP `sendmail_path` points to `msmtp`, which delivers to `SMTP_HOST:SMTP_PORT` (no auth, no TLS). Any SMTP catcher on that port works (MailHog, Mailpit).

## User and home

Runs as `application` (uid 1000) with home `/home/application`. Mount a volume there to persist bash history and Composer config/auth.

## Docker Compose example

```yaml
services:
  web:
    image: girg/php-apache-dev:8.5
    ports:
      - "80:80"
    volumes:
      - ./:/app
      - home:/home/application
    environment:
      WEB_DOCUMENT_ROOT: /app/pub
      APACHE_DOCUMENT_ROOT: /app/pub
      PHP_MEMORY_LIMIT: 3G
      XDEBUG_MODE: debug
      SMTP_HOST: mailhog

  mailhog:
    image: mailhog/mailhog
    ports:
      - "8025:8025"

volumes:
  home:
```

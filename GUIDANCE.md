# Nginx for Moodle on Wodby

What this service adds to the PHP Nginx service it is based on.

## Rules

Moodle's rules replace the image's PHP preset. They are the `preset` config file of this service and can be overridden there. The preset keeps the name `php` (`NGINX_VHOST_PRESET`), which also makes the image point the `php` upstream at the linked Moodle service.

- PHP scripts are passed to PHP-FPM with the path that follows them, so Moodle's slash arguments work, for example `/pluginfile.php/12/mod_resource/content/0/file.pdf` and `/theme/styles.php/boost/…`.
- Requests for paths and scripts that don't exist go to Moodle's router, `r.php`, as Moodle 5.1 and later require. Moodle's JavaScript modules under `/core/esm/` are served that way.
- Files Moodle keeps in its web root but must not serve return 404: `vendor`, `node_modules`, `composer.json`, readme and upgrade notes, `db/install.xml`, test fixtures, `phpunit.xml`, lock files and `environment.xml`. Hidden files return 403.
- Other static files are served by Nginx with a one-year cache lifetime.

## Document root and build

- The `docroot` setting (variable `DOCROOT_SUBDIR`) defaults to `public`, Moodle's web root.
- Only that directory of the Moodle source is copied into this image, at the same path as in the PHP image. `config.php`, `admin/cli` and the rest of the code are not in the web server image.

## Uploads

`NGINX_CLIENT_MAX_BODY_SIZE` is `256m`, the same limit as the Moodle PHP service.

## Check the result

- `nginx -T` shows the rules and document root in effect.
- **Site administration > Server > Environment** in Moodle reports no router warning.

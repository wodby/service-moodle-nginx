# Nginx for Moodle on Wodby

What this service adds to the Nginx Proxy service it is based on.

## Image and rules

The service runs the Nginx variant of the [wodby/moodle](https://github.com/wodby/moodle) image (`5.3-nginx-rN`): Nginx with Moodle's `public` directory, for the Moodle release of the same version as the Moodle service. Keep both services on the same release.

Moodle's rules are the image's PHP preset, `/etc/gotpl/presets/php.conf.tmpl`. They are the `preset` config file of this service and can be overridden there. The service sets the preset name `php` (`NGINX_VHOST_PRESET`), which also makes the image point the `php` upstream at the linked Moodle service.

- PHP scripts are passed to PHP-FPM with the path that follows them, so Moodle's slash arguments work, for example `/pluginfile.php/12/mod_resource/content/0/file.pdf` and `/theme/styles.php/boost/…`.
- Requests for paths and scripts that don't exist go to Moodle's router, `r.php`, as Moodle 5.1 and later require. Moodle's JavaScript modules under `/core/esm/` are served that way.
- Files Moodle keeps in its web root but must not serve return 404: `vendor`, `node_modules`, `composer.json`, readme and upgrade notes, `db/install.xml`, test fixtures, `phpunit.xml`, lock files and `environment.xml`. Hidden files return 403.
- Other static files are served by Nginx with a one-year cache lifetime.

## Document root

`NGINX_SERVER_ROOT` is `/var/www/html/public`, Moodle's web root. `config.php`, `admin/cli` and the rest of the code are not in this image.

## Uploads

`NGINX_CLIENT_MAX_BODY_SIZE` is `256m`, the same limit as the Moodle PHP service.

## Check the result

- `nginx -T` shows the rules and document root in effect.
- **Site administration > Server > Environment** in Moodle reports no router warning.

# Docker nginx + php7.1 container

Docker container based on `Ubuntu 16.04` version. Using `nginx stable` version with `php 7.1`. This is simple container for my own personally purpose. If you have an idea how to improve it, contact me <donatas@navidonskis.com>.

Need PHP 5.6 ? [https://github.com/navidonskis/nginx-php5.6](https://github.com/navidonskis/nginx-php5.6).

## Includes packages

 * nginx, ssmtp, memcached, curl, pwgen, supervisor
 * git, composer
 * php 7.1 (fpm, cli, mysql, apc, curl, gd, intl, mcrypt, mbstring, memcache, memcached, sqlite, tidy, xmlrpc, xsl, pgsql,  ldap, sybase, odbc, soap, json)

## Usage

Creating container via `docker-compose` file.

```yaml
  web:
    image: navidonskis/nginx-php7.1
    container_name: web
    restart: always
    volumes:
      # 1. mount your workdir path
      - /var/www:/var/www
      # 2. mount your configuration of site
      - /mnt/docker/nginx/sites-enabled:/etc/nginx/sites-enabled
      # 3. if you have settings for ssmtp
      - /mnt/docker/nginx/ssmtp/ssmtp.conf:/etc/ssmtp/ssmtp.conf
      # 4. if you want to override php.ini file
      - /mnt/docker/php/custom.ini:/etc/php/7.1/fpm/conf.d/custom.ini
    # 5. have a cronjob tasks? run the command...
    command:
      # remember to escape variables dollar sign with duplication $$ instead $
      - '* * * * * echo "Hello $$(date)" >> /var/log/cron.log 2>&1'
      - '* * * * * echo "Hello world !" >> /var/log/cron.log 2>&1'
```

### Explanations

Check usage section and see explanations

 1. Mount your working directory
 2. Set your own nginx configuration which will be included at `nginx.conf` `http` block.
 3. An example how to use ssmtp (read more here [https://wiki.debian.org/sSMTP](https://wiki.debian.org/sSMTP)):

```
# /etc/ssmtp/ssmtp.conf
mailhub:mail.example.com:587
AuthUser=support@example.com
AuthPass=YourPassword
UseSTARTTLS=YES

hostname=example.com
FromLineOverride=YES
```

 4. Just set your own options to override default `php.ini` file:

```
memory_limit = 1024M
post_max_size = 100M
# set our own ...
```

 5. Set your own cronjob tasks just entering as new entry. Below are an explanation how to use cron. All added entries of commands will be placed at `/etc/cron.d/crontasks` and will be initialized via `crontab`. Type `crontab -e` at container to see your entries. [http://crontab-generator.org/](http://crontab-generator.org/).

```
*     *     *   *    *        command to be executed
-     -     -   -    -
|     |     |   |    |
|     |     |   |    +----- day of week (0 - 6) (Sunday=0)
|     |     |   +------- month (1 - 12)
|     |     +--------- day of        month (1 - 31)
|     +----------- hour (0 - 23)
+------------- min (0 - 59)
```
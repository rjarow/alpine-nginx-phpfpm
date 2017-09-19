# Alpine-NGINX-PHPFPM

This is a Docker Container that uses alpine 3.6, adds the 1.20.00 version of s6 overlay, installs latest nginx, php-fpm and some common php modules.

I have also included mysql-client so that you can interact with a MySQL DB container from within this container.


### Prerequisites

Any OS supporting Docker, I prefer Linux.

[Docker](https://www.docker.com/get-docker)


### Installing

Assuming you have Docker configured correctly, simply pull my image.

Step 1)

```
$ docker pull rjarow/alpine-nginx-phpfpm
$ docker image ls

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
rjarow/alpine-nginx-phpfpm   latest              39af68823660        8 minutes ago       167MB

```
### Testing
```
docker run --rm --name anptest -p 80:80 rjarow/alpine-nginx-phpfpm
```

Proceed to check http://<yourip> for a web response (it will be an error as no html files exist currently). The -p options can be changed to your liking like -p <desiredport>:80


### Deploying

There are a few ways to deploy this, I would suggest using the -v volume mount to point to your application that needs to be hosted. The image looks for /usr/html for content.

The nginx user in the container uses uid/guid 100:101 so we must chown the hosted files as such, please be careful doing this on the host system.

**I plan on updating with a new version soon that will allow us to specify the uid/guid to run the container as.**

Example:

```
docker create --name apphosting -p 80:80 -v $(pwd):/usr/html rjarow/alpine-nginx-phpfpm
chown 100:101 $(pwd)
docker container start apphosting
```

I would highly suggest running this behind an nginx reverse proxy, a really easy, automatic one by [jwilder](https://github.com/jwilder/nginx-proxy) is my preferred method.

Example:
```
docker network create nginx-proxy
docker create -d -p 80:80 --name nginx-proxy --net nginx-proxy -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy
docker create --name apphosting --net nginx-proxy -e VIRTUAL_HOST=yourdomain.com -v $(pwd):/usr/html rjarow/alpine-nginx-phpfpm
chown 100:101 $(pwd)
docker start nginx-proxy
docker start apphosting

```
You can then run other domains behind this same proxy just by specifying the VIRTUAL_HOST variable when creating/running your docker container.

Another suggestion would be to run this with docker-compose to tie into a sql database server.

Additional PHP Modules:

```

List of available modules in Alpine Linux, not all these are installed.

In order to install a php module do, (leave out the version number i.e. -5.6.11-r0

docker exec <image_id> apk add <pkg_name>
docker restart <image_name>
Example:

docker exec <image_id> apk add php7-soap
docker restart <image_name>
php7-common
php7-pdo_sqlite
php7-pear
php7-ftp
php7-imap
php7-mysqli
php7-json
php7-mbstring
php7-soap
php7-litespeed
php7-sockets
php7-bcmath
php7-opcache
php7-dom
php7-zlib
php7-gettext
php7-fpm
php7-intl
php7-openssl
php7-session
php7-mcrypt
php7-pdo_mysql
php7-embed
php7-xmlrpc
php7-wddx
php7-dba
php7-ldap
php7-xsl
php7-exif
php7-pdo_dblib
php7-bz2
php7-pdo
php7-pspell
php7-sysvmsg
php7-gmp
php7-apache2
php7-pdo_odbc
php7-shmop
php7-ctype
php7-phpdbg
php7-enchant
php7-sysvsem
php7-sqlite3
php7-odbc
php7-pcntl
php7-calendar
php7-xmlreader
php7-snmp
php7-zip
php7-posix
php7-iconv
php7-curl
php7-doc
php7-gd
php7-xml
php7-dev
php7-cgi
php7-sysvshm
php7-pgsql
php7-tidy
php7-pdo_pgsql
php7-phar
php7-mysqlnd
```

## Built With

* [Atom](https://atom.io/) - My favorite editor
* [Docker](https://docker.com) - Obviously?
* [Alpine](alpinelinux.org) - The tiny linux!
* [s6-overlay](https://github.com/just-containers/s6-overlay) - Making Docker play nice with processes.
* [Nginx](https://nginx.org/) - The webserver
* [PHP-FPM](https://php-fpm.org/) - PHP Support

## Authors

* **Rich Jarowski** - *Initial work* - [rjarow](https://github.com/rjarow)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Everyone making beautiful efficient Docker Images
* Hi Mom!

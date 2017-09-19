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

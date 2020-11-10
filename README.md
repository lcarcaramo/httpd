# Tags
> _Built from [`quay.io/ibmz/alpine:3.12`](https://quay.io/repository/ibmz/alpine?tab=info)_
-	[`2.4.46`](https://github.com/lcarcaramo/httpd/blob/master/s390x/2.4/alpine/Dockerfile) - [![Build Status](https://travis-ci.com/lcarcaramo/httpd.svg?branch=master)](https://travis-ci.com/lcarcaramo/httpd)

# What is httpd?

The Apache HTTP Server, colloquially called Apache, is a Web server application notable for playing a key role in the initial growth of the World Wide Web. Originally based on the NCSA HTTPd server, development of Apache began in early 1995 after work on the NCSA code stalled. Apache quickly overtook NCSA HTTPd as the dominant HTTP server, and has remained the most popular HTTP server in use since April 1996.

> [wikipedia.org/wiki/Apache_HTTP_Server](http://en.wikipedia.org/wiki/Apache_HTTP_Server)

![logo](https://raw.githubusercontent.com/docker-library/docs/8e367edd887f5fe876890a0ab4d08806527a1571/httpd/logo.png)

# How to use this image.

This image only contains Apache httpd with the defaults from upstream. There is no PHP installed, but it should not be hard to extend. On the other hand, if you just want PHP with Apache httpd see the [PHP image](https://hub.docker.com/_/php/) and look at the `-apache` tags. If you want to run a simple HTML server, add a simple Dockerfile to your project where `public-html/` is the directory containing all your HTML.

### Create a `Dockerfile` in your project

```dockerfile
FROM quay.io/ibmz/httpd:2.4.46
COPY ./public-html/ /usr/local/apache2/htdocs/
```

Then, run the commands to build and run the Docker image:

```console
$ docker build -t my-apache2 .
$ docker run -dit --name my-running-app -p 8080:80 my-apache2
```

Visit http://localhost:8080 and you will see It works!


### Configuration

To customize the configuration of the httpd server, first obtain the upstream default configuration from the container:

```console
$ docker run --rm quay.io/ibmz/httpd:2.4.46 cat /usr/local/apache2/conf/httpd.conf > my-httpd.conf
```

You can then `COPY` your custom configuration in as `/usr/local/apache2/conf/httpd.conf`:

```dockerfile
FROM quay.io/ibmz/httpd:2.4.46
COPY ./my-httpd.conf /usr/local/apache2/conf/httpd.conf
```

#### SSL/HTTPS

If you want to run your web traffic over SSL, the simplest setup is to `COPY` your `server.crt` and `server.key` into `/usr/local/apache2/conf/` and then customize the `/usr/local/apache2/conf/httpd.conf` by removing the comment symbol from the following lines:

```apacheconf
...
#LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
...
#LoadModule ssl_module modules/mod_ssl.so
...
#Include conf/extra/httpd-ssl.conf
...
```

The `conf/extra/httpd-ssl.conf` configuration file will use the certificate files previously added and tell the daemon to also listen on port 443. Be sure to also add something like `-p 443:443` to your `docker run` to forward the https port.

This could be accomplished with a `sed` line similar to the following:

```dockerfile
RUN sed -i \
		-e 's/^#\(Include .*httpd-ssl.conf\)/\1/' \
		-e 's/^#\(LoadModule .*mod_ssl.so\)/\1/' \
		-e 's/^#\(LoadModule .*mod_socache_shmcb.so\)/\1/' \
		conf/httpd.conf
```

The previous steps should work well for development, but we recommend customizing your conf files for production, see [httpd.apache.org](https://httpd.apache.org/docs/2.4/ssl/ssl_faq.html) for more information about SSL setup.

# License

View [license information](https://www.apache.org/licenses/) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in [the `repo-info` repository's `httpd/` directory](https://github.com/docker-library/repo-info/tree/master/repos/httpd).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.

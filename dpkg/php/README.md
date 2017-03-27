# like-php

## Building

1. Prepare an `Debian8 x86_64` server with the following package(s)
   installed:

    * libxml2-dev
    * libssl-dev
    * pkg-config
    * libcurl4-openssl-dev
    * libgd-dev
    * libicu-dev
    * libmcrypt-dev
    * g++

2. Put the php source package into the build folder (where this file
   resides in), the package must be named as `php-*.tar.bz2`, for
   example, `php-7.0.3.tar.bz2`.

3. As root, run `make` in the build folder. If successful, the deb package
   will be generated in the same folder named as `like-php_<version>.deb`,
   e.g. `like-php_7.0.3-30.80b9196.deb`.

## Configuration

After install the like-php deb package, you may start the php service
by running:

    # /etc/init.d/php-fpm start

Note that the service is NOT automatcally started by the installation
process.

**`WARNING`**: Instead of starting the php service directly, follow
the procedures described in the `SETUP.md` file in the `like-backend`
package.


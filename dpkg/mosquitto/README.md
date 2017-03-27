# like-mosquitto

1. Prepare an `UCloud Debian 7.0 64-bit` server with the following package(s) 
   installed:

    * build-essential
    * libcurl4-openssl-dev

2. Put the mosquitto source package into the build folder (where this file
   resides in), the package must be named as `mosquitto-*.tar.gz`, for
   example, `mosquitto-1.4.8.tar.gz`.

3. As root, run `make` in the build folder. If successful, the deb package
   will be generated in the same folder named as `like-mosquitto_<version>.deb`,
   e.g. `like-php_1.4.8-1.ffffff.deb`.

## Configuration

After install the like-mosquitto deb package, you may start the mosquitto service
by running:

    # /etc/init.d/mosquitto start

Note that the service is NOT automatcally started by the installation
process.

**`WARNING`**: Instead of starting the mosquitto service directly, follow
the procedures described in the `SETUP.md` file in the `like-backend`
package.


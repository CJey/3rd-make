# like-nginx

1. Prepare an `UCloud Debian 7.0 64-bit` server with the following package(s) 
   installed:

    * libpcre3-dev
    * libssl-dev

2. Put the nginx and module source packages into the build folder (where 
   this file resides in), the packages must be named as following:

    * main package: `nginx-*.tar.*`
    * echo module: `nginx_echo_module-*.tar.*`
    * upload module: `nginx_upload_module-*.tar.*`

3. As root, run `make` in the build folder. If successful, the deb package 
   will be generated in the same folder named as `like-nginx_<versions>.deb`,
   e.g. `like-nginx_1.8.1+0.45+2.1+2.2.0-30.7b0dd3c.deb`.

## Configuration

After install the like-nginx deb package, you may start the nginx service
by running:

    # start nginx

Note that the service is NOT automatcally started by the installation
process.

**`WARNING`**: Instead of starting the nginx service directly, follow
the procedures described in the `SETUP.md` file in the `like-backend`
package.

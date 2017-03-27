ifeq (0,$(shell git log|wc -l))
    LIST = 0
    GIT_HASH = 0000000
else
    LIST = $(shell git log --oneline . | wc -l)
    GIT_HASH = $(shell git log --oneline . | head -n 1 | awk '{print $$1}')
endif

THREADS = $(shell cat /proc/cpuinfo | grep ^processor | wc -l)

SRC_MAIN = $(shell ls nginx-*.tar.*)
SRC_MOD_ECHO = $(shell ls nginx_echo_module-*.tar.*)

DIR_MAIN = $(firstword $(subst .tar, ,$(SRC_MAIN)))
DIR_MOD_ECHO = $(firstword $(subst .tar, ,$(SRC_MOD_ECHO)))

VER_MAIN = $(lastword $(subst -, ,$(DIR_MAIN)))
VER_MOD_ECHO = $(lastword $(subst -, ,$(DIR_MOD_ECHO)))

VERSION = $(VER_MAIN)+$(VER_MOD_ECHO)-$(LIST)

FULLNAME = $(firstword $(subst .tar, ,$(SRC_MAIN)))

ROOT = /opt/like/nginx
TARGET = like-nginx_$(VERSION).$(GIT_HASH).deb
DEST_DIR = debian$(ROOT)

.PHONY: clean distclean build

$(TARGET): root prepare
	fakeroot dpkg-deb -b debian $(TARGET)

root:
	@if [ $$(id -u) -eq 0 ]; then \
        echo; \
        echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++"; \
        echo "+-------------------------------------------------------+"; \
        echo "+ Notice >>> Root privilege not required anymore!!! <<< +"; \
        echo "+-------------------------------------------------------+"; \
        echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++"; \
        echo; \
        exit 1; \
    fi

prepare: clean build
	cd "$(FULLNAME)" && DESTDIR=../debian make install
	rm -rf $(DEST_DIR)/html
	rm -rf $(DEST_DIR)/conf/nginx.conf
	@sed -i -e "s/^Version:.*$$/Version: $(VERSION)/" \
	    -e "s/^GitHash:.*$$/GitHash: $(GIT_HASH)/" \
        -e "s/^Installed-Size:.*$$/Installed-Size: `du -s --exclude=DEBIAN debian | cut -f 1`/" \
        debian/DEBIAN/control
	@find debian -type f |grep -v DEBIAN |xargs -I file md5sum file| sed s/debian\\//\\// > debian/DEBIAN/md5sums

clean:
	@sed -i -e "s/^Version:.*$$/Version:/" \
	    -e "s/^GitHash:.*$$/GitHash:/" \
        -e "s/^Installed-Size:.*$$/Installed-Size:/" \
        debian/DEBIAN/control
	rm -fr debian/opt
	rm -f  debian/DEBIAN/md5sums

distclean: clean
	rm -fr "$(FULLNAME)"
	rm -f "$(SRC_MAIN)"
	rm -f "$(SRC_MOD_ECHO)"
	rm -f *.deb

build:
	if [ -z "$(SRC_MAIN)" -o -z "$(SRC_MOD_ECHO)" ]; then \
	    echo "Hey, no usable source package found!!!"; \
        exit 2; \
    fi
	if [ ! -d "$(FULLNAME)" ]; then tar xf "$(SRC_MAIN)"; fi
	if [ ! -d "$(FULLNAME)"/mod_echo ]; then mkdir -p "$(FULLNAME)"/mod_echo && tar xf "$(SRC_MOD_ECHO)" -C "$(FULLNAME)"/mod_echo --strip-component=1; fi
	cd "$(FULLNAME)" && ./configure --prefix=$(ROOT) \
	    --add-module=mod_echo \
	    --with-http_addition_module \
	    --with-http_ssl_module \
        --with-http_v2_module
	cd "$(FULLNAME)" && make -j $(THREADS)

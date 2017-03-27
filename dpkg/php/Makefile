ifeq (0,$(shell git log|wc -l))
    LIST = 0
    GIT_HASH = 0000000
else
    LIST = $(shell git log --oneline . | wc -l)
    GIT_HASH = $(shell git log --oneline . | head -n 1 | awk '{print $$1}')
endif

THREADS = $(shell cat /proc/cpuinfo | grep ^processor | wc -l)

SOURCE = $(shell ls php-*.tar.bz2)
UP_VER = $(patsubst php-%.tar.bz2,%,$(SOURCE))
VERSION = $(UP_VER)-$(LIST)
FULLNAME = $(patsubst %.tar.bz2,%,$(SOURCE))
TEMPSUF=$(shell date +%s)
ROOT = /opt/like/php
TARGET = like-php_$(VERSION).$(GIT_HASH).deb

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
	#cd "$(FULLNAME)" && $(MAKE) install INSTALL_ROOT=../debian
	cd "$(FULLNAME)" && $(MAKE) INSTALL_ROOT=../debian install-cli install-fpm install-modules
	cd debian && rm -f .channels .depdb .depdblock .lock .filemap .registry
	rm -rf debian/opt/like/php/var
	rm -rf debian/opt/like/php/php
	@sed -i -e "s/^Version:.*$$/Version: $(VERSION)/" \
	    -e "s/^GitHash:.*$$/GitHash: $(GIT_HASH)/" \
	    -e "s/^Installed-Size:.*$$/Installed-Size: `du -s --exclude=DEBIAN debian | cut -f 1`/" \
	    debian/DEBIAN/control
	@find debian -type f |grep -v ^debian/DEBIAN/ |xargs -I file md5sum file| sed s/debian\\//\\// > debian/DEBIAN/md5sums

clean:
	@sed -i -e "s/^Version:.*$$/Version:/" \
	    -e "s/^GitHash:.*$$/GitHash:/" \
        -e "s/^Installed-Size:.*$$/Installed-Size:/" \
        debian/DEBIAN/control
	cd debian && rm -f .channels .depdb .depdblock .lock .filemap .registry
	rm -fr debian/opt
	rm -f  debian/DEBIAN/md5sums

distclean: clean
	rm -rf "$(FULLNAME)"
	rm -f "$(SOURCE)"
	rm -f *.deb

build:
	if [ -z "$(SOURCE)" ]; then \
        echo "Hey, no usable source package found!!!"; \
        exit 2; \
    fi
	if [ ! -d "$(FULLNAME)" ]; then tar xvf "$(SOURCE)"; fi
	cd "$(FULLNAME)" && ./configure --prefix=$(ROOT) \
	    --enable-fpm \
	    --enable-mbstring \
	    --enable-intl \
	    --enable-mysqlnd \
        --enable-sockets \
	    --with-mysqli=mysqlnd \
	    --with-pdo-mysql=mysqlnd \
	    --with-openssl \
	    --with-curl \
	    --enable-pcntl \
	    --enable-zip \
	    --with-zlib \
	    --with-gd \
	    --with-freetype-dir \
	    --with-jpeg-dir \
	    --with-png-dir \
        --with-mcrypt
	#cd "$(FULLNAME)" && $(MAKE) -j $(THREADS)
	cd "$(FULLNAME)" && $(MAKE) -j $(THREADS) build-modules cli fpm

ifeq (0,$(shell git log|wc -l))
    LIST = 0
    GIT_HASH = 0000000
else
    LIST = $(shell git log --oneline . | wc -l)
    GIT_HASH = $(shell git log --oneline . | head -n 1 | awk '{print $$1}')
endif

THREADS = $(shell cat /proc/cpuinfo | grep ^processor | wc -l)

SOURCE = $(shell ls mosquitto-*.tar.gz)
UP_VER = $(patsubst mosquitto-%.tar.gz,%,$(SOURCE))
VERSION = $(UP_VER)-$(LIST)
FULLNAME = $(patsubst %.tar.gz,%,$(SOURCE))

ROOT = /opt/like/mosquitto
TARGET = like-mosquitto_$(VERSION).$(GIT_HASH).deb
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
	install -d $(DEST_DIR)/conf
	install -d $(DEST_DIR)/bin
	install -d $(DEST_DIR)/sbin
	install -d $(DEST_DIR)/lib
	install -m 755 "$(FULLNAME)"/src/mosquitto $(DEST_DIR)/sbin/
	install -m 755 "$(FULLNAME)"/client/mosquitto_pub $(DEST_DIR)/bin/
	install -m 755 "$(FULLNAME)"/client/mosquitto_sub $(DEST_DIR)/bin/
	install -m 644 -s "$(FULLNAME)"/lib/libmosquitto.so.1 $(DEST_DIR)/lib/
	install -m 644 "$(FULLNAME)"/auth_plugin/mosquitto_auth_plugin_http.so $(DEST_DIR)/lib/auth_plugin_http.so
	install -m 644 "$(FULLNAME)"/mosquitto.conf $(DEST_DIR)/conf/mosquitto.conf.sample
	install -m 644 conf/mosquitto.conf $(DEST_DIR)/conf/
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
	rm -f "$(SOURCE)"
	rm -f *.deb

build: $(SOURCE)
	if [ -z "$(SOURCE)" ]; then \
	    exit 2; \
	else \
	    rm -fr "$(FULLNAME)" && tar xvf "$(SOURCE)" && cp -r auth_plugin "$(FULLNAME)" && cd "$(FULLNAME)" && \
        $(MAKE) -j $(THREADS) all LD_RUN_PATH=$(ROOT)/lib/ WITH_TLS=no WITH_TLS_PSK=no WITH_BRIDGE=no WITH_SRV=no WITH_UUID=no WITH_SOCKS=no WITH_EC=no && \
        cd auth_plugin && $(MAKE) -j $(THREADS); \
	fi

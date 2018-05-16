FROM alpine:3.6

RUN apk update \
  && apk add git ca-certificates openssl python build-base \
  && update-ca-certificates \
  && rm -rf /var/cache/apk/*

RUN mkdir /esp

## Install crosstool compiler
WORKDIR /esp
RUN wget https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz \
  && tar -xzf xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz \
  && rm xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz
## Include in path
ENV PATH=$PATH:/esp/xtensa-esp32-elf/bin

## Checkout esp-idf fix permissions and make sure we are on a supported version and setup env variables
RUN cd /esp && git clone --recursive https://github.com/espressif/esp-idf.git
RUN cd /esp/esp-idf && git checkout 3ede9f011b50999b0560683f9419538c066dd09e && git submodule update --init

## Define ESPIDF path
ENV ESPIDF = /esp/esp-idf

## Set environmental variables needed if flashing from images (only possible when the host system is linux)
#ENV PORT = /dev/ttyUSB0
#ENV FLASH_MODE = qio
#ENV FLASH_SIZE = 4MB
#CROSS_COMPILE = xtensa-esp32-elf

## Checkout MicroPython
RUN cd /esp && git clone --recursive https://github.com/micropython/micropython && cd micropython && git checkout v1.9.4

## Build MicroPython cross-compiler
RUN cd /esp/micropython && make -C mpy-cross

## Mak sure we have the Berkeley DB external dependency
RUN cd /esp/micropython && git submodule init lib/berkeley-db-1.xx && git submodule update

WORKDIR /esp/micropython/ports/esp32

VOLUME ["/esp/micropython/ports/esp32/build", "/esp/micropython/esp32/modules", "/esp/micropython/esp32/scripts"]

## Build base clean firmware image by default
CMD ["make"]

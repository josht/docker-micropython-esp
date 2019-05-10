FROM ubuntu:18.04

RUN apt-get update \
  && apt-get install -y gcc git curl make libncurses-dev flex bison gperf python python-pip python-setuptools python-serial \
    python-cryptography python-future python-pyparsing python-pyelftools \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && useradd --no-create-home esp

RUN mkdir /esp && chown esp:esp /esp
USER esp

## Install crosstool compiler
WORKDIR /esp
RUN curl -O https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz \
  && tar -xzf xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz \
  && rm xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz

## Checkout esp-idf fix permissions. Use release branch instead of commit hash so any fixes get pulled in as well.
RUN cd /esp && git clone https://github.com/espressif/esp-idf.git
RUN cd /esp/esp-idf && git checkout 30545f4cccec7460634b656d278782dd7151098e && git submodule update --init --recursive

## Checkout MicroPython
RUN cd /esp && git clone --recursive https://github.com/micropython/micropython && cd micropython && git checkout v1.10

## Set environmental variables before building, but after code is checked out
ENV ESPIDF=/esp/esp-idf
ENV PATH=/esp/xtensa-esp32-elf/bin:$PATH

## Set environmental variables needed if flashing from images (only possible when the host system is linux)
#ENV PORT = /dev/ttyUSB0
#ENV FLASH_MODE = qio
#ENV FLASH_SIZE = 4MB
#ENV CROSS_COMPILE = xtensa-esp32-elf-

## Build MicroPython cross-compiler
RUN cd /esp/micropython && make -C mpy-cross

## Mak sure we have the Berkeley DB external dependency
RUN cd /esp/micropython && git submodule init lib/berkeley-db-1.xx && git submodule update

WORKDIR /esp/micropython/ports/esp32

VOLUME ["/esp/micropython/ports/esp32/build", "/esp/micropython/ports/esp32/modules", "/esp/micropython/ports/esp32/scripts"]

## Build base clean firmware image by default
CMD ["make"]

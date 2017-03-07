FROM debian:jessie

RUN apt-get update \
  && apt-get install -y build-essential make unrar-free autoconf automake libtool gcc g++ gperf \
  flex bison texinfo gawk grep gettext libncurses-dev ncurses-dev libexpat-dev python python-serial sed \
  git unzip bash wget libtool-bin help2man python-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && useradd --no-create-home micropython

RUN mkdir /esp && chown -R micropython:micropython /esp
USER micropython

## Install crosstool compiler
RUN cd /esp && git clone -b xtensa-1.22.x https://github.com/espressif/crosstool-NG.git \
  && cd crosstool-NG && ./bootstrap && ./configure --prefix=$PWD && make install

## Build esp32 binary and include in path
RUN cd /esp/crosstool-NG && ./ct-ng xtensa-esp32-elf && ./ct-ng build && chmod -R u+w builds/xtensa-esp32-elf
ENV PATH=$PATH:/esp/crosstool-NG/builds/xtensa-esp32-elf/bin

## Checkout esp-idf fix permissions and make sure we are on a supported version and setup env variables
RUN cd /esp && git clone --recursive https://github.com/espressif/esp-idf.git
ENV ESPIDF=/esp/esp-idf
#ENV PORT=/dev/ttyUSB0
#ENV FLASH_MODE=qio
#ENV FLASH_SIZE=4MB
#ENV CROSS_COMPILE=/esp/xtensa-esp32-elf/bin
RUN cd /esp/esp-idf && git checkout 13dfb5568dcc30aa43363c49f7a4505d04036424 && git submodule update --init

RUN cd /esp && git clone --recursive https://github.com/micropython/micropython-esp32 micropython && cd micropython \
  && make -C mpy-cross

WORKDIR /esp/micropython/esp32

VOLUME ["/esp/micropython/esp32/build", "/esp/micropython/esp32/modules", "/esp/micropython/esp32/scripts"]

CMD ["make"]

FROM debian:jessie

RUN apt-get update \
  && apt-get install -y build-essential make unrar-free autoconf automake libtool gcc g++ gperf \
  flex bison texinfo gawk ncurses-dev libexpat-dev python python-serial sed \
  git unzip bash wget libtool-bin help2man python-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && useradd --no-create-home esp

RUN cd /

RUN git clone --recursive https://github.com/pfalcon/esp-open-sdk.git \
  && chown -R esp:esp /esp-open-sdk

USER esp

RUN cd esp-open-sdk && make STANDALONE=y

ENV PATH=/esp-open-sdk/xtensa-lx106-elf/bin:$PATH

WORKDIR /opt

VOLUME ["/opt"]

CMD ["make", "-C", "esp8266"]

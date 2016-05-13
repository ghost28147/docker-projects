FROM centos:6

RUN yum update -y

ENV DIR_FOR_SOURCES=/tmp/asterisk-sources

MAINTAINER ghost28147 <faschevskiy@vl.ru>

WORKDIR /tmp

RUN curl -sf -O \
    http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz \
    && mkdir -p ${DIR_FOR_SOURCES} \
    && tar -xf asterisk-11-current.tar.gz -C ${DIR_FOR_SOURCES} --strip-components=1 \
    && rm -f asterisk-11-current.tar.gz

# установка зависимостей
WORKDIR ${DIR_FOR_SOURCES}
RUN yum install -y subversion \
    && contrib/scripts/install_prereq install \
    && contrib/scripts/install_prereq install-unpackaged

# конфигурация сборки
RUN ./configure CFLAGS=-I/usr/include/libxml2/ \
    && make menuselect.makeopts \
    && menuselect/menuselect --enable CORE-SOUNDS-RU-WAV
    
RUN make \
    && make install \
    && make clean \
    && make samples

RUN yum clean all && rm -rf ${DIR_FOR_SOURCES} && ldconfig

VOLUME ["/etc/asterisk", "/var/log/asterisk", "/var/lib/asterisk", "/var/spool/asterisk"]

EXPOSE 10000-10040/udp 5060/udp 5038

WORKDIR /
CMD ["asterisk", "-f"]

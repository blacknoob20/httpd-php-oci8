FROM debian:buster

RUN apt update &&\ 
 apt install -y unzip \
 libaio-dev \
 build-essential \
 libssl-dev \
 libjansson-dev \
 libcurl4-gnutls-dev \
 libldb-dev \
 libldap2-dev \
 libnghttp2-dev \
 libxml2-dev \
 zlib1g-dev \
 libbz2-dev

RUN mkdir -p /opt/oracle/client/11.2/network/admin &&\
 mkdir -p /opt/apache2/2.4 &&\
 mkdir -p /opt/pcre/8.44 &&\
 mkdir -p /opt/apr/1.7 &&\
 mkdir -p /opt/apr-util/1.6 &&\
 mkdir -p /opt/expat/2.2 &&\
 mkdir -p /opt/php/5.6

# Instalación del cliente Oracle 11g
COPY ./instantclient* /opt/oracle/client/
COPY tnsnames.ora /opt/oracle/client/11.2/network/admin/

LABEL ora_install="Instalación InstantClient 11g"

ENV ORACLE_HOME=/opt/oracle/client/11.2
ENV LD_LIBRARY_PATH="$ORACLE_HOME:$LD_LIBRARY_PATH"
ENV PATH="$ORACLE_HOME:$PATH" 

RUN unzip /opt/oracle/client/instantclient-basic-linux.x64-11.2.0.4.0.zip -d /opt/oracle/client &&\
 mv /opt/oracle/client/instantclient_11_2/* $ORACLE_HOME/ &&\
 rm -R -f /opt/oracle/client/instantclient_11_2/ &&\
 rm -f /opt/oracle/client/instantclient-basic-linux.x64-11.2.0.4.0.zip &&\
 unzip /opt/oracle/client/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip -d /opt/oracle/client &&\
 mv /opt/oracle/client/instantclient_11_2/* $ORACLE_HOME/ &&\
 rm -R -f /opt/oracle/client/instantclient_11_2/ &&\
 rm -f opt/oracle/client/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip &&\
 unzip /opt/oracle/client/instantclient-sdk-linux.x64-11.2.0.4.0.zip -d /opt/oracle/client &&\
 mv /opt/oracle/client/instantclient_11_2/sdk/ $ORACLE_HOME/ &&\
 rm -R -f /opt/oracle/client/instantclient_11_2/ &&\
 rm -f opt/oracle/client/instantclient-sdk-linux.x64-11.2.0.4.0.zip &&\
 ln -s $ORACLE_HOME/libclntsh.so.11.1 $ORACLE_HOME/libclntsh.so &&\
 ln -s $ORACLE_HOME/libocci.so.11.1 $ORACLE_HOME/libocci.so

RUN echo $ORACLE_HOME > /etc/ld.so.conf.d/oracle-instantclient.conf && ldconfig

LABEL apache_install="Instalación de apache2"

# Instalación del PCRE - Perl Compatible Regular Expressions
COPY pcre-8.44.tar.gz /opt/pcre/

RUN tar xzf /opt/pcre/pcre-8.44.tar.gz -C /opt/pcre/ &&\
 rm -f /opt/pcre/pcre-8.44.tar.gz &&\
 cd /opt/pcre/pcre-8.44 &&\
 ./configure --prefix=/opt/pcre/8.44 &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/pcre/pcre-8.44

# Instalación de Expat XML parser
COPY expat-2.2.10.tar.gz /opt/expat/

RUN tar xzf /opt/expat/expat-2.2.10.tar.gz -C /opt/expat/ &&\
 rm -f /opt/expat/expat-2.2.10.tar.gz &&\
 cd /opt/expat/expat-2.2.10 &&\
 ./configure --prefix=/opt/expat/2.2/ &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/expat/expat-2.2.10/

# Instalación de APR - Apache Portable Runtime
COPY apr-1.7.0.tar.gz /opt/apr/
COPY apr-util-1.6.1.tar.gz /opt/apr-util/

RUN tar xzf /opt/apr/apr-1.7.0.tar.gz -C /opt/apr/ &&\
 rm -f /opt/apr/apr-1.7.0.tar.gz &&\
 cd /opt/apr/apr-1.7.0 &&\
 ./configure --prefix=/opt/apr/1.7/ &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/apr/apr-1.7.0/ &&\
 tar xzf /opt/apr-util/apr-util-1.6.1.tar.gz -C /opt/apr-util/ &&\
 rm -f /opt/apr-util/apr-util-1.6.1.tar.gz &&\
 cd /opt/apr-util/apr-util-1.6.1 &&\
 ./configure --prefix=/opt/apr-util/1.6 \
 --with-apr=/opt/apr/1.7/bin/apr-1-config \
 --with-expat=/opt/expat/2.2 \
 --with-ldap=ldap \
 --with-openssl \
 --with-crypto &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/apr-util/apr-util-1.6.1/

# Instalación de Apache2
COPY httpd-2.4.46.tar.gz /opt/apache2/

RUN tar xzf /opt/apache2/httpd-2.4.46.tar.gz -C /opt/apache2/ &&\
 rm -f /opt/apache2/httpd-2.4.46.tar.gz &&\
 cd /opt/apache2/httpd-2.4.46 &&\
 ./configure --prefix=/opt/apache2/2.4 \
 --enable-so \
 --with-pcre=/opt/pcre/8.44 \
 --with-apr=/opt/apr/1.7/bin/apr-1-config \
 --with-apr-util=/opt/apr-util/1.6/bin/apu-1-config &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/apache2/httpd-2.4.46

LABEL php_install="Instalación de PHP5"

# Instalación de PHP5
COPY php-5.6.40.tar.gz /opt/php/

RUN tar xzf /opt/php/php-5.6.40.tar.gz -C /opt/php/ &&\
 rm -f /opt/php/php-5.6.40.tar.gz &&\
 cd /opt/php/php-5.6.40 &&\
 cp php.ini-development /opt/php/5.6 &&\
 ./configure --prefix=/opt/php/5.6 \
 --with-apxs2=/opt/apache2/2.4/bin/apxs \
 --with-oci8=shared,instantclient,/opt/oracle/client/11.2 \
 --with-config-file-path=/opt/php/5.6 \
 --enable-mbstring \
 --enable-soap \
 --enable-cgi \
 --with-bz2 \
 --with-zlib \
 --enable-sockets \
 --enable-sysvsem \
 --enable-sysvshm \
 --enable-pcntl \
 --enable-mbregex \
 --with-mhash \
 --enable-zip \
 --with-pcre-regex \
 --enable-ftp &&\
 #--with-openssl &&\
 make && make install &&\
 cd / &&\
 rm -R -f /opt/php/php-5.6.40/ &&\
 echo "<?php phpinfo(); ?>" > /opt/apache2/2.4/htdocs/info.php

# Borrar librerías innecesarias
RUN apt --purge -y autoremove \
 unzip \
 build-essential
 #\
 #libssl-dev \
 #libjansson-dev \
 #libcurl4-gnutls-dev \
 #libldb-dev \
 #libldap2-dev \
 #libnghttp2-

CMD /opt/apache2/2.4/bin/apachectl -DFOREGROUND

# docker run -d -p 81:80 --name test plus
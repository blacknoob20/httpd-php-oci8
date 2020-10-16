FROM debian:buster

RUN apt remove -y nghttp2 &&\
 apt update &&\ 
 apt install -y unzip \
 libaio1 \
 libaio-dev \
 build-essential \
 libssl-dev \
 libjansson-dev \
 libcurl4-gnutls-dev \
 libldb-dev \
 libldap2-dev \
 nghttp2

RUN mkdir -p /opt/oracle/client/11.2/network/admin &&\
 mkdir -p /opt/apache2/2.4 &&\
 mkdir -p /opt/pcre/8.44 &&\
 mkdir -p /opt/apr/1.7/ &&\
 mkdir -p /opt/apr-util/1.6/ &&\
 mkdir -p /opt/expat/2.2

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
 ln -s $ORACLE_HOME/libclntsh.so.11.1 $ORACLE_HOME/libclntsh.so &&\
 ln -s $ORACLE_HOME/libocci.so.11.1 $ORACLE_HOME/libocci.so

RUN echo $ORACLE_HOME > /etc/ld.so.conf.d/oracle-instantclient.conf && ldconfig

LABEL apache_install="Instalación de apache2"

# Instalación del PCRE - Perl Compatible Regular Expressions
COPY pcre-8.44.tar.gz /opt/pcre/

RUN tar xzf /opt/pcre/pcre-8.44.tar.gz -C /opt/pcre/ &&\
 rm -f /opt/pcre/pcre-8.44.tar.gz &&\
 /opt/pcre/pcre-8.44/configure --prefix=/opt/pcre/8.44 && make && make install &&\
 rm -R -f /opt/pcre/pcre-8.44

# Instalación de Expat XML parser
COPY expat-2.2.10.tar.gz /opt/expat/

RUN tar xzf /opt/expat/expat-2.2.10.tar.gz -C /opt/expat/ &&\
 rm -f /opt/expat/expat-2.2.10.tar.gz &&\
 /opt/expat/expat-2.2.10/configure --prefix=/opt/expat/2.2/ && make && make install &&\
 rm -R -f /opt/expat/expat-2.2.10/

# Instalación de APR - Apache Portable Runtime
COPY apr-1.7.0.tar.gz /opt/apr/
COPY apr-util-1.6.1.tar.gz /opt/apr-util/

RUN tar xzf /opt/apr/apr-1.7.0.tar.gz -C /opt/apr/ &&\
 rm -f /opt/apr/apr-1.7.0.tar.gz &&\
 /opt/apr/apr-1.7.0/configure --prefix=/opt/apr/1.7/ && make && make install &&\
 rm -R -f /opt/apr/apr-1.7.0/ &&\
 tar xzf /opt/apr-util/apr-util-1.6.1.tar.gz -C /opt/apr-util/ &&\
 rm -f /opt/apr-util/apr-util-1.6.1.tar.gz &&\
 /opt/apr-util/apr-util-1.6.1/configure --prefix=/opt/apr-util/1.6 \
 --with-apr=/opt/apr/1.7/bin/apr-1-config \
 --with-expat=/opt/expat/2.2 \
 --with-ldap=ldap \
 --with-openssl \
 --with-crypto &&\
 make && make install &&\
 rm -R -f /opt/apr-util/apr-util-1.6.1/


# Instalación de Apache2
COPY httpd-2.4.46.tar.gz /opt/apache2/

RUN tar xzf /opt/apache2/httpd-2.4.46.tar.gz -C /opt/apache2/ &&\
 rm -f /opt/apache2/httpd-2.4.46.tar.gz &&\
 /opt/apache2/httpd-2.4.46/configure --prefix=/opt/apache2/2.4 \
 --enable-so \
 --with-pcre=/opt/pcre/8.44 \
 --with-apr=/opt/apr/1.7/bin/apr-1-config \
 --with-apr-util=/opt/apr-util/1.6/bin/apu-1-config &&\
 make && make install &&\
 rm -R -f /opt/apache2/httpd-2.4.46/

# Borrar librerías innecesarias
RUN apt purge -y unzip build-essential &&\
 apt autoremove -y

#CMD /opt/apache2/2.4/apachectl start
FROM debian:buster

RUN apt update && apt install -y unzip libaio1 libaio-dev

RUN mkdir -p /opt/oracle/client &&\
 mkdir -p /opt/oracle/client/11.2/network/admin

COPY instantclient-basic-linux.x64-11.2.0.4.0.zip /opt/oracle/client
COPY instantclient-sqlplus-linux.x64-11.2.0.4.0.zip /opt/oracle/client
COPY tnsnames.ora /opt/oracle/client/11.2/network/admin

RUN unzip /opt/oracle/client/instantclient-basic-linux.x64-11.2.0.4.0.zip -d /opt/oracle/client &&\
 mv /opt/oracle/client/instantclient_11_2/* /opt/oracle/client/11.2/ &&\
 rm -R -f /opt/oracle/client/instantclient_11_2/ &&\
 rm -f /opt/oracle/client/instantclient-basic-linux.x64-11.2.0.4.0.zip &&\
 unzip /opt/oracle/client/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip -d /opt/oracle/client &&\
 mv /opt/oracle/client/instantclient_11_2/* /opt/oracle/client/11.2/ &&\
 rm -R -f /opt/oracle/client/instantclient_11_2/ &&\
 rm -f opt/oracle/client/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip &&\
 ln -s /opt/oracle/client/11.2/libclntsh.so.11.1 /opt/oracle/client/11.2/libclntsh.so &&\
 ln -s /opt/oracle/client/11.2/libocci.so.11.1 /opt/oracle/client/11.2/libocci.so &&\
 apt remove -y unzip
 
RUN echo /opt/oracle/client/11.2 > /etc/ld.so.conf.d/oracle-instantclient.conf && ldconfig

ENV ORACLE_HOME=/opt/oracle/client/11.2
ENV LD_LIBRARY_PATH="$ORACLE_HOME:$LD_LIBRARY_PATH"
ENV PATH="$ORACLE_HOME:$PATH" 

#docker build -t plus .
#docker run -d -p 81:80 --name test plus

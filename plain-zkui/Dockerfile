FROM qnib/alplain-openjdk8

ARG MAVEN_VERSION="3.6.0"
ARG ZKUI_GIT_ZIP_URL=https://github.com/JacksonZheng/zkui/archive/master.zip
LABEL zkui.maven.version=${MAVEN_VERSION}
ENV ENTRYPOINTS_DIR=/opt/qnib/entry \
    M2_HOME=/usr/lib/mvn \
    ZKUI_PORT=9090 \
    ZK_SERVER=tasks.zookeeper:2181 \
    ZKUI_ADMIN_PW=manager \
    ZKUI_USER_PW=user
RUN apk --no-cache add wget curl libarchive-tools \
 && wget -qO - "http://ftp.unicamp.br/pub/apache/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz" | tar xfz - -C /opt/ \
 && mv "/opt/apache-maven-$MAVEN_VERSION" "$M2_HOME" \
 && ln -s "$M2_HOME/bin/mvn" /usr/bin/mvn \
 && wget -qO - ${ZKUI_GIT_ZIP_URL} |bsdtar xf - -C /opt/ \
 && mv /opt/zkui-master /opt/zkui \
 && apk --no-cache del wget libarchive-tools \
 && cd /opt/zkui && mvn clean install \
 && rm -rf /tmp/* /var/cache/apk/* /usr/lib/mvn /opt/zkui/config.cfg
RUN echo "grep zkSer /opt/zkui/config.cfg" >> /root/.bash_history
COPY opt/qnib/zkui/bin/start.sh /opt/qnib/zkui/bin/
COPY opt/qnib/entry/* /opt/qnib/entry/
COPY opt/qnib/zkui/conf/zkui.conf.orig /opt/qnib/zkui/conf/
RUN chmod 777 -R /opt/qnib/zkui/bin \
    && chmod 777 -R /opt/qnib/entry


HEALTHCHECK --interval=2s --retries=30 --timeout=1s \
  CMD curl -sI http://127.0.0.1:${ZKUI_PORT}

CMD ["/opt/qnib/zkui/bin/start.sh"]

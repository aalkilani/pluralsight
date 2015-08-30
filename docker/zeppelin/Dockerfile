FROM centos:centos6
MAINTAINER Ahmad Alkilani <https://github.com/aalkilani>

# System Prep
RUN \
  yum update -y \
  && yum clean all \
  && yum remove java \
  && yum remove jdk

# JAVA
ENV JAVA_HOME /usr/jdk1.8.0_31
ENV PATH $PATH:$JAVA_HOME/bin
RUN curl -sL --retry 3 --insecure \
  --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
    "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz" \
    | gunzip \
    | tar x -C /usr/ \
    && ln -s $JAVA_HOME /usr/java \
    && rm -rf $JAVA_HOME/man

# ZEPPELIN
RUN mkdir -p /zeppelin \
    && curl -SL http://apache.mirrors.lucidnetworks.net/incubator/zeppelin/0.5.0-incubating/zeppelin-0.5.0-incubating-bin-spark-1.4.0_hadoop-2.3.tgz \
    | tar -xzC /zeppelin --strip-components=2

WORKDIR /zeppelin
ENV ZEPPELIN_HOME         /zeppelin
ENV ZEPPELIN_CONF_DIR     $ZEPPELIN_HOME/conf
ENV ZEPPELIN_NOTEBOOK_DIR $ZEPPELIN_HOME/notebook
ENV ZEPPELIN_PORT         8988

EXPOSE 8988 8989

CMD ["bin/zeppelin.sh", "start"]
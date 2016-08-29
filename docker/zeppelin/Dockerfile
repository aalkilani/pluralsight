FROM tianon/centos:6.5
MAINTAINER Ahmad Alkilani <https://github.com/aalkilani>

# System Prep
RUN \
  yum clean all && \
  yum -y install tar zip wget curl bzip2 sudo && \
  wget https://github.com/stedolan/jq/releases/download/jq-1.5/jq-linux64 -O jq && \
  chmod +x ./jq && \
  cp jq /usr/bin && \
  mkdir -p /opt/zeppelin

# Setup
RUN \
# JAVA
  curl -sL --retry 3 --insecure \
  --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
    "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz" \
    | gunzip \
    | tar x -C /usr/

ENV JAVA_HOME=/usr/jdk1.8.0_60
ENV PATH $PATH:$JAVA_HOME/bin

# ZEPPELIN
RUN \
  ln -s $JAVA_HOME /usr/java && \
  rm -rf $JAVA_HOME/man && \
  cd /opt/zeppelin && \
  mirror=$(curl --stderr /dev/null https://www.apache.org/dyn/closer.cgi\?as_json\=1 | jq -r '.preferred') && \
  url="${mirror}zeppelin/zeppelin-0.6.1/zeppelin-0.6.1-bin-all.tgz" && \
  curl -sL --retry 3 ${url} | gunzip | tar x -C /opt/zeppelin --strip 1

# Pass these to docker run
#ENV HADOOP_CONF_DIR     /pluralsight/hadoop-2.6.0/etc/hadoop
#ENV MASTER              yarn-client
#ENV ZEPPELIN_JAVA_OPTS  "-Dhdp.version=2.3.1.0-2574"


WORKDIR /opt/zeppelin
ENV ZEPPELIN_HOME=/opt/zeppelin \
    ZEPPELIN_CONF_DIR=/opt/zeppelin/conf \
    ZEPPELIN_NOTEBOOK_DIR=/opt/zeppelin/notebook \
    ZEPPELIN_PORT=8988

EXPOSE 8988 8989

CMD ["bin/zeppelin.sh", "start"]

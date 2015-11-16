FROM tianon/centos:6.5
MAINTAINER Ahmad Alkilani <https://github.com/aalkilani>

# System Prep
RUN \
  yum update -y && \
  yum clean all && \
  yum remove java && \
  yum remove jdk && \
  yum -y install tar zip wget curl-devel expat-devel gettext-devel openssl-devel zlib-devel bzip2 sudo && \
  yum -y groupinstall 'Development Tools' && \
  yum -y install gcc perl-ExtUtils-MakeMaker && \
  yum -y remove git && \
  mkdir -p /home/zeppelin/prerequisites && groupadd -r zeppelin && useradd -s /bin/bash -r -m -d /home/zeppelin -g zeppelin zeppelin

# Setup Git
ENV PATH $PATH:/home/zeppelin/prerequisites/git/bin
RUN \
  cd /home/zeppelin/prerequisites && \
  wget https://github.com/git/git/archive/v2.4.8.tar.gz && \
  tar xzf v2.4.8.tar.gz && \
  cd git-2.4.8 && \
  make prefix=/home/zeppelin/prerequisites/git all && \
  make prefix=/home/zeppelin/prerequisites/git install

# JAVA
ENV JAVA_HOME /usr/jdk1.8.0_60
ENV PATH $PATH:$JAVA_HOME/bin
RUN curl -sL --retry 3 --insecure \
  --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
    "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz" \
    | gunzip \
    | tar x -C /usr/ \
    && ln -s $JAVA_HOME /usr/java \
    && rm -rf $JAVA_HOME/man

# MAVEN and NPM
ENV MAVEN_HOME /home/zeppelin/prerequisites/apache-maven-3.3.3
ENV PATH $PATH:$MAVEN_HOME/bin
RUN \
  cd /home/zeppelin/prerequisites/ && \
  wget ftp://mirror.reverse.net/pub/apache/maven/maven-3/3.3.3/binaries/apache-maven-3.3.3-bin.tar.gz && \
  tar -xf apache-maven-3.3.3-bin.tar.gz && \
  curl --silent --location https://rpm.nodesource.com/setup | bash - && \
  yum -y install nodejs && \
  npm install -g grunt-cli


# ZEPPELIN
RUN \
  cd /home/zeppelin && \
  git clone https://github.com/apache/incubator-zeppelin.git && \
  cd /home/zeppelin/incubator-zeppelin/zeppelin-web && grunt && \
  cd /home/zeppelin/incubator-zeppelin && \
  mvn clean package -Pcassandra-spark-1.5 -Dspark.version=1.5.2 -Dhadoop.version=2.6.0 -Phadoop-2.6 -Pyarn -DskipTests && \
  chown -R zeppelin /home/zeppelin

# Switch to zeppelin user
USER zeppelin

# Pass these to docker run
#ENV HADOOP_CONF_DIR     /pluralsight/hadoop-2.6.0/etc/hadoop
#ENV MASTER              yarn-client
#ENV ZEPPELIN_JAVA_OPTS  "-Dhdp.version=2.3.1.0-2574"


WORKDIR /home/zeppelin/incubator-zeppelin
ENV ZEPPELIN_HOME         /home/zeppelin/incubator-zeppelin
ENV ZEPPELIN_CONF_DIR     $ZEPPELIN_HOME/conf
ENV ZEPPELIN_NOTEBOOK_DIR $ZEPPELIN_HOME/notebook
ENV ZEPPELIN_PORT         8988

EXPOSE 8988 8989

CMD ["bin/zeppelin.sh", "start"]
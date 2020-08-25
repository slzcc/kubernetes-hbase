FROM slzcc/java:java-jdk-1.8.0.201 as jdk

FROM slzcc/sshd:latest

COPY --from=jdk /jdk1.8.0_201 /usr/local/jdk

ENV JAVA_HOME=/usr/local/jdk \
    JAVA_BIN=/usr/local/jdk/bin \
    JRE_HOME=/usr/local/jdk/jre

ENV TZ="Asia/Shanghai" \
    LANG="zh_CN.utf8"

RUN echo "Acquire::http::Proxy \"http://192.168.7.24:44551\";" > /etc/apt/apt.conf && \
    apt update && \
    apt install -y wget openssh-server supervisor vim net-tools apt-transport-https netcat && \
    apt-get install -y fonts-baekmuk fonts-nanum language-pack-zh-hans && \
    rm -rf /var/lib/apt/lists/* && \
    apt clean

ARG HBASE_VERSION=2.1.8

#RUN https_proxy=192.168.7.24:44551 wget -qO- https://www-us.apache.org/dist/hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz | tar zx -C /opt/
RUN https_proxy=192.168.7.24:44551 wget -qO- https://archive.apache.org/dist/hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz | tar zx -C /opt/

ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/hbase-$HBASE_VERSION/bin:/usr/local/jdk/bin

RUN update-rc.d ssh defaults 98 

RUN service ssh start && \
   ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa && \
    cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys && \
    chmod 600 -R ~/.ssh/authorized_keys && \
    ssh-keyscan -t rsa localhost -p 22 > ~/.ssh/known_hosts && \
    ssh-keygen -H -f ~/.ssh/known_hosts

RUN echo "" > /etc/apt/apt.conf

COPY start-supervisord /start-supervisord
COPY start-hbase /start-hbase
COPY check-ready /check-ready
COPY set-ssh-keyscan /set-ssh-keyscan
COPY set-ssh-config /set-ssh-config

ENV HBASE_VERSION=$HBASE_VERSION

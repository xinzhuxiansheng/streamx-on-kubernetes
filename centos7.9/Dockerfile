FROM centos:centos7.9.2009

RUN yum install -y sshd openssl passwd openssh-server vim net-tools wget less telnet groupinstall chinese-support

ADD ./jdk-8u291-linux-x64.tar.gz /usr/local
ADD ./apache-maven-3.8.4-bin.tar.gz /usr/local
ADD ./node-v14.18.1-linux-x64.tar.gz /usr/local
#COPY ./dockerinstall.sh /root

# 中文乱码
ENV LANG=en_US.UTF-8

# 配置环境变量
ENV NODE_HOME /usr/local/node-v14.18.1-linux-x64
ENV MAVEN_HOME /usr/local/apache-maven-3.8.4
ENV JAVA_HOME /usr/local/jdk1.8.0_291
ENV JRE_HOME /usr/local/jdk1.8.0_291/jre
ENV CLASSPATH .:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
ENV PATH $PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$MAVEN_HOME/bin:$NODE_HOME/bin

RUN echo -e "[docker-ce-edge] \n\ 
name=Docker CE Edge - \$basearch  \n\ 
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/\$basearch/edge  \n\
enabled=1 \n\
gpgcheck=1 \n\
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg" >> /etc/yum.repos.d/docker.repo

RUN yum install -y docker-ce



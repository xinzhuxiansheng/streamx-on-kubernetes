FROM streamxcentos:v0.0.1

RUN yum install -y sshd openssl passwd openssh-server vim net-tools wget less

ADD ./flink-1.14.0_2.11 /usr/local/flink-1.14.0_2.11
ADD ./streamx-console-service-1.2.1 /usr/local/streamx-console-service-1.2.1

# 测试环境k8s
RUN mkdir /root/.kube
COPY ./config_k8sprod /root/.kube/config

# 配置workspace
RUN mkdir /usr/local/streamx_workspace
# 要确保streamx配置了此路径

ENTRYPOINT [ "/bin/bash", "/usr/local/streamx-console-service-1.2.1/bin/startup.sh" ] 

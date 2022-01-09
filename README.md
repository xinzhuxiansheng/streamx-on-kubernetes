

## StreamX on Kubernetes

>本仓库相关依赖及安装包采用本地ADD方式build镜像, 方式*.tar.gz、flnk、streamx安装包过大，没有上传以上安装包，仅仅利用touch方式标识安装包，请clone后，自行替换安装包

## 镜像介绍

### centos7.9
系统环境，包含(maven,jdk,node)

```shell
# 打包系统镜像
docker build --no-cache -t="streamxcentos:v0.0.1" .

```





### streamx
streamx服务，包含(k8s config,flink安装包,streamx安装包), streamx镜像是基于centos7.9镜像，所以需要适时修改 `FROM streamxcentos:v0.0.1`

```shell
# 打包streamx服务镜像
docker build --no-cache -t="streamxapp:v0.0.1" .

# 推送仓库

```


**streamx涉及到修改**    
1.修改streamx.sh脚本，将日志通过Kubernetes STUDOUT输出日志，streamx利用Docker Daemon保证streamx后台运行
```shell
# ......省略上文

eval "\"$RUNJAVA\"" $JAVA_OPTS \
  -classpath "\"$APP_CLASSPATH\"" \
  -Dapp.home="\"${APP_HOME}\"" \
  -Dspring.config.location="\"${PROPER}\"" \
  -Djava.io.tmpdir="\"$APP_TMPDIR\"" \
  -Dpid="\"${APP_PID}\"" \
  com.streamxhub.streamx.console.StreamXConsole  

# ......省略下文       
```

2.根据用户环境对flink，streamx的配置文件修改


## Docker环境验证
利用docker in docker方式    
**参数介绍**    
```shell
#  docker in docker
1. -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker
# 挂在本地卷
2. -v 本地路径:/usr/local/streamx_workspace  
# 映射k8s的config配置的域名
3. --add-host test.apiserver.k8s.xxxx.com:xx.xx.82.15  
```

```shell
# 后台运行 -dit  docker in docker启动
docker run -dit -p 10005:10000 -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /Users/yiche/streamx_workspace_docker:/usr/local/streamx_workspace --add-host test.apiserver.k8s.xxxx.com:xx.xx.82.15   [imageId]

# 进入
docker exec -it [containerId] /bin/bash
```

## K8s验证  
以下是streamx.yaml相关内容介绍   

1. 增加hosts
```yaml
hostAliases:
  - ip: "10.xxx.xxx.xxx"
    hostnames:
    - "yzhou.mysql.com"
  - ip: "10.xxx.xxx.xx"
    hostnames:
    - "apiserver.xxxx.k8s.xxxx.com"
```

2. 创建namespace
```yaml
namespace: yzhou
```  


```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: streamxservice
  namespace: yzhou
spec:
  replicas: 1
  selector:
    matchLabels:
      app: streamxservice
  template:
    metadata:
      name: streamxservice
      labels:
        app: streamxservice
    spec:
      containers:
        - name: streamxservice
          image: hub.xxxx.com/streamx/streamxapp:vt0.0.1
          volumeMounts:
          - mountPath: /var/run/docker.sock
            name: docker-sock
          - mountPath: /usr/local/streamx_workspace
            name: streamxworkspace
          resources:
            limits:
              memory: 4096Mi
              cpu: 4
            requests:
              memory: 4096Mi
              cpu: 4
      volumes:
        - name: timezone
          hostPath:
            path: /usr/share/zoneinfo/Asia/Shanghai
        - name: docker-sock
          hostPath:
            path: /var/run/docker.sock
            type: Socket
        - name: streamxworkspace
          hostPath:
            path: /data/streamx_workspace
            type: DirectoryOrCreate
      hostAliases:
        - ip: "10.xxx.xxx.xxx"
          hostnames:
          - "yzhou.mysql.com"
        - ip: "10.xxx.xxx.xx"
          hostnames:
          - "apiserver.xxxx.k8s.xxxx.com"
---
apiVersion: v1
kind: Service
metadata:
  name: streamxservice-svc
  namespace: yzhou
spec:
  type: ClusterIP
  ports:
  - port: 10000
    targetPort: 10000
  selector:
    app: streamxservice
```

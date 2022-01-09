## docker打包镜像

```shell
# 打包系统镜像
docker build --no-cache -t="streamxcentos:v0.0.1" .

# 打包streamx服务镜像
docker build --no-cache -t="streamxapp:vd0.0.1" .

# push harbor
docker tag  streamxapp:vt0.0.1 hub.xxxx.com/streamx/streamxapp:vt0.0.1
docker push hub.xxxx.com/streamx/streamxapp:vt0.0.1

启动时添加hosts
--add-host pbs-test.apiserver.k8s.xxxx.com:xx.xx.82.15

```

## Docker验证
```shell
# 后台运行 -dit  docker in docker启动
docker run -dit -p 10005:10000 -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /Users/yiche/streamx_workspace_docker:/usr/local/streamx_workspace --add-host pbs-test.apiserver.k8s.xxxx.com:xx.xx.82.15 --add-host yzhou.mysql.com:192.168.199.155  5978883ae67b

# 进入
docker run -dit e0a474bf0e2c
docker exec -it d15b0dee3198 /bin/bash
```

## 创建ingress
```
sed -i 's/flink-yzhoutest-cluster0003/streamx-yzhou-mac-producer01/g' streamx-yzhou-mac-producer01.yaml
sed -i 's/原字符串/替换字符串/g' filename
kubectl -n yzhou apply -f streamxservice-svc.yaml
```


## 进入StreamX
```
kubectl -n yzhou
kubectl exec -it streamxservice-5d64d5d658-4vgz2  -n yzhou -- /bin/bash


kubectl -n yzhou apply -f xxx.yaml
kubectl -n yzhou delete -f xxx.yaml

```

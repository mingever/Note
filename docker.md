``` bash
service docker start
```

``` bash
docker exec -it redis /bin/bash
```



## 常用软件

### portainer

``` bash
docker run --name portainer -d -p 9000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer-ce
#本地挂在路径必须为/var/run/docker.sock，才可以连接上本地环境，不要改成别的
```


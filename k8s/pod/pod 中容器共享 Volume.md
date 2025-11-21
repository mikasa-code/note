
同一个 Pod 中的多个容器能够共享 Pod 级别的存储卷 Volume。Volume 可被定义各种类型，多个容器各自进行挂载操作，将一个 Volume 挂载为容器内需要的目录。


Pod 中的 Volume 在进行挂载的时候，默认的行为是**遮罩**，不想 docer 中一样使用 volume 时，会先将文件复制到 volume 中，这种行为更像是 bind mount


在下面的示例中包含两个容器，即 tomcat 容器和 busybox 容器。在 Pod 的配置中设置
名为 app-logs 的存储卷（Volume）
tomcat 容器向该存储卷中写入日志文件，busybox 容器从该存储卷中读取日志文件。

```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: volume-pod  
spec:  
  containers:  
  - name: tomcat  
    image: tomcat  
    ports:  
    - containerPort: 8080  
    volumeMounts:  
    - name: app-logs  
      mountPath: /usr/local/tomcat/logs  
  - name: busybox  
    image: busybox  
    command: ["sh", "-c", "tail -f /logs/catalina*.log"]  
    volumeMounts:  
    - name: app-logs  
      mountPath: /logs  
  volumes:  
  - name: app-logs  
    emptyDir: {}
```

`emptyDir: {}` 这个写法的含义其实非常朴素：**创建一个空目录作为 Volume，没有任何额外配置。**

把它想成 Kubernetes 给 Pod 开的一块“白纸空间”，Pod 里的容器可以在上面随便读写，直到 Pod 消亡。

1. `{}` 表示这个 emptyDir 使用默认配置。  
    默认配置意味着：只要 Pod 在运行，这块目录就一直存在；Pod 重启时会清空；节点重建时会丢失。
    
2. 它的生命周期绑定在 Pod 上，不会持久化，也不和外部存储打交道。
    
3. 它的存储介质默认是节点本地磁盘

如果想要持久化的存储最佳实践是使用 **PV + PVC !!！**

因为文件是共享的
Busybox 容器的启动命令力 “tail -f /logs/catalina*. Log”，就可以通过 kubectl logs 命
令查看 tomact 容器的日志
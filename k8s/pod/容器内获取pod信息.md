Kubernetes 在成功创建 Pod 之后，会为 Pod和容器设置一些额外的信息，例如 Pod 名称、Pod IP、Node IP、Label、Annotation、Pod 和容器的资源配置信息等。
在很多应用场景中，这些信息对容器内的应用来说都很有用，例如将 Pod 名称作为日志记录的一个字段来标识日志来源。
Kubernetes 提供了 DownwardAPI 机制，可将 Pod 和容器的某些元数据信息注入容器环境内，以便容器应用获取和使用。

### 环境变量的方式
下面的示例通过 Downward API 将 Pod 的 IP 地址、名称和所在命名空间注入容器的环
境变量中。
```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: dapi-envars-fieldref  
spec:  
  containers:  
    - name: test-container  
      image: busybox  
      command: [ "sh", "-c"]  
      args:  
      - while true; do  
          echo -en '\n';  
          printenv MY_NODE_NAME MY_POD_NAME MY_POD_NAMESPACE;  
          printenv MY_POD_IP MY_POD_SERVICE_ACCOUNT;  
          sleep 10;  
        done;
     # 注入进环境变量中  
      env:  
        - name: MY_NODE_NAME  
          valueFrom:  
            fieldRef:  
              # node 所在的名称
              fieldPath: spec.nodeName  
        - name: MY_POD_NAME  
          valueFrom:  
            fieldRef:  
              # pod 名称
              fieldPath: metadata.name  
        - name: MY_POD_NAMESPACE  
          valueFrom:  
            fieldRef:
              # 所在的命名空间
              fieldPath: metadata.namespace  
        - name: MY_POD_IP  
          valueFrom:  
            fieldRef:  
              # pod 所在的 ip
              fieldPath: status.podIP  
  restartPolicy: Never
```
查看 Pod 的日志，可以看到容器启动命令将环境变量的值打印了出来

### 使用挂载的方式 
通过 Volume 挂载方式可以将 Pod 或容器的配置信息以文件的形式挂载到容器
内
```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: test_pod
  labels:  
    zone: us-east-coast  
    cluster: test-cluster1  
    rack: rack-22  
  annotations:  
    build: two  
    builder: john-doe  
spec:  
  containers:  
    - name: client-container  
      image: busybox  
      command: ["sh", "-c"]  
      args:  
      - while true; do  
          if [[ -e /etc/podinfo/labels ]]; then  
            echo -en '\n\n'; cat /etc/podinfo/labels; fi;  
          if [[ -e /etc/podinfo/annotations ]]; then  
            echo -en '\n\n'; cat /etc/podinfo/annotations; fi;  
          sleep 5;  
        done;  
      volumeMounts:  
        - name: podinfo  
          mountPath: /etc/podinfo  
  volumes:  
    - name: podinfo  
      downwardAPI:  
        items:  
          - path: "labels"  
            fieldRef:  
              fieldPath: metadata.labels  
          - path: "annotations"  
            fieldRef:  
              fieldPath: metadata.annotations
```

系统会基于 volume 中各 item 的 path 名称生成文件。
根据上面的设置，系统将在容器内的/etc/podinfo 目录下生成 labels 文件和 annotations
文件，内容是对应的键值对
在 labels 文件中将包含 Pod 的全部 Label 列表，在 annotations 文件中将包含 Pod 的全部 Annotation 列表。
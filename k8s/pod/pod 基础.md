
 Kubernetes 中应用的最小管理单元，可以包含一个或多个容器
 它们在 Pod 内共享网络配置和存储卷配置。从逻辑上来说，一个 Pod 相当于一个主机，里面运行的一个或者多个容器在整体上是一个完整的应用系统。

在 pod 中除了可以包含主要的应用程序，还可以包含主应用容器启动之前的初始化容器，它们在运行结束后就停止了，通常用于为主应用容器提供初始的环境配置工作

一个容器通常有两种运行方式：长时间运行（服务型）；运行一次就结束（任务
型）


如果是一次性的容器，例如下面的 bash脚本：
```shell
echo("11")
```
Kubernetes 认为命令执行完，容器就运行结束，会立刻结束运行该容器。
如果为该容器设置的重启策略为 Always，则系统在监控到该容器运行结束后会创建一个新的容器继续运行。在这种情况下，容器会进行启动、停止、重建的无限循环。

如果是长时间允运行的应用程序来说，容器镜像的主进程在启动之后应该长时间运行。只有在资源不足或者系统出错时，才由 Kubernetes 根据健康检查机制重启容器。


pod 可以由多个不同的容器组合而成，如下
```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: redis-php  
  labels:  
    name: redis-php  
spec:  
  containers:  
  - name: frontend  
    image: my_frontend
    ports:  
    - containerPort: 80  
  - name: redis  
    image: redis
    ports:  
    - containerPort: 6379
```




![](assets/pod%20基础/file-20251119212956115.png)

属于同一个 Pod 的多个容器应用之间在相互访问时仅需通过 localhost 就可以通信。
my_frontend 中直接通过 localhost:6379 就可以访问



yaml 定义文件
```yaml
apiVersion: v1                  # Pod 的 API 版本
kind: Pod                       # 类型：Pod
metadata:
  name: super-demo-pod          # Pod 名称
  namespace: default            # 命名空间（不写默认 default）
  labels:                       # 标签，用于 selector / 分组 / 监控
    app: super-demo
    tier: backend
  annotations:                  # 注解，可放一些额外信息
    description: "A demo pod with everything included."

spec:
  nodeSelector:                 # NodeSelector：硬性要求调度到特定节点
    kubernetes.io/os: linux

  imagePullSecrets:             # 拉取私有镜像仓库的凭证
    - name: my-regcred

  containers:
    - name: app-container
      image: nginx:1.25
      imagePullPolicy: IfNotPresent

      ports:
        - name: http
          containerPort: 80

      env:                      # 普通环境变量
        - name: MODE
          value: "production"

      envFrom:                  # 批量导入 ConfigMap & Secret
        - configMapRef:
            name: demo-config
        - secretRef:
            name: demo-secret

      resources:                # 资源配额控制
        requests: 
          cpu: "200m"           # 单位毫核
          
          memory: "256Mi"
        limits:
          cpu: "1m"
          memory: "512Mi"

      livenessProbe:            # 存活探针：检查服务是否挂了
        httpGet:
          path: /
          port: http
        initialDelaySeconds: 10
        periodSeconds: 5
        timeoutSeconds: 2

      readinessProbe:           # 就绪探针：是否可对外提供流量
        httpGet:
          path: /
          port: http
        initialDelaySeconds: 5
        periodSeconds: 3

      startupProbe:             # 启动探针：帮助慢启动应用
        httpGet:
          path: /
          port: http
        failureThreshold: 30
        periodSeconds: 2

      volumeMounts:             # 将卷挂载到容器
        - name: config-volume
          mountPath: /etc/demo
        - name: log-volume
          mountPath: /var/log/demo

  volumes:
    - name: config-volume       # 从 ConfigMap 挂载配置文件
      configMap:
        name: demo-config

    - name: log-volume          # emptyDir：Pod 生命周期的临时目录
      emptyDir: {}

  restartPolicy: Always         # 重启策略（Pod 一般用 Always）
  dnsPolicy: ClusterFirst       # 默认 DNS 策略
  securityContext:              # Pod 级别的安全配置
    runAsUser: 1000
    runAsGroup: 1000
    fsGroup: 2000

  hostAliases:                  # 自定义 hosts（小场景调试时用）
    - ip: "127.0.0.1"
      hostnames:
        - local.test

```
应用部署的最佳实践是将应用所需的配置信息与应用程序分离
ConfigMap 用于保存应用程序运行时需要的配置数据，通过明文存储 key:value
ConfigMap 要与 pod 在一个命名空间才可以使用

### 创建 ConfigMap

示例文件
```yaml
apiVersion: v1  
kind: ConfigMap  
metadata:  
  # 必需字段：为 ConfigMap 指定一个唯一的名称。  
  # 您的 Pod 将通过此名称引用它。  
  name: example-app-config  
    
  # 可选字段：为资源添加标签，用于组织和选择。  
  # 例如，可以标记其所属的环境和应用。  
  labels:  
    app: example-app  
    environment: development  
  # 如果不指定，默认是 'default' 命名空间。  
    namespace: dev  
  
# 必需字段：ConfigMap 的核心部分，用于存储实际的配置数据。  
# 它可以包含两种数据类型：'data' 和 'binaryData'。  
data:  
  # 示例 1: 简单的键值对  
  log_level: "INFO"  
  server_port: "8080"  
  # 示例 2: 存储完整的配置文件内容 (通常是多行文本)。  
  # 使用 | 符号来表示多行文本（字面量块）。  
  # 这种方式可以保持文件内容中的换行和缩进格式。  
  database.properties: |  
    # 这是一个数据库配置文件的内容  
    db.host=mydb-service  
    db.user=app_user  
    db.password=secure_password_123  
    db.pool.size=10  
# # 注意：ConfigMap 中的数据大小限制在 1 MiB 以内。
```
在成功创建 ConfigMap 之后，可以通过一下命令传说
`kubectl describe configmap <cm-name>` 
`kubectl get configmap <cm-name> -o yaml`

### 基于现有的文件创建 ConfigMap

基于下面的文件进行创建
``` config
# server.conf
port=80
threads=5
timeout=30s
```
`kubectl create configmap my-server-config --from-file=server.conf`
直接生成一个键是文件名称，值是文件全部内容的文件
```yaml
# 创建出的 ConfigMap (部分内容)
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-server-config
data:
  server.conf: |
    port=80
    threads=5
    timeout=30s
```

---
基于文件夹中的所有文件创建
`kubectl create configmap app-settings --from-file=./config-files`
每个文件都会成为一个键，对应文件的内容作为值。

---
基于 env 文件进行创建
Env 是VAR=VALUE 的格式，等号两边不能有空格。
此时会将 env 文件中的键值对对应到 configmap 中的键值对

### 在 pod 中使用 ConfigMap
1. 将 ConfigMap 中的内容设置容器内的环境变量。
2. 通过 Volume 将 ConfigMap 中的内容挂载为容器内的文件。
---
将 configMap 内容变为环境变量
```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: tomcat  
spec:  
  containers:  
  - name: tomcat
    image: tomcat  
    # 打印环境变量
    command: [ "/bin/sh", "-c", "env | grep var" ]  
    env:  
    # 要设置的环境变量的名称
    - name: var1  
      valueFrom:  
        configMapKeyRef:  
          # configMap 的名称
          name: configMapName  
          # configMap 中 key 的名称
          key: key1  
    - name: var1  
      valueFrom:  
        configMapKeyRef:  
          name: cm-configMapName  
          key: key2  
  restartPolicy: Never
```
此时查看容器日志，就可以看到环境变量

---

将 config 文件内容挂载为 pod 中的文件
```
apiVersion: v1  
kind: Pod  
metadata:  
  name: cm-test-app  
spec:  
  containers:  
  - name: cm-test-app  
    image: kubeguide/tomcat-app:v1  
    ports:  
    - containerPort: 8080  
    volumeMounts:  
      # 引用 volumes 的名称
    - name: serverxml  
      # 文件存储的路径
      mountPath: /configfiles  
  volumes:  
  - name: serverxml  
    configMap:  
      # 引用 configMap 的名称
      name: cm-appconfigfiles  
      # 如果不指定 items 时，会生成以 key 为文件名的文件
      items:  
      - key: key-serverxml   # 引用 configMap 的 key
        path: server.xml     # 生成的文件名称
      - key: key-loggingproperties  
        path: logging.properties
```

### Pod 的阶段
Pod 的阶段是由 status.phase 体现的
可以使用 `kubectl get pods` 查看

|NAME|READY|STATUS|RESTARTS|AGE|
|---|---|---|---|---|
|my-pod-5f654f9d|1/1|**Running**|0|5d|
`STATUS` 列显示的就是 **`status.phase`** 字段的值，即 **`Running`**。
也可以使用 `kubectl describe` 查看 status 字段


各阶段的描述

| **阶段 (Phase)**     | **描述**                                                                 |
| ------------------ | ---------------------------------------------------------------------- |
| **挂起 (Pending)**   | Pod 已被 Kubernetes 系统接受，但有一个或多个容器镜像尚未创建。等待时间可能包括调度 Pod 的时间和通过网络下载镜像的时间。 |
| **运行中 (Running)**  | Pod 已被绑定到一个节点上，Pod 中的所有容器都已被创建。至少有一个容器正在运行，或者正处于启动或重启状态。               |
| **成功 (Succeeded)** | Pod 中的所有容器都被**成功终止**，并且**不会**再重启。通常用于执行一次性任务的 Pod。                     |
| **失败 (Failed)**    | Pod 中的所有容器都已终止，并且**至少有一个容器是因为失败终止**（例如，容器以非零状态退出或被系统终止）。               |
| **未知 (Unknown)**   | 因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败。                                |

---
### Pod Scheduling Gates (Pod 调度门)

Pod 一旦创建，Master 的调度器就会无限尝试为它寻找最适合的 Node，如果在某种情况下（例如 Pod 一直在等待某种外部资源就绪）很长时间内都无法完成调度，就会很浪费调度器的资源，在大规模集群环境下尤其会影响调度器的工作性能。

调度门允许外部控制器（如配额管理器或其他自定义控制器）将 Pod 标记为“尚未准备好进行调度”，直到外部条件满足为止。
- 当一个 Pod 存在调度门时，调度器会忽略该 Pod，不会浪费调度周期去尝试寻找合适的节点，直到所有调度门都被移除。
- **状态：** 当 Pod 存在调度门时，其状态会显示为 `SchedulingGated`。
示例
```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: my-gated-pod  
spec:  
  # ... 其他 Pod 配置 ...  schedulingGates:  
    - name: "example.com/quota-check"  
    - name: "another-gate-name"  
  containers:  
    - name: main-container  
      image: nginx
```
- `schedulingGates` 字段只能在 **创建 Pod 时** 初始化（客户端创建或准入控制期间修改）。
- **不允许** 在 Pod 创建后添加新的调度门。

清除调度门的工作通常由**外部控制器**负责。当外部条件满足（例如，配额检查通过、依赖资源已就绪等）时，外部控制器会通过 **PATCH/UPDATE** 操作**移除** Pod 对象中 `.spec.schedulingGates` 数组中的相应元素。

只有所有门都被移除后，Pod 才会进入正常的调度流程。
### 容器的状态
通过 ` kubectl describe pod<pod_name>` 命令可以查看 Pod 中每个容器的状态
1. 🚦 Waiting (等待中)
容器正在等待启动，通常是因为缺少必要的操作，例如：
- **正在拉取镜像：** 容器运行时正在从镜像仓库下载容器镜像。
    
- **Init 容器尚未完成：** 如果 Pod 定义了 Init 容器，则应用容器必须等待所有 Init 容器成功完成后才能启动。
    

 2. 🟢 Running (运行中)
容器已成功启动并且正在执行。
- 如果容器被配置了 **postStart** hook，那么 hook 已经执行完毕。
`postStart` hook 是 Kubernetes 为容器提供的**生命周期钩子（Lifecycle Hook）**之一。它允许您在容器**创建成功后（但不是完全运行起来之前）**立即执行特定的操作。
    
- 您可以使用 `kubectl logs` 查看容器的输出。
    

### 3. 🛑 Terminated (已终止)
容器已经开始执行并已终止。容器可能因为以下原因终止：
- **成功完成：** 容器中的进程正常退出（例如，批处理任务）。
- **失败退出：** 容器中的进程非零退出状态码退出，表示运行失败。
- **被 OOMKilled：** 容器被操作系统的 Out-Of-Memory (OOM) killer 杀死，因为它消耗了太多内存。
- 如果容器设置了 preStop回调钩子，kubelet 就会在容器进入 Terminated 状态之前执行回调方法。
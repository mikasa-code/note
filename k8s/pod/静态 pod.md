静态 Pod 是由 kubelet 管理的仅存在于 kubelet 所在 Node 上的 Pod，kubelet 负责监控由它创建的静态 Pod，并且在 Pod 失效时重建Pod。

kubelet 负责向 Master 注册在本Node 上创建的静态 Pod，管理员可以通过 API Server 的接口查看静态 Pod 的信息，只是不能通过 Master 管理和控制。所以，静态 Pod 也不能使用普通 Pod 可以使用的其他资源，例如 ConfigMap、Secret、ServiceAccount 等。


把静态 Pod 的 YAML 文件放到 kubelet 监控的目录里。默认是：
`/etc/kubernetes/manifests`
你只要把一个 Pod 的清单文件丢进去，比如：
`/etc/kubernetes/manifests/my-static-pod.yaml`

就会在当前的 node 创建一个静态的 pod
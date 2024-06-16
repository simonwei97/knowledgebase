---
hide:
  - footer
---
Kubernetes 中 Service 是 将运行在一个或一组 Pod 上的网络应用程序公开为网络服务的方法[^1]。

## 定义Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp  # pod 选择器
  ports:                           # 端口定义
    - name: http
      protocol: TCP                # 不指定默认 tcp
      port: 80
      targetPort: 9376
```

## 服务类型

**ClusterIP** (默认类型)

:  通过集群的内部IP公开Service

**NodePort**

:  通过每个节点的 IP 和静态端口公开Service。


**LoadBalance**

:  使用云平台的负载均衡器向外部公开Service

## 服务发现


对于在集群内运行的客户端，Kubernetes 支持两种主要的服务发现模式：**环境变量** 和 **DNS**。


[^1]: [Kubernetes 服务（Service）](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service)

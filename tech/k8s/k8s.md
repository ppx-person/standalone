livenessProbe

# k8s容器探测机制
* http get 如果探测收到响应，并且响应码是2xx, 3xx，则认为探测成功。
* 如果服务器没有响应或者返回错误则说明探测失败，容器将重启。
* tcp socke则成功
* exec 在容器内执行命令，状态码为0，则探测成功，否则探测失败容器重启

Liveness:       http-get http://:8080/ delay=0s timeout=1s period=10s #success=1 #failure=3

* delays: 0s表示在容器启动后立即开始探测
* timeout: 1s 必须在1s内进行响应，否则这探测记作失败
* period: 10s 每10s探测一次容器
* failure: 3 连接3次失败后重启容器]

# 每个探针都有以下三个结果的一种:
* Success: 容器通过了诊断
* Failure: 容器未通过诊断
* Unknown: 诊断失败，因此不应采取任何措施

# 问题1: 在prod内为容器配置活动性探针和就绪性探针。
* 主要是使用k8s使用liveness探针知识什么时候重新启动容器。
* k8s使用readyness探测器来了解容器何时准备开始接受流量。
* 当所有容器都准备就绪时，Pod被认为已准备就绪。此信号的一个用途是控制哪些Pod用作服务的后端。
当Pod未就绪时，它将从服务负载平衡器中删除。


# imagePullPolicy
* Always 是拉取pull
```
imagePullPolicy: Always
imagePullPolicy: IfNotPresent
imagePullPolicy: Never # 只使用本地镜像，从不拉取
```
* IfNotPresent
默认值，本地有则使用本地镜像，不拉取



# Volume 存储模型Volume
* 容器和Pod是短暂的
* 其含义是它们的生命周期可能很短，会被频繁地销毁和创建。容器销毁时，保存在容器内部文件系统中的数据都会被清除。

* 本质上，k8s volume是一个目录，这一点与Docker volume类似。当Volume被mount到Pod, Pod中的所有容器都可以访问这个Volumne.
k8s Volume也支持多种backend类型，emptyDir hostPah ...

###	emptyDir
* emptyDir是最基础的Volume类型。一个emptyDir Volume是Host上的一个空目录。且无需指定宿主机上对应的目录，k8s会自动分配一个随机的目录。
* emptyDir Volume对于容器来说是持久的，对于Pod则不是。当Pod从节点删除时，Volume的内容也会被删除。但如果只是容器被销毁而Pod还在，则Volumne不受影响。
* Pod中的所有容器都可以共享Volume，它们可以指定各自的mount路径



# k8s之Ingress-nginx原理及配置
在k8s中，服务和Pod的IP地址仅可以在集群网络中使用，对于集群外的应用是不可以见的。为了使外部的应用...
* NodePort LoadBalancer Ingress

# Ingress-nginx介绍
* ingress-nginx-controller:根据用户编写的ingress规则(创建的ingress的yaml文件)
动态的去更改nginx服务的配置文件，并且reload重载使其生效（是自动化的，通过lua脚本来实现）
* ingress资源对象：将Nginx的配置抽象成一个Ingress对象，每添加一个新的Server资源对象只需写一个新的Ingress规则的yaml文件即可
（或修改已存在的ingress规则的yaml文件）

# Ingress-nginx可以解决什么问题?
1) 动态配置服务
	如果按照传统方式，当新增加一个服务时，我们可能需要在流量入口加一个反向代理指向我们新的k8s服务。
	如用Ingress-nginx,只需要配置好这个服务，当服务启动时，会自动注册到Ingress中，不需要额外的操作。
2) 减少不必要的端口映射

# Ingress-nginx工作原理
1) ingress controller通过和kubernetes api交互，动态去感知集群中ingress规则变化。
2) 然后读取它，按照自定义的规则，规则就是写明了哪个域名对应哪个service，生成一段nginx配置
3) 再写到nginx-ingress-controller的pod里，这个Ingress controller的pod里运行着一个Nginx服务，
   控制器会把生成的nginx配置写入/etc/nginx.conf文件中，
4) 然后reload一下使配置生效，对此达到域名分别配置和动态更新的问题





# kubernetes资源类别介绍
资源对象: Pod、Deployment、Job、CronJob...
配置对象：Node、Namespace、Service、ConfigMap、Ingress...
存储对象：Volume、Persistent Volume
策略对象：SecurityContext、ResourceQuota、LimitRange



# pod
* pod共享相同的IP地址和端口空间
pod中的容器运行的多个进程需要注意不能绑定到相同的端口号。
一个pod中的所有容器也都具有相同的loopback网络接口，因此容器可以通过localhost与同一pod中的其他容器进行通信
# pod中的容器
思想：每个容器只安装一个进程，然后多个或一个容器属于一个pod。然后这个pod下的容器可以通过volume的方式共享磁盘
也就是说，应该把整个pod看作虚拟机，然后每个容器相当于运行在虚拟机的进程。
# 将多层应用分散到多个pod中
虽然可以把多个容器放在同一个pod下，但是应该根据应用将容器分布到不同的pod中。
* 每个pod是部署在固定的node上的，将前端、后端应用部署在同一个pod里发挥不出集群的作用
* 当需要进行节点扩容的时候，如果前后端在一个pod里，扩容一个pod后就有两个前端、后端，这样多出的前端是没有意义的而且很有难度
# 何时在pod使用多个容器
* 一般情况下建议单容器pod
* 多容器需要再时扩缩容，是否必须一起运行，代表的是一个主体不是多个独立的组件

Pod是k8s创建或部署的最小/最简单的基本单位，一个Pod代表集群上正在运行的一个进程。


# Pod和Controller




# k8s的五种控制器
* 内建了很多controller，这些相当于一个状态机，用来控制Pod的具体状态和行为
* 
































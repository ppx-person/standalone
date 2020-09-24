wget 
 https://dl.k8s.io/v1.9.3/kubernetes-client-linux-amd64.tar.gz

 tar -zxvf kubernetes-client-linux-amd64.tar.gz

 cd kubernetes/client/bin

 chmod +x ./kubectl

 sudo mv ./kubectl /usr/local/bin/kubectl
 


# 安装
```
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
$ setenforce 0
$ yum install -y kubelet kubeadm kubectl
$ systemctl enable kubelet && systemctl start kubelet
```



curl -Lo minikube https://github.com/kubernetes/minikube/releases/download/v1.6.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
W


curl -Lo minikube https://github.com/kubernetes/minikube/releases/download/v1.13.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/



```
sudo useradd deng -m -s /sbin/nologin  

sudo groupadd docker
sudo usermod -aG docker deng
newgrp docker 
切换用户，执行启动命令： minikube start --driver=docker


service docker start

 minikube start --image-mirror-country=cn  --registry-mirror=https://hub.daocloud.io/

```


# 开启Kubernetes dashboard服务
```
minikube dashboard --url
```
# 开启kube-proxy端口映射，使其可以远程访问
```
kubectl proxy --port=40785 --address='0.0.0.0' --accept-hosts='^.*' &
```

http://129.204.59.139:40785/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/clusterrole?namespace=default


# 标签
* 标签跟资源是多对多关系
* 一个资源有多个标签，可以实现不同维度的管理
* 可以使用标签选择器来指定使用哪些标签

1.组成
shell 中开启 kubectl 命令自动补全
```
yum install bash-completion -y
echo "source <(kubectl completion bash)" >> ~/.bashrc
```



# hello
hello-application.yaml
```
apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: hello-world
	spec:
	  selector:
	    matchLabels:
	      run: load-balancer-example
	  replicas: 2
	  template:
	    metadata:
	      labels:
	        run: load-balancer-example
	    spec:
	      containers:
	        - name: hello-world
	          image: registry.cn-hangzhou.aliyuncs.com/aliyun_google/google-sample-node-hello:1.0
	          ports:
	            - containerPort: 8080
	              protocol: TCP
```
kubectl apply -f ./hello-application.yaml
>> deployment.apps/hello-world created
kubectl get deployment
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   2/2     2            2           3m55s

暴露应用访问入口
kubectl expose deployment hello-world --type=NodePort --name=node-service

[deng@VM_0_14_centos ~]$ kubectl get service node-service
NAME           TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
node-service   NodePort   10.106.115.157   <none>        8080:30561/TCP   75s

kubectl get service node-service
kubectl describe service node-service







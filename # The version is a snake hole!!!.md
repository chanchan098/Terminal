# The versions and network is a snake hole!!!

# Kubeadm cluster setup

## Requirements

* Linux, here is ubuntu 20.04(LTS).
    * To select a appropriate version, because of docker engine required.
        * [system requirements](https://docs.docker.com/engine/install/#server)
    * Trun off the swap.
* Container runtime, here is docker engine.
    * Notice that this docker engine uses differs to ordinary docker, it has an interface layer 
    called cri-dockerd with which communicate to docker, pls check out official doc.
        * [cri-dockerd](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes/#docker)
* Installation for kubeadm, kubectl, kubelet, see also official documentation.
* [Cgroup details](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/)
* Images pulling needed to be retaged
    * using proxy: crictl pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.6
    * example: ctr -n k8s.io i tag registry.aliyuncs.com/google_containers/coredns:v1.10.1 registry.k8s.io/coredns/coredns:v1.10.1
* Installation for network plugin, here is calico
    * [plugins](https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy)
* Checking statuses, see also ##Command line references

## Command line references
```

#To query images
kubeadm config images list

# Pull an image with cn proxy 
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.6

# crictl usages
almost to docker

# Set tag with ctr
ctr -n k8s.io i tag registry.aliyuncs.com/google_containers/coredns:v1.10.1 registry.k8s.io/coredns/coredns:v1.10.1

https://blog.csdn.net/xingzuo_1840/article/details/128833061

#init 
setenforce 0

swapoff -a

kubeadm init  --v=5 

Deprecated: kubeadm init   --image-repository registry.aliyuncs.com/google_containers   --v=5 

kubectl apply -f http://192.168.0.116:8089/resource/weave-daemonset-k8s.yaml

kubectl apply -f http://192.168.0.116:8089/resource/kube-flannel.yml

kubectl apply -f http://192.168.0.116:8089/resource/calico.yaml

# Network enhancements 
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system  #生效

# To query status
kubectl get pods -n kube-system

kubectl get pod --all-namespaces

kubectl get nodes

kubectl cluster-info

docker info | grep Cgroup

cat /var/lib/kubelet/config.yaml | grep cgroup


# log
cat /var/log/syslog | tail -20

kubectl logs etcd-x-virtualbox -n kube-system

journalctl -xeu kubelet

docker tag coredns/coredns:1.10.1 registry.k8s.io/coredns/coredns:v1.10.1

crictl tag registry.aliyuncs.com/google_containers/coredns:v1.10.1 registry.k8s.io/coredns/coredns:v1.10.1

# reset
kubeadm reset;

rm -rf $HOME/.kube/;ipvsadm --clear

docker tag registry.k8s.io/kube-apiserver:v1.28.3 registry.k8s.io/kube-apiserver:v1.6.5

rm -rf /etc/kubernetes;rm -rf /var/lib/kubelet;rm -rf /var/lib/etcd;rm -rf /var/lib/cni;rm -rf /var/run/calico;rm -rf /var/run/flannel;rm -rf /usr/local/bin/kubectl;rm -rf /usr/local/bin/kubeadm;rm -rf /usr/local/bin/kubelet;

sudo apt-get install -y apt-transport-https ca-certificates curl gpg;curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg;echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get install -y kubelet kubeadm kubectl;sudo apt-mark hold kubelet kubeadm kubectl

# docker proxy

vim /etc/systemd/system/docker.service.d/http-proxy.conf 


```
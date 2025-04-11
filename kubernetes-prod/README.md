# 14. Подходы к развертыванию и обновлению production-grade кластера 

## Домашнее задание  
1) Создать кластер с использованием kubeadm  
2) Понимать как обновить кластер до нужной версии kubernetes с использованием kubeadm  
3) Создатþ кластер с исполþзованием kubespray  

## Выполняемые команды
1) Обычное задание

На control plane сервере:
```
KUBERNETES_VERSION=v1.31 && \
sudo bash -c '
  curl -fsSL https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/ /" > /etc/apt/sources.list.d/kubernetes.list && \
  curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" > /etc/apt/sources.list.d/cri-o.list && \
  swapoff -a && sed -i '\''/ swap / s/^/#/'\'' /etc/fstab && \
  echo "br_netfilter" >> /etc/modules-load.d/modules.conf && \
  sed -i '\''s/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/g'\'' /etc/sysctl.conf && \
  curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
' && \
sudo apt update && \
sudo apt install -y cri-o kubelet kubeadm kubectl && \
sudo apt-mark hold kubelet kubeadm kubectl && \
sudo apt upgrade -y && \
crio --version && kubelet --version && kubeadm version && kubectl version --client && \
sudo reboot now

sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket=unix:///var/run/crio/crio.sock

mkdir -p $HOME/.kube && \
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config && \
sudo chown $(id -u):$(id -g) $HOME/.kube/config && \
kubectl create ns kube-flannel && \
kubectl label --overwrite ns kube-flannel pod-security.kubernetes.io/enforce=privileged && \
helm repo add flannel https://flannel-io.github.io/flannel/ && \
helm install flannel flannel/flannel --values values.yaml -n kube-flannel && \
kubectl -n kube-system patch deployment coredns --type json -p '[{"op":"add","path":"/spec/template/spec/tolerations/-","value":{"key":"node.cloudprovider.kubernetes.io/uninitialized","value":"true","effect":"NoSchedule"}}]'


```
На Worker серверах:
```
KUBERNETES_VERSION=v1.31 && \
sudo bash -c '
  curl -fsSL https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/ /" > /etc/apt/sources.list.d/kubernetes.list && \
  curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" > /etc/apt/sources.list.d/cri-o.list && \
  swapoff -a && sed -i '\''/ swap / s/^/#/'\'' /etc/fstab && \
  echo "br_netfilter" >> /etc/modules-load.d/modules.conf && \
  sed -i '\''s/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/g'\'' /etc/sysctl.conf
' && \
sudo apt update && \
sudo apt install -y cri-o kubelet kubeadm kubectl && \
sudo apt-mark hold kubelet kubeadm kubectl && \
sudo apt upgrade -y && \
crio --version && kubelet --version && kubeadm version && kubectl version --client && \
sudo reboot now

sudo kubeadm join 10.130.0.3:6443 --token <tocken> \
        --discovery-token-ca-cert-hash <hash>
```

Маркируем:
```
kubectl label node k8s1-w1 node-role.kubernetes.io/worker=worker && \
kubectl label node k8s1-w2 node-role.kubernetes.io/worker=worker && \
kubectl label node k8s1-w3 node-role.kubernetes.io/worker=worker
```

Обновление кластера:
На control plane:
```
KUBERNETES_VERSION=v1.32 && \
sudo bash -c '
  curl -fsSL https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/ /" > /etc/apt/sources.list.d/kubernetes.list
' && \
sudo apt-mark unhold kubeadm kubelet && \
sudo apt-cache madison kubeadm | tac && \
sudo apt update && sudo apt update kubeadm=1.32.2 && \


sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.32.2
sudo apt install kubelet=1.32.2
sudo apt-mark hold kubeadm kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl get nodes
```

на Worker-ах:
```
KUBERNETES_VERSION=v1.32 && \
sudo bash -c '
  curl -fsSL https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/'"$KUBERNETES_VERSION"'/deb/ /" > /etc/apt/sources.list.d/kubernetes.list
' && \
sudo apt-mark unhold kubelet && \
sudo apt update && sudo apt update kubelet=1.32.2 && \
sudo apt-mark hold kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl drain controlplane --ignore-daemonsets
```

Уничтожить кластер:
```
sudo kubeadm reset
rm $HOME/.kube/config
```
Не удаляет (не очищает) внешний ecdt.


```
KUBERNETES_VERSION=v1.31

curl -fsSL https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" | tee /etc/apt/sources.list.d/kubernetes.list

CRI-O (или не использовать, если мы выберем containerd)
curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" | tee /etc/apt/sources.list.d/cri-o.list

sudo apt update && sudo apt upgrade -y && sudo reboot now
sudo swapoff -a && sudo sed -i '/ swap / s/^/#/' /etc/fstab
echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf
sudo modprobe overlay && sudo modprobe br_netfilter
echo 'br_netfilter' | sudo tee -a /etc/modules-load.d/k8s.conf
sudo sysctl --system

--- или так ---
Подключаем модуль ядра br_netfilter

echo "br_netfilter" >> /etc/modules-load.d/modules.conf
Разрешаем переадресацию пакетов между разными сетями

sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/g' /etc/sysctl.conf
Перезагружаем узел для применения настроек

reboot
----
sudo apt install -y apt-transport-https ca-certificates curl containerd kubelet kubeadm kubectl
#Отключаем автоматическое обновление для только что установленных пакетов
sudo apt-mark hold kubelet kubeadm kubectl

sudo mkdir -p /etc/containerd && containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
sudo systemctl restart containerd && sudo systemctl enable containerd --now


На мастере
sudo apt install -y kubectl
sudo snap install helm --classic
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

На Воркерах
kubeadm join 192.168.1.100:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:...

Установка Калико
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
Установка CNI плагина Flannel
kubectl create ns kube-flannel
kubectl label --overwrite ns kube-flannel pod-security.kubernetes.io/enforce=privileged
helm repo add flannel https://flannel-io.github.io/flannel/
# Для успешного запуска плагина вам необходимо установить значение podCidr аналогично значению networking.podSubnet в файле InitConfiguration.yaml
helm install flannel flannel/flannel --values values.yaml -n kube-flannel
kubectl -n kube-system patch deployment coredns --type json -p '[{"op":"add","path":"/spec/template/spec/tolerations/-","value":{"key":"node.cloudprovider.kubernetes.io/uninitialized","value":"true","effect":"NoSchedule"}}]'

Перед началом установки нам необходимо в ручную провести инициализацию рабочих узлов кластера
kubectl taint nodes <имя первого рабочего узла> node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule-
kubectl taint nodes <имя второго рабочего узла> node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule-


Проверка
kubectl get nodes -o wide
kubectl get pods -A

Обновление:
KUBERNETES_VERSION=v1.32
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" | tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update 
sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.32.3
sudo apt install kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl get nodes

Дрейним ноды по очереди и обновляем, после чего проверяем версии всех нод
KUBERNETES_VERSION=v1.32
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" | tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker1

kubectl drain kubeadm-worker2 --ignore-daemonsets
KUBERNETES_VERSION=v1.32
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/deb/ /" | tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker2

kubectl drain kubeadm-worker3 --ignore-daemonsets
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update && sudo apt install kubeadm kubelet
sudo systemctl daemon-reload && sudo systemctl restart kubelet
kubectl uncordon kubeadm-worker3

kubectl get nodes -o wide
```

2) Задание со *
sudo apt install pssh -y
ssh-keygen -t ed25519
ssh-copy-id user@server1
ssh-copy-id user@server2
user@server2" > hosts.txt
parallel-ssh -i -h hosts.txt "hostname"

```
sudo apt update && sudo apt upgrade -y && sudo reboot now

```
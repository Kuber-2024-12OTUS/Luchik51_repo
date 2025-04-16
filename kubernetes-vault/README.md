# 11. Хранилище секретов для приложения. Vault.

## Домашнее задание  
1) Установить в кластер Hashicorp Vault в HA режиме и научиться его конфигурировать.
2) Понимать как работает хранилище секретов, как создавать секреты в нем, роли и политики доступа
3) Установить External Secret Operator и настроить его для получения секретов, хранящихся в Vault

## Описание
**HashiCorp Vault** — это инструмент для безопасного хранения секретов, управления доступом и шифрования данных. В Kubernetes Vault интегрируется как централизованное хранилище секретов, предоставляя:

* Динамическое создание секретов (например, временных creds для БД).
* Шифрование данных через Transit Engine.
* Аутентификацию через Kubernetes Service Accounts.
* Автоматическое ротирование и отзыв секретов.  
[Git Vault](https://github.com/hashicorp/vault)  
[Helm Vault](https://github.com/hashicorp/vault-helm/tree/main)  



**HashiCorp Consul** — это service mesh и сервис обнаружения (service discovery) для распределённых систем. В Kubernetes Consul обеспечивает:
* Key-Value Store (распределённое хранилище конфигов).
* Service Discovery (автоматическое обнаружение сервисов через DNS или HTTP API).
* Service Mesh (mTLS, трафик-контроль через sidecar-прокси Envoy).
* Health Checking (мониторинг состояния нод и сервисов).  
[Git Consul](https://github.com/hashicorp/consul-k8s)  

[Helm Consul](https://github.com/hashicorp/consul-k8s/tree/main/charts/consul)  
 


**External Secrets Operator** — это Kubernetes-оператор, который автоматически синхронизирует секреты из внешних систем (например, AWS Secrets Manager, HashiCorp Vault, Azure Key Vault, Google Secret Manager) в Kubernetes Secrets. Упрощает управление секретами в Kubernetes без ручного копирования. Оператор синхронизирует данные и поддерживает их актуальность.  
[Описание External Secrets Operator](https://external-secrets.io/latest/)  
[Git External Secrets Operator](https://github.com/external-secrets/external-secrets)  
[Helm External Secrets Operator](https://external-secrets.io/latest/introduction/getting-started/)  




## Основные дейсвия
helm pull oci://registry-1.docker.io/bitnamicharts/vault
helm upgrade --install consul oci://registry-1.docker.io/bitnamicharts/consul -f consul-values.yaml --namespace consul --create-namespace
helm upgrade --install vault oci://registry-1.docker.io/bitnamicharts/vault -f vault-values.yaml --namespace vault --create-namespace
helm upgrade --install external-secrets external-secrets/external-secrets -n vault --create-namespace 

kubectl get pods --namespace "vault" -l app.kubernetes.io/instance=vault

kubectl exec -it vault-server-0 -n vault -- vault operator init
Unseal Key 1: 3LeGVodYk24SwjK3dHKtEDlsadGhH/Sljm792ed18w2m
Unseal Key 2: +ni2YPq8CQn54ilAO3nUjiUqOyhGw+kgK0lbfXUpzrjZ
Unseal Key 3: HNsohgdTrA+meocQcKO8hjbXp1tE1V9KH3H+EchLcjiZ
Unseal Key 4: jADsXfM8Iq2tnS9IvPFYHGdpOV4xgTIHcohRt1XAMQDV
Unseal Key 5: CvztsgJyASyz0nyHFl8o6RCArUnvlclj8sah2VMlcfEu

Initial Root Token: hvs.RsI9sUkfZrZmTf6vrWgNQ5OF

Распечатываем хранилище 
kubectl exec -it vault-server-0 -n vault -- vault operator unseal 3LeGVodYk24SwjK3dHKtEDlsadGhH/Sljm792ed18w2m &&\
kubectl exec -it vault-server-0 -n vault -- vault operator unseal +ni2YPq8CQn54ilAO3nUjiUqOyhGw+kgK0lbfXUpzrjZ &&\
kubectl exec -it vault-server-0 -n vault -- vault operator unseal jADsXfM8Iq2tnS9IvPFYHGdpOV4xgTIHcohRt1XAMQDV &&\
kubectl exec -it vault-server-1 -n vault -- vault operator unseal 3LeGVodYk24SwjK3dHKtEDlsadGhH/Sljm792ed18w2m &&\
kubectl exec -it vault-server-1 -n vault -- vault operator unseal +ni2YPq8CQn54ilAO3nUjiUqOyhGw+kgK0lbfXUpzrjZ &&\
kubectl exec -it vault-server-1 -n vault -- vault operator unseal jADsXfM8Iq2tnS9IvPFYHGdpOV4xgTIHcohRt1XAMQDV &&\
kubectl exec -it vault-server-2 -n vault -- vault operator unseal 3LeGVodYk24SwjK3dHKtEDlsadGhH/Sljm792ed18w2m &&\
kubectl exec -it vault-server-2 -n vault -- vault operator unseal +ni2YPq8CQn54ilAO3nUjiUqOyhGw+kgK0lbfXUpzrjZ &&\
kubectl exec -it vault-server-2 -n vault -- vault operator unseal jADsXfM8Iq2tnS9IvPFYHGdpOV4xgTIHcohRt1XAMQDV
kubectl exec -it vault-server-2 -n vault -- vault operator raft list-peers
http://10.2.80.26:8200


kubectl port-forward -n vault service/vault-server 30002:8200
kubectl port-forward -n consul service/consul-ui 30002:80

**img**
![img](img/img.png)
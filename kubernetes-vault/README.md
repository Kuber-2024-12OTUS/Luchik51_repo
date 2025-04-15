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
* Service Discovery (автоматическое обнаружение сервисов через DNS или HTTP API).
* Service Mesh (mTLS, трафик-контроль через sidecar-прокси Envoy).
* Health Checking (мониторинг состояния нод и сервисов).
* Key-Value Store (распределённое хранилище конфигов).
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

kubectl get pods --namespace "vault" -l app.kubernetes.io/instance=vault

kubectl exec -it vault-server-0 -n vault -- vault operator init
Unseal Key 1: HbMwZP0ZxfUIX10jSbzR0T64z8116SYpkbePWvd8oG4q
Unseal Key 2: 9SQ0XvuVAp7nptQ5BBLla1DP/gZvzM481XL8YjjjgWnw
Unseal Key 3: S6gQrxPaDZu/uHT8O4/lS6Vzg57HA+0h6cL3S1VNseDJ
Unseal Key 4: n02jE80G66XkXyS5WJ6zXlvxJUCTt1NQLnTaW5f/9L5E
Unseal Key 5: zEuq5EZ2UfQ7jIrGK1z2+AivzJLq+rJseNG4NYcj+7pR

Initial Root Token: hvs.IjMblG4eptzcSwYRtxEUEQpM

Распечатываем хранилище 
kubectl exec -it vault-server-0 -n vault -- vault operator unseal HbMwZP0ZxfUIX10jSbzR0T64z8116SYpkbePWvd8oG4q &&\
kubectl exec -it vault-server-0 -n vault -- vault operator unseal 9SQ0XvuVAp7nptQ5BBLla1DP/gZvzM481XL8YjjjgWnw &&\
kubectl exec -it vault-server-0 -n vault -- vault operator unseal zEuq5EZ2UfQ7jIrGK1z2+AivzJLq+rJseNG4NYcj+7pR

**img**
![img](img/img.png)
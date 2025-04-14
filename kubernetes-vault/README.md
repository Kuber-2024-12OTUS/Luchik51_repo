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

 
**img**
![img](img/img.png)
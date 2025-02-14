# 9. Мониторинг приложения в кластере


## Домашнее задание  
1) Установить в кластер и научиться пользоваться prometheus-operator 
2) Научиться инструментировать ваше приложение и собирать с него метрики в Prometheus-формате 

**Что сделано:**  
Файлы скопирвоаны с ДЗ kubernetes-templating, но отключены kafka - очень много ресурсов потребляло и не хватало памяти для запуска подов.


**Для запуска:**
```
minikube delete
minikube start
kubectl label nodes minikube web-server=nginx
minikube addons enable ingress


kubectl apply -f storageClass.yaml
kubectl apply -f pvc.yaml
helmfile sync
```

**Проверяем:**
```

```
![mysql-connect](img/mysql-connect.png)  


**Диагностика:**  
 
```
minikube addons enable metrics-server
kubectl top nodes
kubectl top pods -A
minikube start --cpus=4 --memory=8192
minikube start --memory=max --cpus=max

kubectl describe node minikube
kubectl delete all --all -n prod
kubectl delete all --all -n homework
kubectl delete serviceaccount monitoring -n homework
kubectl get ConfigMap -n homework 
kubectl delete ConfigMap html-config -n homework
kubectl delete ClusterRole metrics-reader
kubectl delete ClusterRoleBinding metrics-access
kubectl get all -A
kubectl get ingress -A
```
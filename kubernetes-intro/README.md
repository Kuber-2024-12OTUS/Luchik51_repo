# 2. Знакомство с Kubernetes, основные понятия и архитектура

## Миникуб: 
[Оффициальная страница](https://kubernetes.io/ru/docs/tasks/tools/install-minikube/)

[Установщик в Windows](https://github.com/kubernetes/minikube/releases/latest/download/minikube-installer.exe)

Запуск: в CMD. Основные команды:
```
minikube start
minikube status
minikube stop
```

Публикация Pod:
```
kubectl apply -f namespace.yaml
kubectl apply -f pod.yaml
```
Проверка работы Pod:
```
kubectl get pod -n homework
```
Диагностика
```
kubectl logs web-server -c init-config -n homework
kubectl describe pod web-server -n homework
```

Проверка работы веб сервера:
```
kubectl exec web-server -n homework -- curl http://localhost:8000
```
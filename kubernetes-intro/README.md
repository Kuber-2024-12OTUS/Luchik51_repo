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

Публикация приложения:
```
kubectl apply -f namespace.yaml
kubectl apply -f pod.yaml
```

Проверка работы веб сервера:
```
kubectl exec web-server -- curl http://localhost:8000
```
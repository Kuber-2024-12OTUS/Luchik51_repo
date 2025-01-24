# 3. Управление жизненным циклом и взаимодействием pod в Kubernetes

## Домашнее задание
Kubernetes controllers. ReplicaSet, Deployment, DaemonSet

**Цель:**
В данном домашнем задании вы научитесь формировать Replicaset, Deployment для своего приложения. Научатся управлять обновлением своего приложения. Так же научатся использовать механизм Probes для проверки работоспособности своих приложений.


**Публикация Deployment:**
```
kubectl apply -f deployment.yaml -n homework
kubectl apply -f namespace.yaml
```
Проверка работы Pod:
```
kubectl get deployment -n homework
kubectl get pod -n homework
```
Все поды должны быть запущены и готовы (Ready 1/1)

Задача со звездочкой *
Запуск deployment на определенных нодах.
Список доступных нодов можно получить так:
```
kubectl get nodes
```
nodeName: minikube
В рамках Minikube у нас только одна нода - minikube. Только на ней запуститься deployment. Если указать не правльное название. то будет ожидать появления нужной ноды.


**Диагностика**
```
kubectl logs web-server -c init-config -n homework
kubectl describe pod web-server -n homework
kubectl exec -it nginx-deployment-58f44f7446-67k62 -c nginx -n homework -- /bin/sh
```

**Проверка работы веб сервера:**
```
kubectl exec nginx-deployment-7474bb6797-cbkd4 -c nginx -n homework -- curl http://localhost:8000
```
Обнаружил, что "-c nginx" выбираем "слой" - контейнер по имени.

Справочно: лимиты в deployment указаны, т.к. подствечивало предупреждение в VS Code.
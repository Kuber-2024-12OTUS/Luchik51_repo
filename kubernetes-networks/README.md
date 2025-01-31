# 4. Сетевая подсистема и сущности Kubernetes (ДЗ-3)

## Домашнее задание
Научится создавать и конфигурировать Service и Ingress.  

Документация:
[Про Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)  
[Про сервисы](https://kubernetes.io/docs/concepts/services-networking/service/)  

**Цель:**
1) Научиться создавать и конфигурировать объекты типа Service  
2) Научится использовать объекты типа Service для взаимодействия между приложениями в кластере  
3) Получить представление об объекте типа Ingress, научится создавать и конфигурировать объекты этого типа.  


**Для запуска приложения надо:**  
Установить ingress-контроллер:  
```
minikube addons enable ingress
```

Если с нуля:
Проставить метку на ноду
kubectl label nodes minikube web-server=nginx


Добавить в c:\Windows\System32\drivers\etc\hosts
```
127.0.0.1 homework.otus
```
В отдельной консоли запустить туннель для проброса портов с миникуба на локальную машину:  
[Описание minikube tunnel](https://minikube.sigs.k8s.io/docs/commands/tunnel/)
```
minikube tunnel
```

Запускаемся:
```
kubectl apply -f .\namespace.yaml -n homework
kubectl apply -f .\deployment.yaml -n homework
kubectl apply -f .\service.yaml -n homework
kubectl apply -f .\ingress.yaml -n homework
```
Добавить в Hosts домен:
```
kubectl get ingress --all-namespaces
# в поле ADDRESS появился 172.19.221.196. Его прописываем в hosts файл.
```

Для задачи со * (звездочкой):  
```
kubectl delete -f .\ingress.yaml -n homework
kubectl apply -f .\ingress-star.yaml -n homework
```

Скриншот, что получилось:  
![homework.otus](img/homework.otus.png)

Диагностика:
```
Проверить метки:
kubectl get pods -n homework --show-labels
# проверить статус ingress
kubectl get pods -n ingress-nginx
# Если не стартует.
kubectl label nodes minikube minikube.k8s.io/primary=true
# Проверить работоспособность сервиса и служб
kubectl get svc -n homework
# Проверить работоспособность отдавать страничку через айпи service
kubectl exec nginx-deployment-fc47f888f-ltpn4 -c nginx -n homework -- curl http://10.104.80.214:8000
# Если понадобавиться удалить деплоймент
kubectl delete -f .\deployment.yaml -n homework
```
# 4. Сетевая подсистема и сущности Kubernetes (ДЗ-3)

## Домашнее задание
В данном вý науùитесþ создаватþ и конфигурироватþ Service и
Ingress.  

Документация:
[Про Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)  
[Про сервисы](https://kubernetes.io/docs/concepts/services-networking/service/)  

**Цель:**
1) Науùитþсā создаватþ и конфигурироватþ обüектý типа
Service  
2) Науùитþсā исполþзоватþ обüектý типа Service длā
взаимодействиā между приложениāми в кластере  
3) Полуùитþ представление об обüекте типа Ingress,
науùитþсā создаватþ и конфигурироватþ обüектý ÿтого
типа.  

**Для запуска приложения надо: ** 
Установить ingress-контроллер:  
```
minikube addons enable ingress
kubectl get pods -n ingress-nginx
# Если не стартует.
kubectl label nodes minikube minikube.k8s.io/primary=true
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

kubectl delete -f .\deployment.yaml -n homework
kubectl apply -f .\namespace.yaml -n homework
kubectl apply -f .\deployment.yaml -n homework
kubectl apply -f .\service.yaml -n homework
kubectl apply -f .\ingress.yaml -n homework
kubectl get svc -n homework
kubectl get ingress --all-namespaces
# в поле ADDRESS появился 172.19.221.196. Его прописываем в hosts файл.

Диагностика:
Проверить метки:
kubectl get pods -n homework --show-labels
kubectl exec nginx-deployment-fc47f888f-ltpn4 -c nginx -n homework -- curl http://10.104.80.214:8000
kubectl get svc -n homework
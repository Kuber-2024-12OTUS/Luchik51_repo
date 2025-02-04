# 4. Сетевая подсистема и сущности Kubernetes (ДЗ-3)

## Домашнее задание
1) Научится создавать в кластере объекты, описывающие  персистентные хранилища.  
2) Научиться создавать объект ConfigMap и монтировать его как volume  
3) Получить представление об объекте StorageClass и механизме provisioning для PV  

Документация:  
[Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)  
[Persistent Volumes (PV и PVC)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)  
[Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)  
[ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)  



**Для запуска приложения надо:**  
Установить ingress-контроллер:  
```
minikube addons enable ingress
```

Если с нуля:  
Проставить метку на ноду  
```
kubectl label nodes minikube web-server=nginx
```

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
kubectl apply -f .\cm.yaml -n homework
kubectl apply -f .\pvc.yaml -n homework
```
Добавить в Hosts домен:
```
kubectl get ingress --all-namespaces
# в поле ADDRESS появился 172.19.221.196. Его прописываем в hosts файл.
```

Проверяем:  
http://homework.otus/homework/conf - предложит скачать файл, в котором будет содержимое из cm.yaml

Для задачи со * (звездочкой):  
```
# выводит конфигурацию стандартного StorageClass
kubectl get sc standard -o yaml
kubectl apply -f .\star-storageClass.yaml
kubectl apply -f .\star-pvc.yaml -n homework
kubectl delete -f .\star-deployment.yaml -n homework

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
# 7. Шаблонизация манифестов приложения, использование Helm. Установка community Helm charts (ДЗ-6)

## Домашнее задание
1) Научится создавать собственные helm-chart ит деплоить их в кластер k8s  
2) Научиться устанавливать community helm chart из публичных репозиториев.  
3) Научиться использовать helmfile для описания релизов


kubectl apply -f .
kubectl get all -n homework
kubectl debug pod/web-deployment-5d4558d5c5-r4w5v -n homework --image=ubuntu --target=nginx -it -- bash
Внутри пода выполняем:
```
# Смотрим процессы
ps aux
# Находим где же у нас nginx
ls -laR /proc/1/root/ 2>/dev/null | grep -i nginx
# Смотрим содержимое папки
ls -la /proc/1/root/etc/nginx/

apt update && apt install tcpdump -y

```  
```
#
kubectl get all -n homework -o wide
#
kubectl get nodes
#
kubectl debug node/sd-k8s4-w2 -it --image=busybox
ls -al /host/
ls -al /host/var/log/pods/
ls -al /host/var/log/pods/homework_web-deployment-5d4558d5c5-r4w5v_aa60c6b5-122e-42eb-ac5c-0f5ff16978c7/nginx/
tail /host/var/log/pods/homework_web-deployment-5d4558d5c5-r4w5v_aa60c6b5-122e-42eb-ac5c-0f5ff16978c7/nginx/0.log
```
*
kubectl debug pod/web-deployment-5d4558d5c5-r4w5v -n homework --image=ubuntu --target=nginx -it -- bash
apt update && apt install strace -y && strace -p 1
ps aux
strace -p 1

Strace пока не победил, нашел тут информацию.
https://github.com/otus-kuber-2019-06/SOMikhaylov_platform/blob/master/docs/kubernetes-debug.md

helmfile kafka 
![helmfile kafka](img/helmfile_kafka.png) 

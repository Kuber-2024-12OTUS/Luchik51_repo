# 3. Управление жизненным циклом и взаимодействием pod в Kubernetes

kubectl run mysql-client --image=mysql:8.0 -it --rm --restart=Never -- /bin/bash
mysql -h mysql-sample.default.svc.cluster.local -u <username> -p<password> -D <database-name>
mysql -h mysql-sample.default.svc.cluster.local -u <username> -p<password>
mysql-sample.default.svc.cluster.local
kubectl logs <controller-pod-name> -n <controller-namespace>
kubectl get mysql -n mysql1
kubectl describe mysql mysql-db1 -n mysql1
kubectl get mysql -n mysql1
kubectl get mysql -n mysql



Проверьте, есть ли CR в кластере:

bash
Copy
kubectl get mysql --all-namespaces
Если CR есть, удалите их:

bash
Copy
kubectl delete mysql --all --all-namespaces
После удаления всех CR попробуйте снова удалить CRD:

bash
Copy
kubectl delete crd mysqls.otus.homework

Выполните команду:

bash
Copy
kubectl patch crd mysqls.otus.homework -p '{"metadata":{"finalizers":[]}}' --type=merge
После этого попробуйте удалить CRD:

bash
Copy
kubectl delete crd mysqls.otus.homework
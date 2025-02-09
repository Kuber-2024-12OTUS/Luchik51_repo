kubectl get ClusterRole -n homework
kubectl delete ClusterRole metrics-reader -n homework
kubectl get ClusterRoleBinding -n homework
kubectl delete ClusterRoleBinding metrics-access -n homework

kubectl apply -f .\storageClass.yaml
kubectl apply -f .\pvc.yaml -n homework


helm delete otus -n homework
helm install otus ./otus -n homework --create-namespace
kubectl delete all --all -n homework

kubectl describe pod/nginx-deployment-86988bf9d-6xq5n -n homework
kubectl get pods/nginx-deployment-5f484b877f-r6qkd -n homework -o yaml



probes:
  livenessProbeenabled: true
  livenessProbe:    
    httpGet:
      path: /
      port: 8000
  readinessProbeenabled: false
  readinessProbe:    
    httpGet:
      path: /
      port: http
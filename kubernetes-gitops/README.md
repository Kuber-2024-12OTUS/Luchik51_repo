# GitOps и инструменты поставки

## Домашнее задание  
1) Установить в кластер ArgoCD и научится его конфигурировать  
2) Создать приложение, описывающее установку манифестов из git репозитория  
3) Создать приложение, описывающее установку из Helm-чарта, хранящегося в Git-репозитории  

**Что сделано:**  


**Для запуска:**
```
helm upgrade --install argocd argo/argo-cd \
  --namespace argocd \
  --create-namespace \
  --set global.nodeSelector.node-role=infra \
  --set global.tolerations[0].key=node-role \
  --set global.tolerations[0].operator=Equal \
  --set global.tolerations[0].value=infra \
  --set global.tolerations[0].effect=NoSchedule
```


kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | ForEach-Object { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
admin
jGAe1pPRmE7CB5vf

kubectl port-forward -n argocd service/argocd-server 8080:80
https://localhost:8080/applications

## Taint и Label
```
kubectl taint nodes <node-name> node-role=infra:NoSchedule

kubectl get nodes --show-labels
kubectl label node <node-name> node-role=infra
```

grafana 
![grafana](img/grafana.png) 


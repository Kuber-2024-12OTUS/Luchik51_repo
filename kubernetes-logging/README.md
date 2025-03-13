# Сервисы централизованного логирования для kubernetes

## Домашнее задание  
1) Используем Yandex Cloud
2) Установить в кластер loki и научиться его конфигурировать
3) научиться собирать логи с нод кластера с помощью promtail
4) настроить подключение grafana и loki

**Что сделано:**  


**Для запуска:**
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm upgrade --install loki grafana/loki-stack --namespace logging --create-namespace
helm upgrade --install grafana grafana/grafana --namespace logging --create-namespace
## В Linux
# kubectl get secret --namespace logging grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
## В Powershell
kubectl get secret --namespace logging grafana -o jsonpath="{.data.admin-password}"| ForEach-Object { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
EZ1lFBPHSyJHmttiL688jOPdDxmL0C8UaDmX7Otr
helm show values grafana/loki > loki-values.yaml
helm show values grafana/promtail > promtail-values.yaml
helm show values grafana/grafana > grafana-values.yaml

```

yandex версия:
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/grafana/loki/chart/loki --version 1.2.0-7 --untar
helm install --namespace logging --create-namespace --set global.bucketname=otus-loki-hw loki ./loki/
```


taint
```
kubectl get nodes
kubectl taint nodes node-1 node-role=infra:NoSchedule
Проверка
kubectl describe node <node-name>
Удаление
kubectl taint nodes <node-name> node-role=infra:NoSchedule-
```

# -- Tolerations for tokengen Job
    tolerations: []

    tolerations:
      - key: "node-role"
        operator: "Equal"
        value: "infra"
        effect: "NoSchedule"
или
  spec:
      containers:
      - name: loki
        image: grafana/loki:latest
        ports:
        - containerPort: 3100
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: "node-role"
                operator: In
                values:
                - "infra"
      tolerations:
      - key: "node-role"
        operator: "Equal"
        value: "infra"
        effect: "NoSchedule"
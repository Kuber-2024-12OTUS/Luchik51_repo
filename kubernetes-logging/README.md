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
helm show values grafana/loki > loki-values.yaml
helm show values grafana/promtail > promtail-values.yaml
helm show values grafana/grafana > grafana-values.yaml

```

yandex версия:
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/grafana/loki/chart/loki --version 1.2.0-7 --untar
helm install --namespace logging --create-namespace --set global.bucketname=otus-loki-hw loki ./loki/
```
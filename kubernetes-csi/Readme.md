# Установка и использование CSI драйвера

## Домашнее задание  

1) Установить и сконфигурировать CSI driver для Yandex Object Storage
2) Запускать и исполþзовать полезную нагрузку, использовать для хранения бакета в Yandex Object Storage




Основная информация отсюда: https://github.com/yandex-cloud/k8s-csi-s3
https://yandex.cloud/ru/docs/managed-kubernetes/operations/volumes/s3-csi-integration




Для проверки работы можно подключиться к поду и посмотреть содержимое /data:

bash
Copy
kubectl exec -it file-generator -- ls /data
kubectl exec -it file-generator -- cat /data/имя_файла.txt
kubectl get ClusterRole -n homework
kubectl delete ClusterRole metrics-reader -n homework
kubectl get ClusterRoleBinding -n homework
kubectl delete ClusterRoleBinding metrics-access -n homework

kubectl apply -f .\storageClass.yaml
kubectl apply -f .\pvc.yaml -n homework


helm delete otus -n homework
helm install otus ./otus -n homework --create-namespace
helm upgrade otus ./otus -n homework


kubectl delete all --all -n homework

kubectl describe pod/nginx-deployment-86988bf9d-6xq5n -n homework
kubectl get pods/nginx-deployment-5f484b877f-r6qkd -n homework -o yaml

kubectl exec -it nginx-deployment-76b8898f4-7vhxw -c otus -n homework -- /bin/sh


HelmFile
От текущего пользователя (без административных прав) в Powershell 5.1+:
```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
# Поиск приложений для установки:
# https://scoop.sh/#/apps?q=helm
scoop install helmfile helm
# winget uninstall helm
# scoop uninstall helm
# scoop uninstall helmfile
```

В Linux так:
```
curl -Lo helmfile.tar.gz https://github.com/helmfile/helmfile/releases/download/v1.0.0-rc.10/helmfile_1.0.0-rc.10_linux_amd64.tar.gz
tar -xzf helmfile.tar.gz
chmod +x helmfile
sudo mv helmfile /usr/local/bin/
```

Основные команды Helmfile  
```
helmfile init: Первое, что надо запустить

helmfile apply: Устанавливает или обновляет релизы.

helmfile sync: Синхронизирует состояние кластера с описанным в helmfile.yaml.

helmfile diff: Показывает различия между текущим состоянием и тем, что описано в helmfile.yaml.

helmfile destroy: Удаляет все релизы, описанные в helmfile.yaml.
```

To install the help-diff plugin on Windows:
(есть стандартная команда:
helm plugin install https://github.com/databus23/helm-diff
Но она устанавливает файлы для Linux и они не работают в Windows)
download the latest release built for Windows (currently: version 3.1.3 - https://github.com/databus23/helm-diff/releases/download/v3.1.3/helm-diff-windows.tgz)
extract the archive
copy the content of the diff extracted folder to $APPDATA$\helm\plugins\helm-diff
check if its properly recognized by helm using helm.exe diff version (for example)



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


1. Get the application URL by running these commands:
{{- if .Values.ingress.enabled }}
{{- range $host := .Values.ingress.hosts }}
  {{- range .paths }}
  http{{ if $.Values.ingress.tls }}s{{ end }}://{{ $host.host }}{{ .path }}
  {{- end }}
{{- end }}
{{- else if contains "NodePort" .Values.service.type }}
  export NODE_PORT=$(kubectl get --namespace {{ .Release.Namespace }} -o jsonpath="{.spec.ports[0].nodePort}" services {{ include "otus.fullname" . }})
  export NODE_IP=$(kubectl get nodes --namespace {{ .Release.Namespace }} -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
{{- else if contains "LoadBalancer" .Values.service.type }}
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch its status by running 'kubectl get --namespace {{ .Release.Namespace }} svc -w {{ include "otus.fullname" . }}'
  export SERVICE_IP=$(kubectl get svc --namespace {{ .Release.Namespace }} {{ include "otus.fullname" . }} --template "{{"{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}"}}")
  echo http://$SERVICE_IP:{{ .Values.service.port }}
{{- else if contains "ClusterIP" .Values.service.type }}
  export POD_NAME=$(kubectl get pods --namespace {{ .Release.Namespace }} -l "app.kubernetes.io/name={{ include "otus.name" . }},app.kubernetes.io/instance={{ .Release.Name }}" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace {{ .Release.Namespace }} $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace {{ .Release.Namespace }} port-forward $POD_NAME 8080:$CONTAINER_PORT
{{- end }}
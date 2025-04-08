# 14. Подходы к развертыванию и обновлению production-grade кластера 

## Домашнее задание  
1) Создать кластер с использованием kubeadm  
2) Понимать как обновить кластер до нужной версии kubernetes с использованием kubeadm  
3) Создатþ кластер с исполþзованием kubespray  

## Выполняемые команды
1) Обычное задание
```
sudo apt update && sudo apt upgrade -y && sudo reboot now
```

2) Задание со *
sudo apt install pssh -y
ssh-keygen -t ed25519
ssh-copy-id user@server1
ssh-copy-id user@server2
user@server2" > hosts.txt
parallel-ssh -i -h hosts.txt "hostname"

```
sudo apt update && sudo apt upgrade -y && sudo reboot now

```
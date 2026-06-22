# Для начала установим Docker

Обновление пакетов
```bash
sudo apt update
```

Устанавливаем Docker
```bash
sudo apt install docker.io -y
```

Добавляем пользователя что бы не писать каждый раз sudo 
```bash
sudo usermod -aG docker $USER
```
Перезагрузка для применения
```bash
newgrp docker
```

# Конфиг зеркала для установки образа Minecraft 

Создание файла конфига
```bash 
sudo nano /etc/docker/daemon.json
```

Редактируем файл 
```bash
{
  "registry-mirrors": [
    "https://dockerhub.timeweb.cloud",
    "https://mirror.gcr.io"
  ]
}
```

![](Server_deployment_Minecraft/screenshots/CreatingMirror.png)

Перезапускаем докер 
```bash
sudo systemctl restart docker
```

Проверь применение зеркала
```bash
docker info | grep -A 2 "Registry Mirrors"
```

Мы увидем такой вывод в терминале 
```bash
 Registry Mirrors:
  https://dockerhub.timeweb.cloud/
  https://mirror.gcr.io/
```

![](Server_deployment_Minecraft/screenshots/RestartAndCheck.png)

# Качаем образ Minecrfat 

```bash
docker pull itzg/minecraft-server:latest
```

![](Server_deployment_Minecraft/InstallingImage.png)

# Запускаем сервер

```bash
docker run -d \
  --name minecraft-server \
  --restart unless-stopped \
  -p 25565:25565 \
  -e EULA=TRUE \
  -e VERSION=LATEST \
  -e MEMORY=2G \
  -e ONLINE_MODE=FALSE \
  -v /home/$USER/minecraft-data:/data \
  itzg/minecraft-server
```

![](Server_deployment_Minecraft/StartingServer.png)

Проверяем запустился ли контейнер 
```bash
docker ps
```

![](Server_deployment_Minecraft/ContainerInspection.png)

# Проверка логов сервера 

```bash 
docker logs -f minecraft-server
```

![](Server_deployment_Minecraft/CheckLogsOne.png)

Ждём появления строки 
`Done (х.ххs)! For help, type "help"`

![](Server_deployment_Minecraft/CheckLogsTwo.png)

Узнаём наш ip 
```bash 
ip a | grep inet
```
![](Server_deployment_Minecraft/SearchIp.png)

в моём случае `192.168.2.100`

# Запуск в майнкрафте 

Добавляем сервер 
```bash
192.168.2.100:25565
```








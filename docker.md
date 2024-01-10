[DOCKER](https://docs.docker.com)

## установка
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

## добавление пользователя
```bash
useradd -m -s /bin/bash <name user>
usermod -aG docker <name user>
```

## комманды терминала
```bash
service docker status       # проверка докера на активность     q выход
docker --version            # проверка верскии докера
docker pull <image name>    # скачивание контейнера
docker run <image name>     # если не скачан, скачивается автоматом
docker ps                   # показывает процессы - запущенные контейнеры
docker ps -a                # -- // -- которые спят
docker rm <name or id>      # удалит скаченный контейнер
docker images               # покажет скаченные
docker
docker
docker
docker
docker
docker
docker

```
https://www.youtube.com/watch?v=O8N1lvkIjig
28:57
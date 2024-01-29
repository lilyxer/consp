adduser lilyxer
usermod lilyxer -aG sudo    # добавляем пользователя в группу суперпользователей
su lilyxer                  # переключение на пользователя
sudo apt update             # роверка обновлений пакетного менеджера
sudo apt upgrade            # обновление всех пакетов -y без запроса
sudo apt install python3-venv python3-pip postrgesql
sudo fuser -k 8000/tcp      # убийство процесса который занимает 8000 порт
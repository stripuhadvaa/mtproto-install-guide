

Инструкция по установке легковесного прокси-сервера [mtg](https://github.com/9seconds/mtg) на Ubuntu с поддержкой Fake-TLS и спонсорского канала.

## 1. Установка
```bash
# Скачиваем архив (версия 2.2.4 для amd64)
wget https://github.com/9seconds/mtg/releases/download/v2.2.4/mtg-2.2.4-linux-amd64.tar.gz

# Распаковываем
tar -xzf mtg-2.2.4-linux-amd64.tar.gz

# Перемещаем исполняемый файл
sudo mv mtg-2.2.4-linux-amd64/mtg /usr/local/bin/

# Удаляем временные файлы
rm -rf mtg-2.2.4-linux-amd64 mtg-2.2.4-linux-amd64.tar.gz
```

## 2. Конфигурация
Создайте файл `/etc/mtg.toml`:
```bash
sudo nano /etc/mtg.toml
```
Вставьте следующие данные:
```toml
secret = "ваш секрет"
bind-to = "0.0.0.0:443"
adtag = "ваш тег из бота @MTProxybot"
```

## 3. Настройка службы (Systemd)
Создайте файл `/etc/systemd/system/mtg.service`:
```bash
sudo nano /etc/systemd/system/mtg.service
```
Вставьте содержимое:
```ini
[Unit]
Description=MTProto Proxy Server (mtg)
After=network.target

[Service]
Type=simple
User=nobody
Group=nogroup
ExecStart=/usr/local/bin/mtg run /etc/mtg.toml
Restart=on-failure
RestartSec=3
LimitNOFILE=1048576
LimitNPROC=512

[Install]
WantedBy=multi-user.target
```

Запуск:
```bash
sudo systemctl daemon-reload
sudo systemctl enable mtg
sudo systemctl start mtg
```

## 4. Firewall
```bash
sudo ufw allow 443/tcp
sudo ufw reload
```

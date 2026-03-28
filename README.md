# Инструкция по установке и настройке MTProxy (mtg) на Ubuntu/debian

### 1. Установка, смотрите актуальную версии тут [mtg](https://github.com/9seconds/mtg)
```bash
# Скачиваем последнюю версию mtg
wget https://github.com/9seconds/mtg/releases/download/v2.2.4/mtg-2.2.4-linux-amd64.tar.gz

# Распаковываем и перемещаем
tar -xzf mtg-2.2.4-linux-amd64.tar.gz
sudo mv mtg-2.2.4-linux-amd64/mtg /usr/local/bin/

# Очистка
rm -rf mtg-2.2.4-linux-amd64 mtg-2.2.4-linux-amd64.tar.gz
```

### 2. Регистрация в @MTProxybot
1. Откройте [@MTProxybot](https://t.me/MTProxybot) и напишите `/newproxy`.
2. Введите адрес вашего сервера: `IP:443`.
3. Бот попросит ввести секрет. **ВАЖНО:** Так как бот принимает только 32-символьные секреты, сгенерируйте короткий ключ командой:
   ```bash
   openssl rand -hex 16
   ```
4. Скопируйте полученный 32-символьный код и отправьте его боту.
5. Бот пришлет вам **Proxy Tag**. Скопируйте его, он понадобится для конфигурации.

### 3. Генерация основного Fake-TLS секрета
Теперь создайте «длинный» секрет для работы самого прокси (Fake-TLS):
```bash
mtg generate-secret -c vk.ru
```
*Скопируйте полученный длинный код (начинается на `ee...`).*
*Вместо `vk.ru` указывайте любой домен.

### 4. Конфигурация
Создайте файл конфигурации:
```bash
sudo nano /etc/mtg.toml
```
Вставьте конфигурацию, заменив значения на свои:
```
secret = "ВАШ_ДЛИННЫЙ_HEX_СЕКРЕТ"
secret-teleproxy = "ВАШ_КОРОТКИЙ_32_СИМВОЛЬНЫЙ_СЕКРЕТ"
bind-to = "0.0.0.0:443"
adtag = "ВАШ_PROXY_TAG_ИЗ_БОТА"
```

### 5. Настройка службы (Systemd)
Создаем автозапуск сервера:
```bash
sudo nano /etc/systemd/system/mtg.service
```
Вставьте следующий текст:
```ini
[Unit]
Description=MTProto Proxy Server (mtg)
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/mtg run /etc/mtg.toml
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

### 6. Активация и запуск
```bash
# Перезагружаем демоны и запускаем
sudo systemctl daemon-reload
sudo systemctl enable mtg
sudo systemctl start mtg

# Проверка статуса (должно быть active (running))
sudo systemctl status mtg
```

### 7. Настройка Firewall, открываем порт 443, если ещё не открыт
```bash
sudo ufw allow 443/tcp
sudo ufw reload
```

---
**Важно:** Для подключения пользователей используйте **длинный секрет** (`secret`), а не тот, который вы отправляли боту. Бот будет получать статистику через `secret-teleproxy` автоматически в фоновом режиме.

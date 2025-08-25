---

# 🚀 Быстрая установка: n8n + Flowise + Metabase + Postgres

Этот проект помогает вам за **10 минут** развернуть готовый набор сервисов для автоматизации, работы с LLM и аналитики данных.  

## 📦 Что включено

- **n8n** → инструменты автоматизации (но‑код)  
- **Flowise** → визуальная работа с LLM (чат-боты, агенты и пайплайны)  
- **Metabase** → дашборды и визуализация данных  
- **Postgres** → база данных  
- **pgAdmin** → управление Postgres через веб-интерфейс  
- **Nginx** → вход через поддомены  

---

## 🔧 Требования

- VPS или сервер с **Ubuntu 22.04+**  
- Установленный **Docker + Docker Compose plugin**  
- Домен (например, `example.com`) и 4 поддомена:
  - `n8n.example.com`
  - `flowise.example.com`
  - `metabase.example.com`
  - `pg.example.com`
- Все поддомены должны указывать на IP вашего сервера (A‑записи в DNS).  
---

🔌 Подключение к серверу (VPS)
Для начала подключитесь к вашему серверу по SSH. Это можно сделать либо с помощью встроенного терминала (Linux, macOS, Windows PowerShell), либо через удобный клиент Termius.

Вариант 1: через терминал (Linux/macOS/Windows PowerShell)
Выполните команду (замените USER и IP на данные вашего сервера):

```
ssh USER@IP
```
Пример:


```
ssh root@123.45.67.89
```
При первом подключении нужно будет подтвердить ключ сервера (yes) и ввести пароль пользователя.

Вариант 2: через Termius (Windows/macOS/Linux)
Установите Termius.
Создайте новый Host:
Address: IP вашего сервера (123.45.67.89)
Username: ваш пользователь (обычно root)
Password: пароль или используйте SSH‑ключ (.ssh/id_rsa).
Дважды кликните по хосту — произойдёт подключение.
👉 Рекомендуется использовать SSH-ключи вместо пароля:

Сгенерируйте ключ на локальной машине:

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
Ключ будет сохранён в ~/.ssh/id_ed25519.pub. Скопируйте его на сервер:

```
ssh-copy-id USER@IP
```
После этого подключение к серверу будет выполняться без пароля.

➡ Теперь, когда вы в системе — можно переходить к установке Docker.

---

## ⚡ Установка

### 1. Установите Docker

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
```

Проверим:
```bash
docker --version
docker compose version
```

Должен получится такой итог:
```
metaldeth@compute-vm-2-2-20-ssd-1753102165319:~$ docker --version
Docker version 28.3.3, build 980b856
metaldeth@compute-vm-2-2-20-ssd-1753102165319:~$ docker compose version
Docker Compose version v2.39.1
```

---

### 2. Скачайте репозиторий

```
cd /opt
sudo git clone https://github.com/metaldeth/n8n-flowise-metabase-stack.git
cd n8n-flowise-metabase-stack
```

---

### 3. Настройте окружение
ss
Скопируйте `.env.example` в `.env`:

```bash
sudo cp .env.example .env
sudo nano .env
```

Измените:  
- `DOMAIN=ваш_домен`  
- Задайте свои пароли (`POSTGRES_PASSWORD`, `PGADMIN_DEFAULT_PASSWORD`, `N8N_BASIC_AUTH_PASSWORD`, `FLOWISE_PASSWORD`).  

---

### 4. Запустите сервисы

```bash
sudo docker compose up -d
```
---
### 5. Настройка DNS

Перед тем как включать SSL‑сертификаты LetsEncrypt, убедитесь, что ваш домен и все поддомены правильно указывают на IP‑адрес сервера.

В панели управления вашим доменом создайте A‑записи:

Поддомен	Тип	Значение (IP сервера)
n8n	      A	  123.45.67.89
flowise	  A	  123.45.67.89
metabase	A	  123.45.67.89
pg	      A	  123.45.67.89
👉 замените 123.45.67.89 на настоящий IP‑адрес вашего VPS.

Проверьте, что поддомены действительно указывают на сервер. Для этого выполните:

```
dig +short n8n.example.com
dig +short flowise.example.com
dig +short metabase.example.com
dig +short pg.example.com
```
или (альтернативно) через nslookup:


nslookup n8n.example.com
Ответ должен содержать IP вашего VPS.

⚠️ DNS‑записи иногда обновляются не мгновенно — может уйти от 5 минут до 2‑3 часов (редко до суток).
---

### 6. Настройте SSL (LetsEncrypt в Docker)

Теперь мы получим и настроим бесплатные SSL‑сертификаты для всех поддоменов.

1. Убедитесь, что все поддомены (`n8n`, `flowise`, `metabase`, `pg`) указывают на IP вашего сервера.

2. Выполните команду один раз для первичного получения сертификатов  
   (замените `admin@вашдомен.ru` на ваш реальный e‑mail):

   ```bash
   sudo docker run --rm \
     -v $(pwd)/nginx/certs:/etc/letsencrypt \
     -v $(pwd)/nginx/certbot:/var/www/certbot \
     certbot/certbot certonly --webroot \
     -w /var/www/certbot \
     -d n8n.DOMAIN \
     -d flowise.DOMAIN \
     -d metabase.DOMAIN \
     -d pg.DOMAIN \
     --non-interactive --agree-tos -m admin@вашдомен.ru
```
После успешной выдачи сертификатов перезапустите nginx:

```
sudo docker compose restart nginx
```
С этого момента все ваши сервисы будут доступны по HTTPS.
Автоматическое продление сертификатов выполняется контейнером certbot каждые 12 часов, поэтому SSL‑сертификаты будут поддерживаться актуальными без вашего участия.

---

## 🌐 Доступ к сервисам

После запуска всё будет доступно по вашим поддоменам:  
- `https://n8n.DOMAIN` — n8n  
- `https://flowise.DOMAIN` — Flowise  
- `https://metabase.DOMAIN` — Metabase  
- `https://pg.DOMAIN` — pgAdmin  

👉 Для входа используйте логины/пароли, которые указали в `.env`.

---

## 🔑 Внешние интеграции

Интеграции с OpenAI, OpenRouter, Pinecone и другими сервисами настраиваются **внутри n8n или Flowise**. Просто возьмите свой API‑ключ и базовый URL и вставьте его напрямую при настройке.  

---

## 📜 Лицензия

MIT License © 2025 **metaldeth**  
См. [LICENSE](./LICENSE).  

---
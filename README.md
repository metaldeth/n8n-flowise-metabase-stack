# 🚀 n8n-flowise-metabase-stack

Готовый Docker‑стек для быстрого запуска:  
- **n8n** → автоматизация процессов  
- **Flowise** → визуальный интерфейс для LLM  
- **Metabase** → визуализация и аналитика данных  
- **Postgres + pgAdmin** → база данных и графический интерфейс для её управления  
- **Nginx** → reverse proxy для поддоменов  

Создано для того, чтобы **новички могли развернуть весь стек в 3–4 шага** на своём VPS.

---

## ⚡ Быстрый старт

```bash
# 1. Установка Docker + Compose Plugin
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
sudo usermod -aG docker $USER
newgrp docker

# 2. Клонирование репозитория
git clone https://github.com/your-org/n8n-flowise-metabase-stack.git
cd n8n-flowise-metabase-stack

# 3. Настройка .env
cp .env.example .env
nano .env

# 4. Запуск стека
docker compose up -d
```

После запуска сервисы будут доступны по адресам:
- `http://n8n.DOMAIN`  
- `http://flowise.DOMAIN`  
- `http://metabase.DOMAIN`  
- `http://pg.DOMAIN`  

---

## 🔧 Требования к серверу

- Ubuntu 22.04 LTS (или выше)  
- Минимум:
  - 2 CPU
  - 4 GB RAM (рекомендуется 8 GB для Metabase)
  - 20 GB SSD
- Домен + 4 поддомена:
  - `n8n.example.com`
  - `flowise.example.com`
  - `metabase.example.com`
  - `pg.example.com`
- Все A‑записи в DNS должны указывать на IP вашего сервера.

---

## 🚀 Подробная инструкция по развёртыванию

### 1. Подготовьте сервер
- Арендуйте VPS с Ubuntu 22.04 LTS (или выше).  
- Зарегистрируйте домен и создайте поддомены:  
  - `n8n.DOMAIN`  
  - `flowise.DOMAIN`  
  - `metabase.DOMAIN`  
  - `pg.DOMAIN`  
- Все A‑записи должны указывать на IP‑адрес вашего сервера.

---

### 2. Установите Docker и Docker Compose Plugin

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
sudo usermod -aG docker $USER
newgrp docker
```

Проверяем:  
```bash
docker --version
docker compose version
```

---

### 3. Клонируйте репозиторий

```bash
git clone https://github.com/your-org/n8n-flowise-metabase-stack.git
cd n8n-flowise-metabase-stack
```

---

### 4. Настройте параметры окружения

Скопируйте пример `.env` и отредактируйте его:

```bash
cp .env.example .env
nano .env
```

В `.env` укажите:
- `DOMAIN=вашдомен.ru`  
- Пароли для Postgres, pgAdmin, n8n и Flowise  

---

### 5. Запустите стек

```bash
docker compose up -d
```

▶️ Первый запуск может занять 1–3 минуты, пока скачаются образы и инициализируются сервисы.  

---

### 6. Настройте SSL (рекомендуется)

```bash
sudo ufw allow 'Nginx Full'
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx \
  -d n8n.DOMAIN \
  -d flowise.DOMAIN \
  -d metabase.DOMAIN \
  -d pg.DOMAIN
```

---

### 7. Установка интеграций (опционально)

- Для **OpenAI**: получите API‑ключ в [OpenAI](https://platform.openai.com) и добавьте его в `.env`:  

  ```dotenv
  OPENAI_API_KEY=sk-ваш_ключ
  ```

- Для **Pinecone** или других сервисов также можно добавить ключи в `.env`.

---

### 8. Управление контейнерами

- Проверить запущенные контейнеры:  
  ```bash
  docker ps
  ```
- Остановить:  
  ```bash
  docker compose down
  ```
- Перезапустить:  
  ```bash
  docker compose restart
  ```

---

## 📜 Лицензия

Этот проект распространяется под лицензией **MIT**.

MIT License © 2025 metaldeth

См. подробный текст [LICENSE](./LICENSE).
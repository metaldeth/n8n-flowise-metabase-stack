Супер 🚀  
Вот тебе финальный **`README.md`** именно под студентов/пользователей.  
Минимально, просто, пошагово и без излишних технических деталей.  

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

---

### 2. Скачайте репозиторий

```bash
git clone https://github.com/your-org/n8n-flowise-metabase-stack.git
cd n8n-flowise-metabase-stack
```

---

### 3. Настройте окружение

Скопируйте `.env.example` в `.env`:

```bash
cp .env.example .env
nano .env
```

Измените:  
- `DOMAIN=ваш_домен`  
- Задайте свои пароли (`POSTGRES_PASSWORD`, `PGADMIN_DEFAULT_PASSWORD`, `N8N_BASIC_AUTH_PASSWORD`, `FLOWISE_PASSWORD`).  

---

### 4. Запустите сервисы

```bash
docker compose up -d
```

---

### 5. Настройте SSL (рекомендуется)

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

💡 Готово! Теперь у вас есть собственная небольшая инфраструктура для работы с автоматизацией, LLM и аналитикой.  

---

👉 Вопрос: Хочешь, я дополню `README.md` картинкой‑схемой (какой сервис для чего и по какому адресу открывается), чтобы студентам было ещё понятнее?
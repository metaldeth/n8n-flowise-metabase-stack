# 🚀 n8n-flowise-metabase-stack

Готовый Docker‑стек для быстрого запуска:
- **n8n** → автоматизация процессов
- **Flowise** → визуальный интерфейс для LLM
- **Metabase** → визуализация и аналитика данных
- **Postgres + pgAdmin** → база данных и графический интерфейс для её управления
- **Nginx** → reverse proxy для поддоменов

Создано для того, чтобы **новички могли развернуть весь стек в 3-4 шага** на своём VPS.

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
- Все A‑записи в DNS должны указывать на ваш сервер.

---

## 🚀 Развёртывание

### 1. Установите Docker и Git

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git docker.io docker-compose
sudo systemctl enable docker

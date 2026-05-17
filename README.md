<div align="center">

# SmartERP

**Комплексная корпоративная ERP-платформа для управления HR, финансами, складом, закупками, продажами и проектами — единая система под вашим контролем.**

[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![Django](https://img.shields.io/badge/Django-5.0-092E20?style=flat-square&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![DRF](https://img.shields.io/badge/DRF-3.15-red?style=flat-square)](https://www.django-rest-framework.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=flat-square&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-7-DC382D?style=flat-square&logo=redis&logoColor=white)](https://redis.io/)
[![Celery](https://img.shields.io/badge/Celery-5.4-37814A?style=flat-square&logo=celery&logoColor=white)](https://docs.celeryq.dev/)
[![Docker](https://img.shields.io/badge/Docker_Compose-3.9-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docs.docker.com/compose/)
[![Nginx](https://img.shields.io/badge/Nginx-1.25-009639?style=flat-square&logo=nginx&logoColor=white)](https://nginx.org/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

</div>

---

## Содержание

1. [О проекте](#1-о-проекте)
2. [Ключевые возможности](#2-ключевые-возможности)
3. [Технологический стек](#3-технологический-стек)
4. [Структура репозитория](#4-структура-репозитория)
5. [Архитектура и как это работает](#5-архитектура-и-как-это-работает)
6. [Доменная модель](#6-доменная-модель)
7. [Сервисы в Docker Compose](#7-сервисы-в-docker-compose)
8. [Быстрый старт](#8-быстрый-старт)
9. [Основные команды Docker](#9-основные-команды-docker)
10. [Ручной запуск frontend и backend](#10-ручной-запуск-frontend-и-backend)
11. [Конфигурация и переменные окружения](#11-конфигурация-и-переменные-окружения)
12. [API, очереди и интеграции](#12-api-очереди-и-интеграции)
13. [Мониторинг и эксплуатация](#13-мониторинг-и-эксплуатация)
14. [CI/CD](#14-cicd)
15. [Безопасность и хранение данных](#15-безопасность-и-хранение-данных)
16. [Роли компонентов в продакшене](#16-роли-компонентов-в-продакшене)
17. [Лицензия](#17-лицензия)
18. [Поддержка](#18-поддержка)

---

## 1. О проекте

**SmartERP** — это production-ready система планирования ресурсов предприятия (ERP), объединяющая ключевые бизнес-процессы в едином веб-интерфейсе. Платформа охватывает управление персоналом, бухгалтерию, складской учёт, закупки, продажи и проектную деятельность с полной изоляцией данных между организациями.

Целевая аудитория: **средний и крупный бизнес**, **SaaS-операторы** (мультитенантный режим), **интеграторы** через REST API с OpenAPI-документацией.

### Тип системы

SmartERP — **мультисервисная распределённая платформа**, а не монолитный скрипт. Каждый слой (API, SPA, фоновые задачи, кэш, БД) развёртывается и масштабируется независимо.

| Аспект | Описание |
|--------|----------|
| **Продукт** | B2B ERP: HR, финансы, склад, закупки, продажи, проекты с RBAC и аудитом |
| **Архитектура** | Django REST API + React SPA + Celery workers + Nginx reverse proxy |
| **Хранение** | PostgreSQL (метаданные и транзакции) + Redis (кэш, сессии, брокер Celery) + локальные media/static |
| **Мультитенантность** | Row-level изоляция по `Organization` через middleware и scoped querysets |
| **Аутентификация** | JWT (SimpleJWT) с ротацией refresh-токенов и blacklist |

---

## 2. Ключевые возможности

### Платформа

- **Мультитенантность** — каждая организация (tenant) изолирована на уровне строк БД; middleware автоматически привязывает контекст запроса к организации пользователя.
- **Гранулярный RBAC** — роли с правами `view / create / edit / delete / approve / export` по модулям: HR, Finance, Inventory, Procurement, Sales, Projects.
- **JWT-аутентификация** — access/refresh токены, blacklist после ротации, throttle на API.
- **Аудит изменений** — `django-auditlog` фиксирует критичные операции в БД.
- **Фоновая обработка** — Celery с именованными очередями `default`, `hr`, `finance`, `inventory`.
- **Интерактивная документация API** — Swagger UI и ReDoc на базе `drf-spectacular`.

### Модули

| Модуль | Возможности |
|--------|-------------|
| **HR** | Сотрудники, должности, отпуска с цепочкой согласования, посещаемость, расчёт зарплаты |
| **Finance** | План счетов, проводки (двойная запись), счета, бюджеты, расходы |
| **Inventory** | Склады, каталог товаров, остатки, движения (приход/расход/перемещение) |
| **Procurement** | Поставщики, заявки на закупку, заказы, приёмка |
| **Sales** | CRM, коммерческие предложения, заказы, отчёты по выручке |
| **Projects** | Проекты, этапы, задачи, учёт времени, бюджет проекта |

### Frontend

- React 18 SPA с Redux Toolkit
- Дашборд с KPI и графиками (Chart.js, Recharts)
- Адаптивная вёрстка, toast-уведомления, таблицы с фильтрацией

---

## 3. Технологический стек

| Слой | Технологии |
|------|------------|
| **Backend** | Python 3.12+, Django 5.0, Django REST Framework 3.15 |
| **Auth** | djangorestframework-simplejwt, django-axes |
| **Frontend** | React 18, Redux Toolkit, Axios, React Router 6 |
| **База данных** | PostgreSQL 16 |
| **Кэш и брокер** | Redis 7, django-redis |
| **Очереди задач** | Celery 5.4, django-celery-beat, django-celery-results |
| **API Docs** | drf-spectacular (OpenAPI 3) |
| **Прокси** | Nginx 1.25 (rate limiting, gzip, security headers) |
| **Контейнеризация** | Docker, Docker Compose 3.9 |
| **WSGI-сервер** | Gunicorn (4 workers, 2 threads) |
| **Мониторинг** | Sentry SDK (production), structured logging |

---

## 4. Структура репозитория

```
SmartERP/
├── backend/                    # Django-приложение
│   ├── config/                 # Настройки, URLs, Celery, WSGI
│   │   └── settings/
│   │       ├── base.py         # Общие настройки
│   │       ├── development.py  # Локальная разработка
│   │       └── production.py   # Продакшен (HSTS, Sentry, SSL)
│   ├── apps/
│   │   ├── accounts/           # Пользователи, организации, роли, tenancy
│   │   ├── hr/                 # Кадры, отпуска, зарплата
│   │   ├── finance/            # Бухгалтерия, счета, бюджеты
│   │   ├── inventory/          # Склад и товары
│   │   ├── procurement/        # Закупки
│   │   ├── sales/              # Продажи и CRM
│   │   └── projects/           # Управление проектами
│   ├── utils/                  # Пагинация, permissions, exceptions
│   ├── manage.py
│   └── requirements.txt
├── frontend/                   # React SPA
│   ├── public/
│   └── src/
│       ├── api/                # HTTP-клиент и endpoints
│       ├── components/         # UI-компоненты по модулям
│       ├── hooks/              # useAuth, useApi
│       ├── pages/              # Dashboard, Settings
│       └── store/              # Redux store
├── nginx/
│   └── nginx.conf              # Reverse proxy, rate limits, static/media
├── docker-compose.yml          # Полный стек сервисов
├── .env.example                # Шаблон переменных окружения
└── README.md
```

---

## 5. Архитектура и как это работает

### Схема взаимодействия

```
                         ┌─────────────────────┐
                         │   Nginx ( :80 )     │
                         │  rate limit, gzip   │
                         └──────────┬──────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
    ┌─────────▼─────────┐  ┌────────▼────────┐  ┌────────▼────────┐
    │  React Frontend   │  │  Django API     │  │  /static /media │
    │  ( :3000 )        │  │  Gunicorn :8000 │  │  volumes        │
    └───────────────────┘  └────────┬────────┘  └─────────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                 │
          ┌─────────▼────────┐ ┌──────▼──────┐ ┌───────▼────────┐
          │  PostgreSQL 16   │ │  Redis 7    │ │  Celery Worker │
          │  (метаданные)    │ │  cache+MQ   │ │  + Celery Beat │
          └──────────────────┘ └─────────────┘ └────────────────┘
```

### Жизненный цикл запроса

1. Клиент обращается к **Nginx** (`/` → React, `/api/` → Django).
2. **JWT** проверяется в DRF; **TenantMiddleware** устанавливает контекст организации в thread-local.
3. ViewSet применяет **permissions** и **tenant-scoped** queryset.
4. Долгие операции (зарплата, уведомления, отчёты) уходят в **Celery** через именованные очереди.
5. Результаты задач сохраняются в **django-celery-results**; кэш сессий — в **Redis**.

### Очереди Celery

| Очередь | Назначение |
|---------|------------|
| `default` | Общие задачи |
| `hr` | Уведомления об отпусках, расчёт зарплаты |
| `finance` | Финансовые отчёты и batch-операции |
| `inventory` | Пересчёт остатков, алерты низкого склада |

---

## 6. Доменная модель

### Ядро (Accounts)

```
Organization ──┬── Department ── User
               ├── Role ── RolePermission (module + permission)
               └── User (email-auth, is_org_admin)
```

### Бизнес-модули (упрощённо)

| Модуль | Ключевые сущности |
|--------|-------------------|
| **HR** | `Position`, `Employee`, `LeaveRequest`, `Attendance`, `Payroll` |
| **Finance** | `Account`, `Transaction`, `TransactionEntry`, `Invoice`, `Budget`, `Expense` |
| **Inventory** | `Warehouse`, `Product`, `ProductCategory`, `StockLevel`, `StockMovement` |
| **Procurement** | `Supplier`, `PurchaseRequest`, `PurchaseOrder` (+ line items) |
| **Sales** | `Customer`, `Quotation`, `SalesOrder`, `SalesReport` |
| **Projects** | `Project`, `Milestone`, `Task`, `TimeEntry` |

Все бизнес-сущности привязаны к **Organization** для обеспечения мультитенантной изоляции.

---

## 7. Сервисы в Docker Compose

| Сервис | Образ / сборка | Порт | Назначение |
|--------|----------------|------|------------|
| `db` | `postgres:16-alpine` | 5432 | Основная реляционная БД |
| `redis` | `redis:7-alpine` | 6379 | Кэш, сессии, брокер Celery |
| `backend` | `./backend` (build) | — (внутр. 8000) | Django API + Gunicorn, migrate, collectstatic |
| `celery_worker` | `./backend` (build) | — | Обработчик фоновых задач |
| `celery_beat` | `./backend` (build) | — | Планировщик (DatabaseScheduler) |
| `frontend` | `./frontend` (build) | — (внутр. 3000) | React dev/build сервер |
| `nginx` | `nginx:1.25-alpine` | 80, 443 | Единая точка входа, static/media |

**Volumes:** `postgres_data`, `redis_data`, `static_volume`, `media_volume`.

---

## 8. Быстрый старт

### Требования

- [Docker](https://docs.docker.com/get-docker/) и Docker Compose
- [Git](https://git-scm.com/)
- 4+ GB RAM для комфортной работы стека

### Запуск за 5 шагов

```bash
# 1. Клонировать репозиторий
git clone https://github.com/NodirOdilov/SmartERP.git
cd SmartERP

# 2. Создать конфигурацию
cp .env.example .env
# Отредактируйте DJANGO_SECRET_KEY и пароли для production

# 3. Поднять весь стек
docker compose up --build -d

# 4. Создать суперпользователя (миграции выполняются автоматически при старте backend)
docker compose exec backend python manage.py createsuperuser

# 5. Открыть в браузере
```

| Ресурс | URL |
|--------|-----|
| **Веб-приложение** | http://localhost |
| **API** | http://localhost/api/v1/ |
| **Админ-панель** | http://localhost/admin/ |
| **Swagger UI** | http://localhost/api/docs/ |
| **ReDoc** | http://localhost/api/redoc/ |
| **Health check** | http://localhost/health |

---

## 9. Основные команды Docker

> Makefile в репозитории отсутствует — используйте `docker compose` напрямую.

```bash
# Запуск / остановка
docker compose up -d              # Запуск в фоне
docker compose down               # Остановка
docker compose down -v            # Остановка + удаление volumes (ОСТОРОЖНО: потеря данных)

# Логи
docker compose logs -f backend    # Логи API
docker compose logs -f celery_worker
docker compose ps                 # Статус сервисов

# Django management
docker compose exec backend python manage.py migrate
docker compose exec backend python manage.py makemigrations
docker compose exec backend python manage.py collectstatic --noinput
docker compose exec backend python manage.py shell
docker compose exec backend python manage.py test

# Celery
docker compose exec celery_worker celery -A config inspect active
docker compose exec celery_worker celery -A config inspect registered

# Frontend
docker compose exec frontend npm test
docker compose exec frontend npm run build
```

---

## 10. Ручной запуск frontend и backend

Используйте при локальной разработке без полного Docker-стека (PostgreSQL и Redis должны быть доступны).

### Backend

```bash
cd backend
python -m venv venv

# Windows
venv\Scripts\activate
# Linux / macOS
source venv/bin/activate

pip install -r requirements.txt

# В корне проекта: скопируйте .env и установите
# DJANGO_SETTINGS_MODULE=config.settings.development
# POSTGRES_HOST=localhost, REDIS_URL=redis://localhost:6379/0

python manage.py migrate
python manage.py runserver
```

### Frontend

```bash
cd frontend
npm install

# REACT_APP_API_URL=http://localhost:8000/api
npm start
```

При `npm start` dev-сервер React доступен на **http://localhost:3000**; proxy в `package.json` перенаправляет API на backend `:8000`.

---

## 11. Конфигурация и переменные окружения

Скопируйте `.env.example` → `.env`. **Никогда не коммитьте `.env` в Git.**

### Критичные переменные

| Переменная | Описание | Пример |
|------------|----------|--------|
| `DJANGO_SECRET_KEY` | Криптографический ключ Django | Длинная случайная строка |
| `DJANGO_SETTINGS_MODULE` | Модуль настроек | `config.settings.development` / `production` |
| `DJANGO_DEBUG` | Режим отладки | `True` (dev) / `False` (prod) |
| `DJANGO_ALLOWED_HOSTS` | Разрешённые хосты | `localhost,your-domain.com` |
| `DATABASE_URL` / `POSTGRES_*` | Подключение к PostgreSQL | см. `.env.example` |
| `REDIS_URL` | Кэш и сессии | `redis://:password@redis:6379/0` |
| `CELERY_BROKER_URL` | Брокер задач | `redis://:password@redis:6379/1` |
| `CELERY_RESULT_BACKEND` | Результаты задач | `redis://:password@redis:6379/2` |
| `JWT_ACCESS_TOKEN_LIFETIME_MINUTES` | TTL access-токена | `60` |
| `JWT_REFRESH_TOKEN_LIFETIME_DAYS` | TTL refresh-токена | `7` |
| `REACT_APP_API_URL` | Base URL API для SPA | `http://localhost/api` |
| `SENTRY_DSN` | Мониторинг ошибок (prod) | `https://...@sentry.io/...` |
| `EMAIL_*` | SMTP для уведомлений | Gmail / корпоративный SMTP |

Полный список — в файле [`.env.example`](.env.example).

---

## 12. API, очереди и интеграции

### Базовый URL

```
/api/v1/
```

### Маршруты модулей

| Префикс | Модуль |
|---------|--------|
| `/api/v1/accounts/` | Регистрация, login, refresh, профиль, организации |
| `/api/v1/hr/` | Сотрудники, отпуска, посещаемость, зарплата |
| `/api/v1/finance/` | Счета, проводки, инвойсы, бюджеты |
| `/api/v1/inventory/` | Склады, товары, остатки, движения |
| `/api/v1/procurement/` | Поставщики, заявки, заказы |
| `/api/v1/sales/` | Клиенты, КП, заказы |
| `/api/v1/projects/` | Проекты, задачи, time entries |

### Аутентификация

```http
POST /api/v1/accounts/login/
Content-Type: application/json

{
  "email": "user@company.com",
  "password": "your_secure_password"
}
```

```http
GET /api/v1/hr/employees/
Authorization: Bearer <access_token>
```

### Переключение организации (superuser)

```http
X-Organization-ID: <uuid-организации>
```

### Документация

- **OpenAPI schema:** `/api/schema/`
- **Swagger UI:** `/api/docs/`
- **ReDoc:** `/api/redoc/`

---

## 13. Мониторинг и эксплуатация

### Логирование

- Консольный вывод для всех сервисов (`docker compose logs`)
- Ротируемый файл: `LOG_FILE` (по умолчанию `logs/smarterp.log`)
- Уровень: `LOG_LEVEL=INFO` (или `DEBUG` в development)

### Sentry (production)

Укажите `SENTRY_DSN` и `SENTRY_ENVIRONMENT` в `.env` — ошибки Django и Celery автоматически отправляются в Sentry.

### Health checks

| Проверка | Endpoint / команда |
|----------|-------------------|
| Nginx | `GET /health` → `200 healthy` |
| PostgreSQL | `pg_isready` (встроен в docker healthcheck) |
| Redis | `redis-cli ping` (healthcheck в compose) |
| Celery | `celery -A config inspect ping` |

### Рекомендации по масштабированию

- Увеличьте `--concurrency` у `celery_worker` для тяжёлых отчётов
- Добавьте read-replica PostgreSQL при высокой нагрузке на чтение
- Вынесите static/media на CDN или S3 (`django-storages` уже в зависимостях)
- Горизонтально масштабируйте `backend` за Nginx upstream

---

## 14. CI/CD

В репозитории **пока не настроен** GitHub Actions / GitLab CI. Рекомендуемый pipeline:

```yaml
# Пример этапов CI
- lint: flake8, black --check, eslint
- test: pytest-django, coverage, npm test
- build: docker build backend + frontend
- deploy: docker compose -f docker-compose.prod.yml up -d
```

Минимальный чеклист перед деплоем:

- [ ] `DJANGO_DEBUG=False`
- [ ] Уникальный `DJANGO_SECRET_KEY`
- [ ] `DJANGO_ALLOWED_HOSTS` и `DJANGO_CORS_ALLOWED_ORIGINS` настроены
- [ ] SSL через Nginx / Let's Encrypt
- [ ] `SENTRY_DSN` подключён
- [ ] Резервное копирование `postgres_data`

---

## 15. Безопасность и хранение данных

| Механизм | Реализация |
|----------|------------|
| **Аутентификация** | JWT + blacklist refresh-токенов |
| **Авторизация** | RBAC по модулям, `IsAuthenticated` по умолчанию |
| **Тенантность** | `TenantMiddleware`, изоляция по `organization_id` |
| **Rate limiting** | Nginx: 10 req/s (API), 5 req/min (login) |
| **HTTP-заголовки** | X-Frame-Options, CSP, HSTS (production) |
| **Пароли** | Минимум 10 символов, валидаторы Django |
| **Аудит** | django-auditlog на критичных моделях |
| **Загрузка файлов** | Лимит `MAX_UPLOAD_SIZE_MB`, запрет исполнения скриптов в `/media/` |
| **Production** | `SECURE_SSL_REDIRECT`, secure cookies, SSL для PostgreSQL |

---

## 16. Роли компонентов в продакшене

| Компонент | Роль |
|-----------|------|
| **Nginx** | TLS-терминация, маршрутизация, gzip, rate limit, раздача static/media |
| **Gunicorn** | WSGI-процессы Django, обработка синхронных API-запросов |
| **Celery Worker** | Асинхронные задачи: HR-уведомления, payroll, отчёты |
| **Celery Beat** | Cron-расписание через `django-celery-beat` (хранение в БД) |
| **PostgreSQL** | ACID-транзакции, связи между модулями, мультитенантные данные |
| **Redis** | Кэш сессий, throttle, брокер и backend результатов Celery |
| **React SPA** | Клиентский UI; в prod — `npm run build` + статика через Nginx |

---

## 17. Лицензия

Проект распространяется под лицензией **MIT**. Подробности — в файле [LICENSE](LICENSE) (при отсутствии файла уточните у maintainers репозитория).

---

## 18. Поддержка

- **Issues:** [GitHub Issues](https://github.com/NodirOdilov/SmartERP/issues)
- **Документация API:** http://localhost/api/docs/ (после запуска)
- **Вопросы по развёртыванию:** создайте issue с тегом `deployment` и приложите вывод `docker compose ps` и логов `backend`

---

<div align="center">

**SmartERP** — управляйте бизнесом целиком, а не разрозненными таблицами.

*Сделано с ❤️ для современного предприятия*

</div>

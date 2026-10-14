# DevOps — Лабораторная работа №2

Репозиторий оркестрации микросервисов **frontend** и **backend** (версия 2.0)
при помощи Docker и Docker Compose.

## Состав

```
DevOps/
├── docker-compose.yml   # описание сборки и запуска микросервисов
├── backend/             # git-подмодуль → IliaProkoshin/backend (v2.0)
└── frontend/            # git-подмодуль → IliaProkoshin/frontend (v2.0)
```

Микросервисы подключены как git-подмодули, а Dockerfile лежит в каждом из
репозиториев микросервисов. Compose собирает образы из этих Dockerfile.

## Требования

- Docker Desktop (Docker Engine + Docker Compose v2).

## Запуск

```bash
# Клонировать репозиторий вместе с подмодулями
git clone --recurse-submodules https://github.com/IliaProkoshin/DevOps.git
cd DevOps

# Собрать образы и запустить контейнеры
docker compose up --build -d
```

После запуска приложение доступно в браузере:

<http://127.0.0.1:5000>

Остановка:

```bash
docker compose down
```

Остановка с удалением тома с данными:

```bash
docker compose down -v
```

## Как реализованы пункты задания

| Пункт | Реализация |
|-------|------------|
| 4.1 Сборка образов с именем и версией микросервиса | `build: ./backend` + `image: backend:2.0`, `build: ./frontend` + `image: frontend:2.0` |
| 4.2 Сохранение `data.txt` вне контейнера | именованный том `backend-data` смонтирован в `/data`, бэкенд пишет в `DATA_FILE=/data/data.txt` |
| 4.3 Отдельная сеть без проброса портов наружу | сеть `devops-net`; фронтенд обращается к бэкенду по имени `backend`; порт бэкенда наружу не публикуется |
| 4.4 Зависимость фронтенда от бэкенда | `depends_on: backend (condition: service_healthy)` |
| 4.5 Автоперезапуск после перезагрузки ноды | `restart: always` у обоих сервисов |

## Схема взаимодействия

```
браузер ──► frontend:5000 ──(/api/*, сеть devops-net)──► backend:5001 ──► том backend-data
```

Браузер открывает страницу фронтенда и обращается к API по относительному
пути `/api/...`. Фронтенд как обратный прокси передаёт эти запросы бэкенду
по внутренней сети Docker. Бэкенд доступен только внутри сети и не имеет
опубликованных портов.

## Проверка взаимодействия

```bash
# Отправить данные через API фронтенда (браузер делает это автоматически)
curl -X POST http://127.0.0.1:5000/api/data -H "Content-Type: application/json" -d "{\"text\": \"Привет из Docker\"}"

# Прочитать сохранённые данные
curl http://127.0.0.1:5000/api/data
```

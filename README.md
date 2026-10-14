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

## Проверка взаимодействия

```bash
# Отправить данные через API фронтенда (браузер делает это автоматически)
curl -X POST http://127.0.0.1:5000/api/data -H "Content-Type: application/json" -d "{\"text\": \"Привет из Docker\"}"

# Прочитать сохраненные данные
curl http://127.0.0.1:5000/api/data
```

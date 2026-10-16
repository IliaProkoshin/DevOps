# DevOps

Репозиторий оркестрации микросервисов **frontend** и **backend** версий 2.0.
Остановка и запуск через Docker Compose описаны в ЛР2,
развертывание на удаленной ноде через Ansible в ЛР3.

## Состав

```
DevOps/
├── docker-compose.yml   # описание сборки и запуска микросервисов
├── backend/             # git-подмодуль → IliaProkoshin/backend (v2.0)
└── frontend/            # git-подмодуль → IliaProkoshin/frontend (v2.0)
```

Микросервисы подключены как git-подмодули, Dockerfile лежит в каждом из
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

## ЛР3 Ansible

В каталоге `ansible/` находится Ansible-проект, который устанавливает Docker
и Docker Compose на удаленную ноду, клонирует туда  репозиторий вместе
с микросервисами (подмодули frontend и backend), запускает/останавливает
сервисы через Docker Compose

### Стенд

Контроллер ansible WSL2 Ubuntu-24.04, удаленная нода WSL2 Ubuntu-22.04,
связь по ssh (пользователь ansible, ключ авторизации)

```bash
wsl -d Ubuntu-22.04 --exec sleep infinity
```

### Запуск

```bash
cd ansible
ansible-galaxy collection install -r requirements.yml

# docker + clone + запуск сервисов
ansible-playbook run_playbook.yml --ask-vault-pass --tags deploy

# Только запуск сервисов
ansible-playbook run_playbook.yml --ask-vault-pass --tags start

# Только остановка сервисов
ansible-playbook run_playbook.yml --ask-vault-pass --tags stop
```

Теги: `install`, `clone`, `start`, `stop`, `deploy`

Пароль для расшифровки vault-файлов: `ilya_prokoshin`

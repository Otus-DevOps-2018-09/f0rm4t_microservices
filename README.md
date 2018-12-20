# f0rm4t_microservices
f0rm4t microservices repository

# GitLab CI #1

Добавлена конфигурация Docker Compose (`./gitlab-ci/docker-compose.yml`) для разворачивания GitLab CI и конфигурация для сборки и тестирования приложения (`./gitlab-ci.yml`).

# Docker #4

Добавлена конфигурация docker-compose: `./src/docker-compose.yml`. Необходимо скопировать файл `./src/.env.example` в `./src/.env` и внести необходимые изменения:

```
COMPOSE_PROJECT_NAME=reddit
USERNAME=
UI_PORT=9292
UI_VERSION=1.0
POST_VERSION=1.0
COMMENT_VERSION=1.0
MONGO_VERSION=3.2
```

Запуск проекта:

```bash
docker-compose up -d
docker-compose -f docker-compose.yml -f docker-compose.override.yml up -d # local env
```

# Docker #3

Добавлен код микросервисов и Dockerfile к каждому из них (директория `./src`). Проведена оптимизация сборки образа для сервиса ui.

Сборка образов:

```bash
docker build -t f0rm4t/post:1.0 ./post-py
docker build -t f0rm4t/comment:1.0 ./comment
docker build -t f0rm4t/ui:1.0 ./ui
```

Создание сети и тома для MongoDB:

```bash
docker network create reddit
docker volume create reddit_db
```

Запуск контейнеров:

```bash
docker run -d --network=reddit --network-alias=db -v reddit_db:/data/db mongo:latest
docker run -d --network=reddit --network-alias=post -e POST_DATABASE_HOST=db f0rm4t/post:1.0
docker run -d --network=reddit --network-alias=comment -e COMMENT_DATABASE_HOST=db f0rm4t/comment:1.0
docker run -d --network=reddit -e POST_SERVICE_HOST=post -e COMMENT_SERVICE_HOST=comment -p 9292:9292 f0rm4t/ui:1.0
```

# Docker #2

Добавлен Dockerfile для сборки образа otus-reddit с установленным приложением и требуемым окружением. Образ добавлен на hub.docker.com: f0rm4t/otus-reddit.

# Docker #1

Добавлена конфигурация для интеграции с Travis.

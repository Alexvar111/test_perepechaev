# sandbox_db
![logo_sandbox](./data/logo.png){width="30%"}

Данный проект позволяет запустить PostgresSQL базу, Minio S3, Clickhouse и Airflow все четыре сервиса поднимаются в одном файле и оказываются одной локальной сети, что позволяет сразу приступить к обучению и не тратить лишнее время на настроку сервисов. Проект подразумевает, что вы работаете в линуксе, знаете как работать с docker.

***
## Инструкция по запуску
***
Для того, что бы развернуть проект следует выполнить пару простых шагов:

шаг 1 ) 
Создадим нужные папки
```
mkdir -p ./dags ./logs ./plugins ./config ./pgdata
```

шаг 2 )
Создадим файл .env путем добавления в него параметра AIRFLOW_UID
```
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

шаг 3 )
Создадим базы данных на которых будет работать Airflow
```
sudo docker compose up airflow-init
```

шаг 4 )
Запустим развертывание контейнеров с сервисами
```
sudo docker compose up -d
```
Если все прошло успешно то, все контейнеры должны запуститься.
Выглядит это примерно так
```
CONTAINER ID   IMAGE                   COMMAND                  CREATED        STATUS                            PORTS                                       NAMES
15e2ec149d9c   apache/airflow:2.10.3   "/usr/bin/dumb-init …"   25 hours ago   Up 6 seconds (health: starting)   8080/tcp                                    airflow-worker
a958cc38b8d7   apache/airflow:2.10.3   "/usr/bin/dumb-init …"   25 hours ago   Up 6 seconds (health: starting)   8080/tcp                                    airflow-scheduler
e4d9f3a98f33   apache/airflow:2.10.3   "/usr/bin/dumb-init …"   25 hours ago   Up 6 seconds (health: starting)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   airflow_webserver
0cc2864fb400   apache/airflow:2.10.3   "/usr/bin/dumb-init …"   25 hours ago   Up 6 seconds (health: starting)   8080/tcp                                    airflow-triggerer
c0d113306363   postgres:13             "docker-entrypoint.s…"   25 hours ago   Up 6 seconds (healthy)            5432/tcp                                    airflow_metadata_pg
434ad068594b   postgres:17.0-alpine    "docker-entrypoint.s…"   25 hours ago   Up 6 seconds (healthy)            0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   dwh_pg
deb25aa67585   redis:7.2-bookworm      "docker-entrypoint.s…"   25 hours ago   Up 6 seconds (healthy)            6379/tcp                                    airflow_metadata_redis
```

***
## Удаление проекта или перезапуск

***
Если потребуется все удалить, то это можно сделать так
```
sudo docker compose down
rm -rf pgdata/
```
А если нужно полное удаление
```
docker compose down --volumes --rmi all
rm -rf pgdata/
```

***
# Адреса сервисов
***
Я константами указал конкретные ip для сервисов, а так же пароли и логины для подключений.
Это позволяет выучить их один раз и затем использовать при работе и настройке этих сервисов
```
IP list:
|NAME             |IP          |PORT|LOGIN    |PASSWORD|
|-----------------+------------+----+---------+--------+
|dwh_pg           |10.19.88.101|5432|tech_load|dwh88   |
|dwh_minio        |10.19.88.102|9000|tech_load|minio123|
|dwh_click        |10.19.88.103|8123|tech_load|dwh88   |
|airflow_webserver|10.19.88.104|8080|airflow  |airflow |
```
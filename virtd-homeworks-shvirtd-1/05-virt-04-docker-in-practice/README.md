# Домашнее задание к занятию 5. «Практическое применение Docker»

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  

### Ответ к задаче 0
2. Результат выполнения команды docker compose version
3. 
   <img width="693" height="47" alt="image" src="https://github.com/user-attachments/assets/fb1be106-0ce5-4869-95be-cf91d558f634" />


###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

---

## Задача 1
1. Сделайте в своем GitHub пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python).

2. Создайте файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Используйте базовый образ ```python:3.12-slim```
   - Обязательно используйте конструкцию ```COPY . .``` в Dockerfile
   - Создайте `.dockerignore` файл для исключения ненужных файлов
   - Используйте ```CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]``` для запуска
   - Протестируйте корректность сборки
2.1 Используйте multistage сборку вместо single stage.
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker, с помощью venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) Изучите код приложения и добавьте управление названием таблицы через ENV переменную.
---

### Ответ к задаче 1

1. Сделан fork репозитория https://github.com/world12hub/shvirtd-example-python
2. Создан файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Содержание  ```Dockerfile.python```
```
FROM python:3.12-slim AS builder
WORKDIR /build
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    python3-dev \
    libffi-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN python -m venv /venv && \
    /venv/bin/pip install --no-cache-dir -r requirements.txt

FROM python:3.12-slim
COPY --from=builder /venv /venv
WORKDIR /app
COPY . .
ENV PATH="/venv/bin:$PATH"
EXPOSE 5000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"] 
```
-  протестирована корректность сборки
<img width="819" height="271" alt="image" src="https://github.com/user-attachments/assets/ab98e5da-068e-4b92-87be-807cbd9472b8" />


2.1 Используется multistage сборка.


### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете информационную ошибку --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию virtd, как это указано в .env)>; show tables; SELECT * from requests LIMIT 10;```. Примечание: таблица в БД создается после первого поступившего запроса к приложению.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

### Ответ к задаче 3

1. Изучен файл "proxy.yaml"
Создан в репозитории с проектом файл compose.yaml. С помощью директивы "include" подключен к нему файл "proxy.yaml".
Описан файл compose.yaml в соответствии с тех.заданием в задаче:

```
include:
  - proxy.yaml

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.python
    container_name: web-app
    restart: always
    networks:
      backend:
        ipv4_address: 172.20.0.5
    environment:
      DB_HOST: db
      DB_PORT: 3306
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
      DB_NAME: ${MYSQL_DATABASE}
    depends_on:
      - db

  db:
    image: mysql:8
    container_name: mysql-db
    restart: always
    networks:
      backend:
        ipv4_address: 172.20.0.10
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
```
Скриншот:

<img width="858" height="1098" alt="image" src="https://github.com/user-attachments/assets/eae971e7-7111-406e-8fd8-d38b2500afc9" />

2. Запущен проект при помощи команды
```
docker compose up -d
```
Скриншот:

<img width="974" height="846" alt="image" src="https://github.com/user-attachments/assets/4f8738c6-bf90-420c-9155-f376bb220fe0" />

<img width="974" height="127" alt="image" src="https://github.com/user-attachments/assets/7e3c8065-d6f9-48ff-94e9-8f7941507a11" />

2.1. Проверка запущенных контейнеров:
```
docker ps -a
```
Скриншот:

<img width="974" height="127" alt="image" src="https://github.com/user-attachments/assets/e8a16e60-4eb3-4f71-ad19-042e79deb7e9" />


2.2. Тестирование curl

```
curl -L http://127.0.0.1:8090
```

Скриншот:

<img width="974" height="55" alt="image" src="https://github.com/user-attachments/assets/4729f4d2-a248-40c6-a3e9-29d8327c6def" />

3. Подключение к MySQL и выполнение SQL-запросов
```
docker exec -ti mysql-db mysql -uroot -pYtReWq4321
```
Скриншот:

<img width="974" height="368" alt="image" src="https://github.com/user-attachments/assets/95728bf7-db30-4396-8e24-c718c9125926" />

3.1. В интерактивной оболочке MySQL выполнены следующие команды:
```
show databases;
use virtd;
show tables;
SELECT * FROM requests LIMIT 10;
```
Скриншот:

<img width="974" height="1000" alt="image" src="https://github.com/user-attachments/assets/e34e056a-a28f-4a99-9162-127a5c4b169d" />

4. Остановка проекта

```
docker compose down
```
Скриншот:

<img width="974" height="225" alt="image" src="https://github.com/user-attachments/assets/a387bc6f-4dcb-456d-a4d5-f366f6c04fad" />


## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. Трафик должен пройти через цепочки: Пользователь → Internet → Nginx → HAProxy → FastAPI(запись в БД) → HAProxy → Nginx → Internet → Пользователь
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. Повторите SQL-запрос на сервере и приложите скриншот и ссылку на fork.

### Ответ к задаче 4

1. Запущена в Yandex Cloud ВМ.
   
Скриншот:

<img width="974" height="56" alt="image" src="https://github.com/user-attachments/assets/b3971144-d16d-4b68-bbf6-df2a0df828d5" />

2. Подключение к Вм по ssh и установка docker.
```
ssh -i ~/.ssh/<private_key> <user>@<external_IP>
```

```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```
```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl status docker
sudo systemctl start docker
```

Скриншот:

<img width="974" height="303" alt="image" src="https://github.com/user-attachments/assets/f7e1fa59-e0bc-44c0-b32f-9066fda82368" />

3. Написан bash-скрипт, который скачивает fork-репозиторий в каталог /opt и запускает проект целиком.
```
#!/bin/bash
git clone https://github.com/world12hub/shvirtd-example-python.git /opt/myproject
cd /opt/myproject
cat > .env <<EOF
MYSQL_ROOT_PASSWORD="YtReWq4321"
MYSQL_DATABASE="virtd"
MYSQL_USER="app"
MYSQL_PASSWORD="QwErTy1234"
EOF
docker compose up -d
```

Скриншот:

<img width="974" height="344" alt="image" src="https://github.com/user-attachments/assets/d07e120a-3c11-44fe-a221-e871d6ae379e" />

<img width="974" height="480" alt="image" src="https://github.com/user-attachments/assets/126b0682-9345-4178-82b2-7b9830b06c3f" />

4. Проверка http подключений, например(или аналогичный): https://check-host.net/check-http и запуск проверки сервиса http://<внешний_IP-адрес_вашей_ВМ>:8090.
5. 
Скриншот:
<img width="974" height="721" alt="image" src="https://github.com/user-attachments/assets/e72c1a07-c5aa-410c-a850-7bd961fdc3ff" />

Повтор SQL-запроса на сервере и приложите скриншот.
```
docker exec -ti mysql-db mysql -uroot -pYtReWq4321
```
Скриншот:
<img width="974" height="287" alt="image" src="https://github.com/user-attachments/assets/e974aba3-72d7-410d-9449-d70630ac9c8d" />

```
show databases;
use virtd;
show tables;
SELECT * FROM requests LIMIT 10;
```
Скриншот:
<img width="974" height="1035" alt="image" src="https://github.com/user-attachments/assets/b9609126-db45-4b45-8dd1-36b534be265a" />

Ссылка на fork: 
https://github.com/world12hub/shvirtd-example-python/tree/main 

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

### Ответ к задаче 6

Скачивание docker образ hashicorp/terraform:latest 
```
docker pull hashicorp/terraform:latest
```

Скриншот:
<img width="974" height="207" alt="image" src="https://github.com/user-attachments/assets/40d1e5c2-b0df-476d-b58d-9b0e6faebc5d" />

Копирование бинарного файла /bin/terraform на локальную машину, используя docker save. 
Скриншот:
<img width="974" height="95" alt="image" src="https://github.com/user-attachments/assets/b6aeeb4b-9c59-4da8-812e-e8f8050d4775" />

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

### Ответ к задаче 6.1

Задача 6.1

Команды:
```
docker run -d --name temp-terraform hashicorp/terraform:latest sleep 10
docker cp temp-terraform:/bin/terraform ./terraform
docker rm -f temp-terraform
./terraform version
```
Скриншот:

<img width="974" height="254" alt="image" src="https://github.com/user-attachments/assets/4c38b468-b786-485f-9839-580bf2ab98a7" />


## Задача 6.2 (**)
Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.  
Предоставьте скриншоты  действий .

## Задача 7 (***)
Запустите ваше python-приложение с помощью runC, не используя docker или containerd.  
Предоставьте скриншоты  действий .

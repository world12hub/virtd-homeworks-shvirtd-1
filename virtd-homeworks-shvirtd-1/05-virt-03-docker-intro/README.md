
# Домашнее задание к занятию 4 «Оркестрация группой Docker контейнеров на примере Docker Compose»

### Инструкция к выполению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
2. Практические задачи выполняйте на личной рабочей станции или созданной вами ранее ВМ в облаке.
3. Своё решение к задачам оформите в вашем GitHub репозитории в формате markdown!!!
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

## Задача 1

Сценарий выполнения задачи:
- Установите docker и docker compose plugin на свою linux рабочую станцию или ВМ.
- Если dockerhub недоступен создайте файл /etc/docker/daemon.json с содержимым: ```{"registry-mirrors": ["https://mirror.gcr.io", "https://daocloud.io", "https://c.163.com/", "https://registry.docker-cn.com"]}```
- Зарегистрируйтесь и создайте публичный репозиторий  с именем "custom-nginx" на https://hub.docker.com (ТОЛЬКО ЕСЛИ У ВАС ЕСТЬ ДОСТУП);
- скачайте образ nginx:1.29.0;
- Создайте Dockerfile и реализуйте в нем замену дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html с содержимым:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
```
- Соберите и отправьте созданный образ в свой dockerhub-репозитории c tag 1.0.0 (ТОЛЬКО ЕСЛИ ЕСТЬ ДОСТУП). 
- Предоставьте ответ в виде ссылки на https://hub.docker.com/<username_repo>/custom-nginx/general .

### Ответ к задаче 1

1. Установлен **docker** и **docker-compose**:
```
apt-get update
apt-get install docker-engine containerd docker-compose 
usermod $USER -aG docker
systemctl enable --now docker
```
**Скриншот:**

<img width="1041" height="303" alt="image" src="https://github.com/user-attachments/assets/83725fad-ba98-4d4e-a3ee-63dec555948f" />

<img width="712" height="186" alt="image" src="https://github.com/user-attachments/assets/cf83889e-a129-4fd9-a641-6b9a47835b5e" />

2. На **hub.docker.com** создан публичный репозиторий с именем **custom-nginx** <https://hub.docker.com/repository/docker/world12dockerhub/custom-nginx/>

**Скриншот:**

<img width="1041" height="297" alt="image" src="https://github.com/user-attachments/assets/8e0a3313-d3b7-4af9-9523-b0f1d5e79831" />

3. Загрузка образа **nginx:1.29.0**:

```
docker pull nginx:1.29.0
docker image list
```

**Скриншот:**

<img width="1041" height="367" alt="image" src="https://github.com/user-attachments/assets/2510a023-6ad1-440d-ac8f-800f388e2104" />

4. Создание Dockerfile и реализация в нем замены дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html:

4.1. Создание Dockerfile файла со следующим монифестом 

```
tee ~/docker_project/Dockerfile <<- 'EOF'
FROM nginx:1.29.0
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
EOF
```
**Скриншот:**

<img width="1041" height="133" alt="image" src="https://github.com/user-attachments/assets/76661f3e-e811-4e4e-b20e-b62923d36dca" />

4.2. Создание файла **index.html** с содержимым:

```
nano index.html
```
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
```

**Скриншот:**

<img width="973" height="342" alt="image" src="https://github.com/user-attachments/assets/60399420-1d2b-42eb-8b9d-b3fef4388d73" />

5. Сборка и отправка созданного образ в свой dockerhub-репозитории c tag 1.0.0

5.1. Вход в docker-репозиторий 

```
docker login -u world12dockerhub
Username: username
Password: password 
```

**Скриншот:**

<img width="1041" height="331" alt="image" src="https://github.com/user-attachments/assets/1becb13a-08a6-42bd-ba46-6a2b6611d328" />

5.2. Сборка образа

```
docker buildx build –t world12dockerhub/custom-nginx:1.0.0 .
```

**Скриншот:**

<img width="1041" height="351" alt="image" src="https://github.com/user-attachments/assets/4c196104-d0c7-473e-a2fc-fb76bf08a09a" />

5.3. Отпавка образа

```
docker push world12dockerhub/custom-nginx:1.0.0
```

**Скриншот:**

<img width="1041" height="264" alt="image" src="https://github.com/user-attachments/assets/23cbf96a-71b5-40a5-9a7c-cd6c67742a8e" />

6. Ссылка на репозиторий <https://hub.docker.com/repository/docker/world12dockerhub/custom-nginx/>

**Скриншот:**

<img width="1041" height="433" alt="image" src="https://github.com/user-attachments/assets/dfc56f49-120e-406c-bc21-5730e087a2de" />


## Задача 2

1. Запустите ваш образ custom-nginx:1.0.0 командой docker run в соответвии с требованиями:
- имя контейнера "ФИО-custom-nginx-t2"
- контейнер работает в фоне
- контейнер опубликован на порту хост системы 127.0.0.1:8080
2. Не удаляя, переименуйте контейнер в "custom-nginx-t2"
3. Выполните команду ```date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html```
4. Убедитесь с помощью curl или веб браузера, что индекс-страница доступна.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

### Ответ к задаче 2

1.	Запуск образа **custom-nginx:1.0.0**
``` 
docker run --name "kanyugin-sergey-custom-nginx-t2" -p 127.0.0.1:8080:80 -d world12dockerhub/custom-nginx:1.0.0
```

**Скриншот:**

<img width="1041" height="76" alt="image" src="https://github.com/user-attachments/assets/4f1699d8-58a5-455d-8183-174b0af445ad" />

<img width="1041" height="124" alt="image" src="https://github.com/user-attachments/assets/364df78f-0129-40e0-9527-b79f919c37c9" />

2. Переименование контейнера в "custom-nginx-t2"
   
```
docker rename kanyugin-sergey-custom-nginx-t2 custom-nginx-t2 
```

Скриншот:

<img width="1041" height="153" alt="image" src="https://github.com/user-attachments/assets/075306a6-2af3-42d4-a071-1f518a490163" />

3. Выполнение команды:
   
```
date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html
```

**Скриншот:**

<img width="1041" height="107" alt="image" src="https://github.com/user-attachments/assets/205f9b26-9da0-4b3e-8a55-481c39e56371" />

4. Проверка доступности страницы

```
curl localhost:8080
```

**Скриншот:**

<img width="948" height="298" alt="image" src="https://github.com/user-attachments/assets/d33cfde1-78fd-4185-94bf-a4abce50c710" />

## Задача 3
1. Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".
2. Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.
3. Выполните ```docker ps -a``` и объясните своими словами почему контейнер остановился.
4. Перезапустите контейнер
5. Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.
6. Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.
7. Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".
8. Запомните(!) и выполните команду ```nginx -s reload```, а затем внутри контейнера ```curl http://127.0.0.1:80 ; curl http://127.0.0.1:81```.
9. Выйдите из контейнера, набрав в консоли  ```exit``` или Ctrl-D.
10. Проверьте вывод команд: ```ss -tlpn | grep 127.0.0.1:8080``` , ```docker port custom-nginx-t2```, ```curl http://127.0.0.1:8080```. Кратко объясните суть возникшей проблемы.
11. * Это дополнительное, необязательное задание. Попробуйте самостоятельно исправить конфигурацию контейнера, используя доступные источники в интернете. Не изменяйте конфигурацию nginx и не удаляйте контейнер. Останавливать контейнер можно. [пример источника](https://www.baeldung.com/linux/assign-port-docker-container)
12. Удалите запущенный контейнер "custom-nginx-t2", не останавливая его.(воспользуйтесь --help или google)

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

### Ответ к задаче 3

1. Подключение к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2" осуществляется через команду:
    
```
docker attach <Имя_контенера>
```

2. Подключние к контейнеру:

```
docker attach custom-nginx-t2
```

**Скриншот:**

<img width="1041" height="618" alt="image" src="https://github.com/user-attachments/assets/1a4d5120-65eb-4a88-b30f-804a927e0ac7" />

3. Просмотр всех созданных контейнеров:
   
```
docker ps –a 
```

**Скриншот:**

<img width="1041" height="125" alt="image" src="https://github.com/user-attachments/assets/b32c7355-0524-469e-82e6-81f35a4c8708" />

Причина остановки контейнера заключается в том, что подключение было непосредственно к основному процессу контейнера, а комбинация Ctrl-C завершила процесс, соответственно приостановила работу контенйнера.

4. Перезапуск контейнера
    
```
docker restart custom-nginx-t2
```

**Скриншот:**

<img width="856" height="87" alt="image" src="https://github.com/user-attachments/assets/06214ff9-56c4-4ab7-89dc-caeac7720c52" />


5. Вход в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.
   
```
docker exec–it custom-nginx-t2 bash
```

**Скриншот:**

<img width="939" height="75" alt="image" src="https://github.com/user-attachments/assets/f9f76b47-97f4-41f5-8ed0-436eeb585b1d" />

6. Установка текстового редактора nano с помощью **apt-get**.
 
```
apt-get update 
apt-get install nano
```

**Скриншот:**

<img width="1041" height="440" alt="image" src="https://github.com/user-attachments/assets/b65ffc9f-499e-45f8-929a-5056e314baad" />

7. Отредактирован файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".
   
```
nano /etc/nginx/conf.d/default.conf
```

**Скриншот:**

<img width="954" height="316" alt="image" src="https://github.com/user-attachments/assets/17927781-d7fb-460e-84b8-7943020b0267" />

8. Запоминание(!) и выполнение команды nginx -s reload, а затем внутри контейнера curl http://127.0.0.1:80 ; curl http://127.0.0.1:81.
   
```
nginx -s reload
curl http://127.0.0.1:80 
curl http://127.0.0.1:81
```

**Скриншот:**

 <img width="1041" height="380" alt="image" src="https://github.com/user-attachments/assets/f2f32513-79f4-4c34-9f8f-14d68dcd745e" />

9. Выход из контейнера, набрав в консоли exit или Ctrl-D.

10. Проверка вывода команд: ss -tlpn | grep 127.0.0.1:8080 , docker port custom-nginx-t2, curl http://127.0.0.1:8080. Кратко объясните суть возникшей проблемы.

```
ss -tlpn | grep 127.0.0.1:8080
docker port custom-nginx-t2
curl http://127.0.0.1:8080
```

**Скриншот:**

 <img width="1041" height="259" alt="image" src="https://github.com/user-attachments/assets/a826d84d-a517-4d50-a3ab-b441d60f5fed" />

Проблема в сетевом соединении host-машины с docker-контейнером, из-за внесенных изменений в конфигурационный файл веб-сервера nginx.

11.  Решение проблемы с доступом к веб-ресурсу 127.0.0.1:8080

```
docker stop custom-nginx-t2
docker commit custom-nginx-t2 world12dockerhub/custom-nginx-t2:1.1.0
docker rename custom-nginx-t2 custom-nginx-t2-old
docker stop custom-nginx-t2-old
docker run --name custom-nginx-t2 -p 127.0.0.1:8080:81 -d world12dockerhub/custom-nginx-t2:1.1.0
curl 127.0.0.1:8080
```

**Скриншот:**

<img width="1041" height="407" alt="image" src="https://github.com/user-attachments/assets/7376cc94-ce98-4582-b49b-ddd659773760" /> 

12. Удаление запущенного контейнера "custom-nginx-t2", не останавливая его.
    
```
docker rm –f custom-nginx-t2
```

**Скриншот:**

<img width="823" height="83" alt="image" src="https://github.com/user-attachments/assets/1bd3a661-2d35-49e6-a44e-9896f6e30078" />



## Задача 4


- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку  текущий рабочий каталог ```$(pwd)``` на хостовой машине в ```/data``` контейнера, используя ключ -v.
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив текущий рабочий каталог ```$(pwd)``` в ```/data``` контейнера. 
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```.
- Добавьте ещё один файл в текущий каталог ```$(pwd)``` на хостовой машине.
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.


В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

### Ответ к задаче 4

1. Запущен первый контейнер из образа centos c любым тегом в фоновом режиме, подключив папку текущий рабочий каталог $(pwd) на хостовой машине в /data контейнера, используя ключ -v.

```
docker run -d -v $(pwd):/data --name centos_cont centos:7 sleep infinity
```

**Скриншот:**

<img width="1041" height="132" alt="image" src="https://github.com/user-attachments/assets/f6f728dc-905a-4013-9cf9-779ca198e7e3" />

2. Запущен второй контейнер из образа debian в фоновом режиме, подключив текущий рабочий каталог $(pwd) в /data контейнера.

```
docker run -d -v $(pwd):/data --name debian_cont debian:latest sleep infinity
```
**Скриншот:**

<img width="1041" height="209" alt="image" src="https://github.com/user-attachments/assets/3344fc5b-3ec2-4b2a-9682-48731262c251" />

3. Подключение к первому контейнеру с помощью docker exec и создание текстового файла file.json в /data.

```
docker exec -it centos_cont /bin/bash
touch /data/file.json 
exit
```
**Скриншот:**

<img width="1033" height="150" alt="image" src="https://github.com/user-attachments/assets/67109fa4-c0a1-4415-bc07-6eaec03e966e" />
 
4. Добавление ещё одного файл new_file.json в текущий каталог $(pwd) на хостовой машине.

```
touch new_file.json
```

**Скриншот:**

<img width="1041" height="98" alt="image" src="https://github.com/user-attachments/assets/fc675a2c-7288-49c4-b900-8ae169d79a3f" /> 

5. Подключение ко второму контейнеру и отображение листинга и содержания файлов в /data контейнера.

```
docker exec -it debian_cont /bin/bash
ls  –ll /data
```
**Скриншот:**

<img width="1041" height="248" alt="image" src="https://github.com/user-attachments/assets/2b3307b1-5001-4ed3-a363-55da645b81d4" />


## Задача 5

1. Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него.
"compose.yaml" с содержимым:
```
version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```
"docker-compose.yaml" с содержимым:
```
version: "3"
services:
  registry:
    image: registry:2

    ports:
    - "5000:5000"
```

И выполните команду "docker compose up -d". Какой из файлов был запущен и почему? (подсказка: https://docs.docker.com/compose/compose-application-model/#the-compose-file )

2. Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: https://docs.docker.com/compose/compose-file/14-include/)

3. Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: https://distribution.github.io/distribution/about/deploying/
4. Откройте страницу "https://127.0.0.1:9000" и произведите начальную настройку portainer.(логин и пароль адмнистратора)
5. Откройте страницу "http://127.0.0.1:9000/#!/home", выберите ваше local  окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:

```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```
6. Перейдите на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении <> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

7. Удалите любой из манифестов компоуза(например compose.yaml).  Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод, файл compose.yaml , скриншот portainer c задеплоенным компоузом.

### Ответ к задаче 5

1. Создана отдельная директория /tmp/netology/docker/task5 и 2 файла внутри него. "compose.yaml" с содержимым:
   
```
mkdir -p /tmp/netology/docker/task5
tee /tmp/netology/docker/task5/compose.yaml <<-‘EOF’
version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
EOF
```

"docker-compose.yaml" с содержимым:

```
tee /tmp/netology/docker/task5/docker-compose.yaml <<-‘EOF’
version: "3"
services:
  registry:
    image: registry:2

    ports:
    - "5000:5000"
EOF
```

**Скриншот:**

<img width="1041" height="434" alt="image" src="https://github.com/user-attachments/assets/fe8a838f-e541-42f2-86b8-446022559f1f" />

<img width="1041" height="415" alt="image" src="https://github.com/user-attachments/assets/53bb850d-a95a-4d15-8093-043c104ade1c" />

1.1. Выполнена команда “docker compose up –d”. Из двух файлов запустился файл compose.yaml, так как у него приоритет выше.

```
docker compose up –d
```

**Скриншот:**

<img width="1041" height="235" alt="image" src="https://github.com/user-attachments/assets/21500657-2dda-4358-8b0e-43e6793a2f99" />

2. Отредактирован файл **compose.yaml** так, чтобы были запущенны оба файла. 

```
nano compose.yaml
```

```
include:
  - ./docker-compose.yaml

version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

**Скриншот:**

<img width="992" height="464" alt="image" src="https://github.com/user-attachments/assets/e06812c9-8118-4a91-9ce6-6395833b2edb" />

```
docker compose up -d 
```
**Скриншот:**

<img width="1041" height="504" alt="image" src="https://github.com/user-attachments/assets/6e1cfdd2-976a-4a95-a522-0a24a5e11dcc" />


3. Выполнение в консоли хостовой ОС необходимых команд чтобы залить образ custom-nginx как custom-nginx:latest в запущенное локальное registry. 

```
docker tag world12dockerhub/custom-nginx:1.0.0 localhost:5000/custom-nginx:latest
docker push localhost:5000/custom-nginx:latest
```

**Скриншот:**

<img width="1041" height="617" alt="image" src="https://github.com/user-attachments/assets/4428836c-7646-4956-81c9-46e0d0cf46c1" />

4. Сервис portainer доступен по адресу: "https://127.0.0.1:9000", также осуществлен вход в portainer.

**Скриншот:**

<img width="1041" height="663" alt="image" src="https://github.com/user-attachments/assets/90d21a5a-2b15-4562-b2c4-3b1f56baac53" />

5. Открыта страница "http://127.0.0.1:9000/#!/home", выбрано local окружение. Перешел на вкладку "stacks" и в "web editor" задеплоил следующий компоуз:

```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```

**Скриншот:**

<img width="1041" height="586" alt="image" src="https://github.com/user-attachments/assets/551efb3e-4404-4163-8505-986dfbbbcf4f" />

6. Переход на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выбрал контейнер с nginx и нажал на кнопку "inspect". В представлении <> Tree развернул поле "Config" и сделал скриншот от поля "AppArmorProfile" до "Driver".

**Скриншот:**

<img width="1041" height="543" alt="image" src="https://github.com/user-attachments/assets/deeb6a0f-7eae-44d8-8e3d-8d45d2466c7d" />

<img width="1041" height="540" alt="image" src="https://github.com/user-attachments/assets/21386675-bbec-4b82-b5c3-de49c5b67e8a" />

<img width="1041" height="541" alt="image" src="https://github.com/user-attachments/assets/238f34a6-6bfc-44c7-b6ab-6a972be65938" />


7. Удаление файла compose.yaml. Выполнение команды "docker compose up -d". 

```
rm -rf compose.yaml
docker compose -u d
docker compose down -d
```

**Скриншот:**

<img width="1041" height="196" alt="image" src="https://github.com/user-attachments/assets/aba38170-021d-48c7-9a1b-9d5aa1df7a0c" />

На основе выведенных предупреждений предлагается:
1.	Удалить строку version из файла docker-compose.yaml, так как этот атрибут больше не нужен и игнорируется.

**Скриншот:**

<img width="616" height="308" alt="image" src="https://github.com/user-attachments/assets/cb40dd00-7fc0-4e8e-972a-aaf50255b871" />

2.	Очистить осиротевшие контейнеры:
- либо вручную удалить контейнер task5-portainer-1 командой 

```
docker rm task5-portainer-1
```
- либо при следующем запуске добавить флаг --remove-orphans, чтобы Docker Compose удалил их автоматически.

```
docker compose up -d --remove-orphans
docker compose down
```

**Скриншот:**

<img width="1041" height="306" alt="image" src="https://github.com/user-attachments/assets/69fa6c7c-c6fc-4402-a3ee-7cb0ddb8c36f" />


***УРА!!! Я ЭТО СДЕЛАЛ!! ЭТО БЫЛО НЕ ПРОСТО, НО ОЧЕНЬ УВЛЕКАТЕЛЬНО!))))***

---

### Правила приема

Домашнее задание выполните в файле readme.md в GitHub-репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.




# Домашнее задание к занятию 2. «Применение принципов IaaC в работе с виртуальными машинами»

#### Это задание для самостоятельной отработки навыков и не предполагает обратной связи от преподавателя. Его выполнение не влияет на завершение модуля. Но мы рекомендуем его выполнить, чтобы закрепить полученные знания. Все вопросы, возникающие в процессе выполнения заданий, пишите в раздел "Вопросы по заданиям" в личном кабинете.
---
## Важно

**Перед началом работы над заданием изучите [Инструкцию по экономии облачных ресурсов](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD).**
Перед отправкой работы на проверку удаляйте неиспользуемые ресурсы.
Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
Подробные рекомендации [здесь](https://github.com/netology-code/virt-homeworks/blob/virt-11/r/README.md).

---

### Цели задания

1. Научиться создвать виртуальные машины в Virtualbox с помощью Vagrant.
2. Научиться базовому использованию packer в yandex cloud.

   
## Задача 1
Установите на личный Linux-компьютер или учебную **локальную** ВМ с Linux следующие сервисы(желательно ОС ubuntu 20.04):

- [VirtualBox](https://www.virtualbox.org/),
- [Vagrant](https://github.com/netology-code/devops-materials), рекомендуем версию 2.3.4
- [Packer](https://github.com/netology-code/devops-materials/blob/master/README.md) версии 1.9.х + плагин от Яндекс Облако по [инструкции](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/packer-quickstart)
- [уandex cloud cli](https://cloud.yandex.com/ru/docs/cli/quickstart) Так же инициализируйте профиль с помощью ```yc init``` .

### Ответ к задаче 1
1. Установлен vagrant версии 2.3.4.
   
   <img width="650" height="108" alt="image" src="https://github.com/user-attachments/assets/3b6cabaf-8438-4c6b-832f-1e1c614488ba" />
   
2. Установлен Packer версии 1.15.0
   
   <img width="571" height="47" alt="image" src="https://github.com/user-attachments/assets/6cb5511c-7eb9-4a01-8392-f338f3126980" />
   
   и плагин от Яндекс Облако
   
   <img width="569" height="196" alt="image" src="https://github.com/user-attachments/assets/bc831447-22e1-4d8c-961f-5b532ac11f71" />
   
4. Установлен yandex cloude cli и инициализирован профиль
   
   <img width="689" height="104" alt="image" src="https://github.com/user-attachments/assets/b4c47927-4800-4d42-8d7e-37293c4e6dff" />


Примечание: Облачная ВМ с Linux в данной задаче не подойдёт из-за ограничений облачного провайдера. У вас просто не установится virtualbox.

## Задача 2

1. Убедитесь, что у вас есть ssh ключ в ОС или создайте его с помощью команды ```ssh-keygen -t ed25519```
2. Создайте виртуальную машину Virtualbox с помощью Vagrant и  [Vagrantfile](https://github.com/world12hub/virtd-homeworks-shvirtd-1/blob/main/virtd-homeworks-shvirtd-1/05-virt-02-iaac/src/Vagrantfile) в директории src.
3. Зайдите внутрь ВМ и убедитесь, что Docker установлен с помощью команды:
```
docker version && docker compose version
```

3. Если Vagrant выдаёт ошибку (блокировка трафика):
```
URL: ["https://vagrantcloud.com/bento/ubuntu-20.04"]     
Error: The requested URL returned error: 404:
```

Выполните следующие действия:

- Используйте [зеркало](https://vagrant.elab.pro/downloads/) файл-образ "bento/ubuntu-24.04".

**Важно:**    
- Если ваша хостовая рабочая станция - это windows ОС, то у вас могут возникнуть проблемы со вложенной виртуализацией. Ознакомиться со cпособами решения можно [по ссылке](https://www.comss.ru/page.php?id=7726).

- Если вы устанавливали hyper-v или docker desktop, то  все равно может возникать ошибка:  
`Stderr: VBoxManage: error: AMD-V VT-X is not available (VERR_SVM_NO_SVM)`   
 Попробуйте в этом случае выполнить в Windows от администратора команду `bcdedit /set hypervisorlaunchtype off` и перезагрузиться.

- Если ваша рабочая станция в меру различных факторов не может запустить вложенную виртуализацию - допускается неполное выполнение(до ошибки запуска ВМ)

### Ответ к задаче 2
1. Создана виртуальная машина с hostname=server
   <img width="681" height="182" alt="image" src="https://github.com/user-attachments/assets/bb8bd706-9ccc-4479-b4ef-603a5795d00b" />
2. Информация после входа в созданную виртуальную машину
   <img width="785" height="466" alt="image" src="https://github.com/user-attachments/assets/88f72006-714e-42ad-87e3-0897db811ed8" />
3. Команда docker version && docker compose version
   <img width="777" height="282" alt="image" src="https://github.com/user-attachments/assets/5f7738d9-b806-48a5-b056-36f6c3630a7e" />


## Задача 3

1. Отредактируйте файл    [mydebian.json.pkr.hcl](https://github.com/netology-code/virtd-homeworks/blob/shvirtd-1/05-virt-02-iaac/src/mydebian.json.pkr.hcl)  или [mydebian.jsonl](https://github.com/netology-code/virtd-homeworks/blob/shvirtd-1/05-virt-02-iaac/src/mydebian.json) в директории src (packer умеет и в json, и в hcl форматы):
   - добавьте в скрипт установку docker. Возьмите скрипт установки для debian из  [документации](https://docs.docker.com/engine/install/debian/)  к docker, 
   - дополнительно установите в данном образе htop и tmux.(не забудьте про ключ автоматического подтверждения установки для apt)
3. Найдите свой образ в web консоли yandex_cloud
4. Необязательное задание(*): найдите в документации yandex cloud как найти свой образ с помощью утилиты командной строки "yc cli".
5. Создайте новую ВМ (минимальные параметры) в облаке, используя данный образ.
6. Подключитесь по ssh и убедитесь в наличии установленного docker.
7. Удалите ВМ и образ.
8. **ВНИМАНИЕ!** Никогда не выкладываете oauth token от облака в git-репозиторий! Утечка секретного токена может привести к финансовым потерям. После выполнения задания обязательно удалите секретные данные из файла mydebian.json и mydebian.json.pkr.hcl. (замените содержимое токена на  "ххххх")
9. В качестве ответа на задание  загрузите результирующий файл в ваш ЛК.

### Ответ к задаче 3
1. Отредактирован файл mydebian.json и добавлена установка docker, а также дополнительно добавлены в установку htop и tmux.
   Выполнена команда "yc compute image list".
   Итого:
   
   <img width="848" height="238" alt="image" src="https://github.com/user-attachments/assets/ef08da22-373d-4e78-b08b-131e3aa7287e" />
   
3. В web консоли yandex cloude имеется образ debian-11
   
   <img width="909" height="100" alt="image" src="https://github.com/user-attachments/assets/2df0bfdd-b44b-415d-b172-7a49b03b97e2" />
   
4. Создана виртуальная машина в облаке, используя образ

<img width="1796" height="99" alt="image" src="https://github.com/user-attachments/assets/10677229-0a52-43cf-b98f-38a692e11b85" />

5. Осуществлено подключение по ssh и проверка в наличии установленного docker

<img width="892" height="76" alt="image" src="https://github.com/user-attachments/assets/43002268-64a7-40fd-99c9-d1c1f2364115" />

6. Файл [mydebian.json](https://github.com/world12hub/virtd-homeworks-shvirtd-1/blob/main/virtd-homeworks-shvirtd-1/05-virt-02-iaac/src/mydebian.json) для создания образа 

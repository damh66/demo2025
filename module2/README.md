# *Demo2025 - Модуль 2*

### **[Задание](https://github.com/damh66/demo2025/blob/main/%D0%9A%D0%9E%D0%94%2009.02.06-1-2025%20%D0%A2%D0%BE%D0%BC%201%20(%D1%81%D0%BE%D0%BA%D1%80).pdf)**

#

### Содержание

1. **[Настройте доменный контроллер Samba на машине BR-SRV](https://github.com/damh66/demo2025/blob/main/module2/README.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-1)**
    > Не решено
2. **[Сконфигурируйте файловое хранилище](https://github.com/damh66/demo2025/blob/main/module2/README.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-2)**

3. **[Настройте службу сетевого времени на базе сервиса chrony](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-3)**

4. **[Сконфигурируйте ansible на сервере BR-SRV](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-4)**

5. **[Развертывание приложений в Docker на сервере BR-SRV](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-5)**
    > Решено не полностью (проблема в **учетной записи пользователя базы данных**)
6. **[На маршрутизаторах сконфигурируйте статическую трансляцию портов](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-6)**

7. **[Запустите сервис moodle на сервере HQ-SRV](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-7)**
<br/>

<br/>

<p align="center">
  <img width="450" height="600" src="https://github.com/user-attachments/assets/8ee209f5-6fed-4f03-bbe3-e202155957b3"
<p\>
<p align="center"><strong>Топология</strong></p>

<br/>

## Задание 1

### Настройте доменный контроллер Samba на машине BR-SRV

- Создайте 5 пользователей для офиса HQ: имена пользователей фомата user№.hq. Создайте группу hq, введите в эту группу созданных пользователей

- Введите в домен машину HQ-CLI

- Пользователи группы hq имеют право аутентифицироваться на клиентском ПК

- Пользователи группы hq должны иметь возможность повышать привилегии для выполнения ограниченного набора команд: cat, grep, id. Запускать другие команды с повышенными привилегиями пользователи не имеют права

- Выполните импорт пользователей из файла users.csv. Файл будет располагаться н авиртуальной машине BR-SRV в папке /opt

<br/>

<details>
<summary>Не решено</summary>
<br/>

</details>

<br/>

## Задание 2

### Сконфигурируйте файловое хранилище

- При помощи трех дополнительных дисков, размером 1Гб каждый, на HQ-SRV сконфигурируйте дисковый массив уровня 5

- Имя устройства - md0, конфигурация массива размещается в файле /etc/mdadm.conf

- Обеспечьте автоматическое монтирование в папку /raid5

- Создайте раздел, отформатируйте раздел, в качестве файловой системы используйте ext4

- Настройте сервер сетевой файловой системы (nfs), в качестве папки общего доступа выберите /raid5/nfs, доступ для чтения и записи для всей сети в сторону HQ-CLI

- На HQ-CLI настройте автомонтирование в папку /mnt/nfs

- Основные параметры сервера отметьте в отчете

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Создание RAID

Просматриваем имена добавленных дисков:
```yml
lsblk
```
> Вывод:
> ```yml
> sdb  8:16  0  1G  0  disk
> sdc  8:32  0  1G  0  disk
> sdd  8:48  0  1G  0  disk
> ```

<br/>

Обнуляем суперблоки для добавленных дисков:
```yml
mdadm --zero-superblock --force /dev/sd{b,c,d}
```
> Вывод:
> ```yml
> mdadm: Unrecongised md component device - /dev/sdx
> ```
> > Гласит о том, что диски не использовались ранее для **RAID**

<br/>

Удаляем старые метаданные и подпись на дисках:
```yml
wipefs --all --force /dev/sd{b,c,d}
```

<br/>

Создаем **RAID**:
```yml
mdadm --create /dev/md0 -l 5 -n 3 /dev/sd{b,c,d}
```
> **/dev/md0** - название RAID после сборки
>
> **-l 5** - уровень RAID
>
> **-n 3** - количество дисков, из которых собирается массив
>
> **/dev/sd{b,c,d}** - диски, из которых выполняется сборка

<br/>

Проверяем:
```yml
lsblk
```
> Вывод:
> ```yml
> sdb  8:16  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> sdc  8:32  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> sdd  8:48  0  1G  0  disk
>   md0  9:0  0  2G  0  raid5
> ```

<br/>

Создаем файловую систему из созданного **RAID**:
```yml
mkfs -t ext4 /dev/md0
```

<br/>

#### Создание файла `mdadm.conf`

Создаем директорию для файла:
```yml
mkdir /etc/mdadm
```

<br/>

Заполняем файл информацией:
```yml
echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
mdadm --detail --scan | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf
```

<br/>

#### Создание файловой системы и монтирование RAID-массива

Создаем директорию для монтирования массива:
```yml
mkdir /mnt/raid5
```

<br/>

Добавляем строку в **`/etc/fstab`**:
```yml
/dev/md0  /mnt/raid5  ext4  defaults  0  0
```

<br/>

Монтируем:
```yml
mount -a
```

<br/>

Проверяем монтирование:
```yml
df -h
```
> Вывод:
> ```yml
> /dev/md0  2.0G  24K  1.9G  1%  /mnt/raid5
> ```

<br/>

#### Настройка NFS

Устанавливаем пакеты для **NFS-сервера**:
```yml
apt-get install -y nfs-{server,utils}
```

<br/>

Создаем директорию для общего доступа:
```yml
mkdir /mnt/raid5/nfs
```

<br/>

Выдаем права на чтение и запись этой директории:
```yml
chmod 766 /mnt/raid5/nfs
```

<br/>

Добавляем строку в **`/etc/exports`**:
```yml
/mnt/raid5/nfs 192.168.200.0/28(rw,no_root_squash)
```
> **/mnt/raid5/nfs** - общий ресурс
>
> **192.168.200.0/28** - клиентская сеть, которой разрешено монтирование общего ресурса
>
> **rw** — разрешены чтение и запись
>
> **no_root_squash** — отключение ограничения прав **root**

<br/>

Экспортируем файловую систему, которую прописали ранее:
```yml
exportfs -arv
```
> **-a** - экспортировать все указанные каталоги
>
> **-r** - повторный экспорт всех каталогов, синхронизируя **/var/lib/nfs/etab** с **/etc/exports** и файлами в **/etc/exports.d**
>
> **-v** - подробный вывод

<br/>

Запускаем и добавляем в автозагрузку **NFS-сервер**:
```yml
systemctl enable --now nfs-server
```

<br/>

#### Настройка клиента

Устанавливаем требуемые пакеты для **NFS-клиента**:
```yml
apt-get update && apt-get install -y nfs-{utils,clients}
```

<br/>

Создаем директорию для общего ресурса:
```yml
mkdir /mnt/nfs
```

<br/>

Выдаем права этой директории:
```yml
chmod 777 /mnt/nfs
```

<br/>

Добавляем строку в **`/etc/fstab`** для автоматического монтирования общего ресурса:
```yml
192.168.100.62:/mnt/raid5/nfs  /mnt/nfs  nfs  defaults  0  0
```

<br/>

Монтируем общий ресурс:
```yml
mount -a
```

<br/>

Проверяем монтирование:
```yml
df -h
```
> Вывод:
> ```yml
> 192.168.100.62:/mnt/raid5/nfs  2,0G  0  1,9G  0%  /mnt/nfs
> ```
</details>

<br/>

## Задание 3

### Настройте службу сетевого времени на базе сервиса chrony

- В качества сервера выступает HQ-RTR

- На HQ-RTR настройте сервер chrony, выберите стратум 5

- В качестве клиентов настройте HQ-SRV, HQ-CLI, BR-RTR, BR-SRV

<br/>

<details>
<summary>Решение</summary>
<br/>

**Так как на HQ-RTR нет утилиты chrony и возможность выбора стратума, NTP-сервером будет выступать ISP**

#### Конфигурация NTP-сервера (ISP)

Скачиваем пакет **chrony**:
```yml
apt-get install -y chrony
```

<br/>

Приводим начало файла **`/etc/chrony.conf`** к следующему виду:
```yml
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (https://www.pool.ntp.org/join.html
#pool pool.ntp.org iburst

server 127.0.0.1 iburst prefer
hwtimestamp *
local stratum 5
allow 0/0
```
> **server 127.0.0.1** - указываем сервером синхронизации самого себя
> > **iburst** - принудительно отправляет пакеты для точности синхронизации
> > 
> > **prefer** - отдает приоритет этому серверу
>
> **hwtimestamp** * - указывает сетевой интерфейс как собственный источник времени и синхронизирует клиентов с ним
>
> **local stratum 5** - указание иерархического уровня
>
> **allow 0/0** - разрешает подключение с любого IP-адреса

<br/>

Запускаем и добавляем в автозагрузку утилиту **chronyd**:
```yml
systemctl enable --now chronyd
```

<br/>

#### Проверка конфигурации NTP-сервера

Получаем вывод источников времени с помощью команды:
```yml
chronyc sources
```
> Вывод:
> ```yml
> MS Name/IP address        Stratum  Poll  Reach  LastRx  Last  sample
> =============================================================================
> ^/ localhost.localdomain  0        8     377    -       +0ns[  +0ns] +/-  0ns
> ```

<br/>

Получаем вывод **уровня стратума** с помощью связки команд:
```yml
chronyc tracking | grep Stratum
```
> Вывод:
> ```yml
> Stratum: 5
> ```

<br/>

#### Конфигурация NTP-клиента EcoRouter

Указываем IP-адрес **NTP-сервера**:
```yml
ntp server 172.16.4.1
```

<br/>

Указываем часовой пояс:
```yml
ntp timezone utc+5
```

<br/>

#### Проверка конфигурации NTP-клиента EcoRouter

Проверяем командой:
```yml
show ntp status
```
> Вывод:
> ```yml
> Status Description
> *      best
> +      sync
> -      failed
> ?      unknown
>
> ----------------------------------------------------------------------------------------------------
> Status  |  VR name  |  Server  |  Stratum  |  Delay  |  Version  |  Offset  |  Last  |  Source IP
> ----------------------------------------------------------------------------------------------------
>        *|    default|172.16.4.1|          5|   0.0391|          4|    0.0036|    3:26|        

<br/>

#### Конфигурация NTP-клиента Alt Linux

Скачиваем пакет **chrony**:
```yml
apt-get install chrony
```

<br/>

Приводим начало файла **`/etc/chrony.conf`** к следующему виду:
```yml
#pool pool.ntp.org iburst
server 172.16.4.1 iburst prefer
```
> **iburst** - принудительно отправляет пакеты для точности синхронизации
>
> **prefer** - отдает приоритет этому серверу

<br/>

Запускаем утилиту **chrony** и добавляем ее в автозагрузку:
```yml
systemctl enable --now chronyd
```

<br/>

#### Проверка конфигурации NTP-клиента Alt Linux

Проверка NTP-клиента на Alt Linux аналогична **[проверке NTP-сервера на Alt Linux](https://github.com/damh66/demo2025/tree/main/module2#%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D0%B8-ntp-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0)**

</details>

<br/>

## Задание 4

### Сконфигурируйте ansible на сервере BR-SRV

- Сформируйте файл инвентаря, в инвентарь должны входить HQ-SRV, HQ-CLI, HQ-RTR и BR-RTR

- Рабочий каталог ansible должен располагаться в /etc/ansible

- Все указанные машины должны без предупреждений и ошибок отвечать pong на команду ping в ansible посланную с BR-SRV

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Конфигурация SSH Alt Linux

Затронутые строки в конфигурационном файле **SSH** **`/etc/openssh/sshd_config`** должны выглядеть следующим образом:
```yml
Port 2024
MaxAuthTries 2
PubkeyAuthentication yes
PasswordAuthentication yes
Banner /etc/openssh/bannermotd
AllowUsers  sshuser
```
> Первоначальная настройка **SSH** производилась в задании **[Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV](https://github.com/damh66/demo2025/tree/main/module1#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-5)** из **[Модуля 1](https://github.com/damh66/demo2025/tree/main/module1#demo2025---%D0%BC%D0%BE%D0%B4%D1%83%D0%BB%D1%8C-1)**

<br/>

#### Конфигурация Ansible

Устанавливаем необходимые пакеты:
```yml
apt-get install -y ansible sshpass
```

<br/>

Редактируем указанные строки в **конфигурационном файле `/etc/ansible/ansible.cfg`**:
```yml
inventory = ./inventory.yml
host_key_checking = False
```
> **inventory = ./inventory.yml** - путь до инвентарного файла
>
> **host_key_checking = False** - отключение проверки ключа хоста

<br/>

Далее заполняем **инвентарный файл `/etc/ansible/inventory.yml`**:
```yml
all:
  children:
    Networking:
      hosts:
        hq-rtr:
        br-rtr:
    Servers:
      hosts:
        hq-srv:
          ansible_host: 192.168.100.62
          ansible_port: 2024
    Clients:
      hosts:
        hq-cli:
          ansible_host: 192.168.200.14
          ansible_port: 2024
```

<br/>

Создаем файлы с переменными для **всех категорий** и для категории **Networking**:
```yml
cd /etc/ansible
mkdir group_vars
touch group_vars/{all.yml,Networking.yml}
```

<br/>

Редактируем их:
```yml
ansible_ssh_user: sshuser
ansible_ssh_pass: P@ssw0rd
ansible_python_interpreter: /usr/bin/python3
```
> all.yml

```yml
ansible_connection: network_cli
ansible_network_os: ios
```
> Networking.yml

<br/>

Выполняем команду для **ping**`а всех машин:
```yml
ansible -m ping all
```
> **-m (--module-name)** - параметр для указания модуля
>
> **ping** - модуль
>
> **all** - выполнить модуль для всех виртуальных машин, указанных в инвентарном файле

<br/>

</details>

<br/>

## Задание 5

### Развертывание приложений в Docker на сервере BR-SRV

- Создайте в домашней директории пользователя файл wiki.yml для приложения MediaWiki

- Средствами docker compose должен создаваться стек контейнеров с приложением MediaWiki и базой данных

- Используйте два сервиса

- Основной контейнер MediaWiki должен называться wiki и использовать образ mediawiki

- Файл LocalSettings.php с корректными настройками должен находиться в домашней папке пользователя и автоматически монтироваться в образ

- Контейнер с базой данных должен называться mariadb и использовать образ mariadb

- Разверните

- Он должен создавать базу с названием mediawiki, доступную по стандарнтому порту, пользователя wiki с паролем WikiP@ssw0rd должен иметь права доступа к этой базе данных

- MediaWiki должна быть доступна извне через порт 8080

<br/>

<details>
<summary>Решено не полностью (проблема в учетной записи пользователя базы данных)</summary>
<br/>

#### Конфигурация файла Docker-Compose

Останавливаем службу **ahttpd**, которая занимает порт **8080**:
```yml
systemctl disable —now ahttpd
```
> **ahttpd** - модуль для веб-интерфейса, который предназначен для управления настройками web-сервера, обеспечивающего работоспособность **Центра управления системой**

<br/>

Устанавливаем **docker** и **docker-compose**:
```yml
apt-get install -y docker-{ce,compose}
```

<br/>

Включаем и добавляем в автозагрузку **docker**:
```yml
systemctl enable --now docker
```

<br/>

В домашней директории пользователя создаем файл **`wiki.yml`** и прописываем следующее:
```yml
services:
  mediawiki:
    container_name: wiki
    image: mediawiki
    restart: always
    ports:
      - "8080:80"
    links:
      - db
#    volumes:
#      - ./LocalSettings.php:/var/www/html/LocalSettings.php

  db:
    container_name: mariadb
    image: mariadb
    restart: always
    environment:
      MARIADB_DATABASE: mediawiki
      MARIADB_USER: wiki
      MARIADB_PASSWORD: WikiP@ssw0rd
      MARIADB_ROOT_PASSWORD: P@ssw0rd
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```
> **services** - основной раздел, в котором описываются сервисы
>
> **container_name** - имя контейнера
>
> **image** - имя образа
>
> **restart** - перезапуск контейнера, если он остановлен
>
> **ports** - проброс портов
>
> **links** - ссылка на контейнер
> 
> **volumes** - проброс папок
>
> **environment** - переменные окружения

<br/>

Собираем стек контейнеров:
```yml
docker compose -f wiki.yml up -d
```
> **-f** - указание на файл
>
> **up** - запуск
>
> **-d** - запуск в фоновом режиме

<br/>

#### Установка MediaWiki в веб-интерфейсе

На **HQ-CLI** в браузере вводим **`http://192.168.0.30:8080`** и начинаем установку **MediaWiki**, нажав на **set up the wiki**:
<p align="center">
  <img width="600" src="https://github.com/user-attachments/assets/d396a8fb-486e-4cdf-96b2-d7233b5e81f3"
</p>

<br/>

Выбираем язык:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/c59c1003-75e6-4dbb-bfa2-9808fa31d51f"
</p>

<br/>

Проверяем внешнюю среду и нажимаем **далее**:
<p align="center">
  <img width="600" src="https://github.com/user-attachments/assets/b09a45e6-eb92-4d8f-b2c4-4a280352bdf0"
</p>

<br/>

Заполняем параметры для базы данных в соответствии с заданными переменными окружения в **wiki.yml**:
<p align="center">
  <img width="250" src="https://github.com/user-attachments/assets/edbfc738-5406-4d3f-a73f-9cdad1f15398"
</p>

<br/>

Оставляем галочку и жмем **далее**:
<p align="center">
  <img width="550" src="https://github.com/user-attachments/assets/2ebdc0e0-027d-4aff-9d00-4c1b4e38c358"
</p>

<br/>

Заполняем информацию об учетной записи администратора:
<p align="center">
  <img width="500" src="https://github.com/user-attachments/assets/0b60037a-7e9f-4f0f-9290-a4d0b2f457fb"
</p>

<br/>

Подтверждаем установку **MediaWiki**:
<p align="center">
  <img width="500" src="https://github.com/user-attachments/assets/d338a392-ce13-414c-ba16-2e45743d1d4f"
</p>

<br/>

После окончания установки нажимаем **далее**:
<p align="center">
  <img width="450" src="https://github.com/user-attachments/assets/158beb5a-9715-46cc-8af5-1e3e0b05176d"
</p>

<br/>

Получаем конфигурационный файл, который нужно передать на **BR-SRV**:
<p align="center">
  <img width="800" src="https://github.com/user-attachments/assets/923ad086-0244-4b4b-be83-1973c193f355"
</p>

<br/>

#### Правка файла Docker-Compose

Перемещаем файл **`LocalSettings.php`** в домашнюю директорию пользователя **sshuser**:
```yml
mv /home/user/Загрузки/LocalSettings.php /home/sshuser
```
> В моем случае, ранние действия выполнялись из под пользователя **user**, поэтому загруженный файл оказался именно в его папке

<br/>

Передаем файл с **HQ-CLI** на **BR-SRV**:
```yml
scp -P 2024 /home/sshuser/LocalSettings.php sshuser@192.168.0.30:/home/sshuser
```
> **-P** - указание порта SSH
>
> **/home/sshuser/LocalSettings.php** - файл, который будет передан
>
> **sshuser@192.168.0.30:/home/sshuser** - имя-пользователя@IP-адрес:директория-назначения

<br/>

На **BR-SRV** перемещаем файл в домашнюю директорию **root**:
```yml
mv /home/sshuser/LocalSettings.php /root
```
> Если файл **wiki.yml** создавали в домашней директории другого пользователя - перемещаем туда

<br/>

В файле **wiki.yml** расскоментируем следующие строки:
```yml
volumes:
  - ./LocalSettings.php:/var/www/html/LocalSettings.php
```

<br/>

Перезапускаем запущенные **Docker**`ом сервисы:
```yml
docker compose -f wiki.yml stop
docker compose -f wiki.yml up -d
```

</details>

<br/>

## Задание 6

### На маршрутизаторах сконфигурируйте статическую трансляцию портов

- Пробросьте порт 80 в порт 8080 на BR-SRV на маршрутизаторе BR-RTR, для обеспечения работы сервиса wiki

- Пробросьте порт 2024 в порт 2024 на HQ-SRV на маршрутизаторе HQ-RTR

- Пробросьте порт 2024 в порт 2024 на BR-SRV на маршрутизаторе BR-RTR

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Конфигурация BR-RTR

Проброс портов с 80 на 8080 для работы сервиса **wiki**:
```yml
ip nat source static tcp 192.168.0.1 80 192.168.0.30 8080
```

<br/>

Проброс портов с 2024 на 2024:
```yml
ip nat source static tcp 192.168.0.1 2024 192.168.0.30 2024
```

<br/>

#### Конфигурация HQ-RTR

Проброс портов с 2024 на 2024:
```yml
ip nat source static tcp 192.168.100.1 2024 192.168.100.62 2024
```

</details>

<br/>

## Задание 7

### Запустите сервис moodle на сервере HQ-SRV

- Используйте веб-сервер apache

- В качестве системы управления базами данных используйте mariadb

- Создайте базу данных moodledb

- Создайте пользователя moodle с паролем P@ssw0rd и предоставьте ему права доступа к этой базе данных

- У пользователя admin в системе обучения задайте пароль P@ssw0rd

- На главной странице должен отражаться номер рабочего места в виде арабской цифры, других подписей делать не надо

- Основные параметры отметьте в отчеты

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Конфигурация базы данных

Устанавливаем необходимые пакеты:
```yml
apt-get install -y moodle moodle-apache2 moodle-base moodle-local-mysql phpMyAdmin
```

<br/>

Добавляем в **автозагрузку** базу данных:
```yml
systemctl enable --now mysqld
```

<br/>

Задаем пароль для пользователя **root** в базе данных:
```yml
mysqladmin password 'P@ssw0rd'
```

<br/>

Редактируем настройки **веб-сервера**:
```yml
cat /etc/httpd2/conf/include/Directory_moodle_default.conf | grep 'Require all granted' || sed -i '/AllowOverride None/a Require all granted' /etc/httpd2/conf/include/Directory_moodle_default.conf
```

<br/>

Изменяем строку, отвечающую за количество входных переменных:
```yml
sed -i 's/; max_input_vars = 1000/max_input_vars = 5000/g' /etc/php/8.2/apache2-mod_php/php.ini
```

<br/>

Добавляем в **автозагрузку** веб-сервер:
```yml
systemctl enable --now httpd2
```

<br/>

Авторизуемся в **MySQL**:
```yml
mysql -u root -p
```
> Вводим ранее указанный пароль

<br/>

Создаем **пользователя** для базы данных:
```yml
create user 'moodle'@'localhost' identified by 'P@ssw0rd';
```

<br/>

Создаем базу данных:
```yml
create database moodledb default character set utf8 collate utf8_unicode_ci;
```

<br/>

Выдаем **права** пользователю на созданную базу данных:
```yml
grant all privileges on moodledb.* to moodle@localhost;
```

<br/>

Переходим на **`hq-srv.au-team.irpo/moodle`** и выбираем язык:
<p align="center">
  <img width="600" src="https://github.com/user-attachments/assets/0e8cdfda-6466-416e-afab-e7f61fcf1b3a"
</p>

<br/>

Подтверждаем пути директорий:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/548b1126-3b5a-4104-a692-b0aafdd0617a"
</p>

<br/>

Выбираем систему управления базы данных:
<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/d21aa42f-8988-44f6-bcab-67e0fa9cae08"
</p>

<br/>

Заполняем данные о базе данных и пользователе:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/1c5414a6-5aba-4ffd-8d4a-32e49bcb770b"
</p>

<br/>

Соглашаемся с условиями:
<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/055ed45c-f87c-44ec-9f3e-0f32737f38c7"
</p>

<br/>

Убеждаемся в успешной проверке:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/7ddc9e35-3c96-4623-94d0-9f57e2803b3c"
</p>

<br/>

После установки настраиваем учетную запись администратора:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/337888dc-1542-4bdd-90c2-b43dcf14e7fa"
</p>

> Заполняем в соответствии с условиями задания

<br/>

Указываем название сайта, часовой пояс и электронную почту:
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/494d4a54-dad8-4e60-adc8-f415671bef4a"
</p>
<p align="center">
  <img width="400" src="https://github.com/user-attachments/assets/eca69476-dedd-4fc6-9198-4c7c27d7a428"
</p>

<br/>

После успешного создания попадаем на главную страницу:
<p align="center">
  <img width="600" src="https://github.com/user-attachments/assets/9ae25d22-7cf0-4b8c-8fd9-c1f538679d82"
</p>

</details>

<br/>

# *Demo2025 - Модуль 2*

### **[Задание](https://github.com/damh66/demo2025/blob/main/%D0%9A%D0%9E%D0%94%2009.02.06-1-2025%20%D0%A2%D0%BE%D0%BC%201%20(%D1%81%D0%BE%D0%BA%D1%80).pdf)**

#

### Содержание

1. **[Настройте доменный контроллер Samba на машине BR-SRV](https://github.com/damh66/demo2025/blob/main/module2/README.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-1)**

2. **[Сконфигурируйте файловое хранилище](https://github.com/damh66/demo2025/blob/main/module2/README.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-2)**

3. **[Настройте службу сетевого времени на базе сервиса chrony](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-3)**

4. **[Сконфигурируйте ansible на сервере BR-SRV](https://github.com/damh66/demo2025/tree/main/module2#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-4)**

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

#### Конфигурация NTP-клиента Alt Linux

Скачиваем пакет **chrony**:
```yml
apt-get install chrony
```

<br/>

Добавляем строку в **`/etc/chrony.conf`**:
```yml
echo "server 172.16.4.1 iburst prefer >> /etc/chrony.conf
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
<summary>Решение (не полностью)</summary>
<br/>

#### Установка Python

Скачиваем архив с последней версией **Python** во временную директорию **`/tmp`**:
```yml
wget https://www.python.org/ftp/python/3.14.0/Python-3.14.0a1.tgz
```

<br/>

Разархивируем скачанный архив:
```yml
tar zxvf Python-3.14.0a1.tgz
```

<br/>

Копируем полученную папку и сразу переходим в нее:
```yml
cp -r Python-3.14.0a1 /usr/local/bin
```

<br/>

Устанавливаем зависимости для компиляции:
```yml
apt-get install zlib-devel libssl-devel libsqlite3-devel libffi-devel gcc pip
```

<br/>

Добавляем параметры для компиляции:
```yml
./configure --prefix=/usr/local --with-ensurepip=install
```
> **--prefix=/usr/local** - указание корневой директории
>
> **--with-ensurepip=install** - обеспечивает поддержку начальной загрузки установщика pip в виртуальную среду или существующую установку Python

<br/>

Командой **`python3.14`** проверяем, что **Python** установился, получив следующий вывод:
```yml
Python 3.14.0a1 (main, Nov 2 2024, 28:18:22) [GCC 10.3.1 20210703 (ALT Sisyphus 10.3.1-alt2)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

<br/>

#### Создание виртуального окружения

Создаем директорию для **ansible**:
```yml
mkdir /etc/ansible
```

</br>

В ранее созданной директории создаем виртуальной окружение:
```yml
python3.14 -m vent .env
```

<br/>

Активируем его:
```yml
source .env/bin/activate
```

<br/>

После активации окружения мы увидим изменения оболочки:
```yml
(.env) [root@br-srv ~]#
```

</details>

# Demo2025 - Модуль 1

[Задание](https://github.com/damh66/demo2025/blob/main/%D0%9A%D0%9E%D0%94%2009.02.06-1-2025%20%D0%A2%D0%BE%D0%BC%201.pdf)

[Задание (сокращенное)](https://github.com/damh66/demo2025/blob/main/%D0%9A%D0%9E%D0%94%2009.02.06-1-2025%20%D0%A2%D0%BE%D0%BC%201%20(%D1%81%D0%BE%D0%BA%D1%80).pdf)

## Топология:

<p align="center">
  <img width="450" height="600" src="https://github.com/user-attachments/assets/8ee209f5-6fed-4f03-bbe3-e202155957b3"
<p\>

<br/>

## Задание 1

**Произведите базовую настройку устройств**

- Настройте имена устройств согласно топологии. Используйте полное доменное имя

- На всех устройствах необходимо сконфигурировать IPv4

- IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918

- Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов

- Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов

- Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов

- Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов

- Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3

<br/>

<details>
<summary>Решение</summary>
<br/>
  
**Настройка имен устройств на ALT Linux:**
```
hostnamectl set-hostname <FQDN>; exec bash
```
> FQDN (Fully Qualified Domain Name) - полное доменное имя
> 
> `exec bash` - обновление оболочки

<br/>

**Настройка имен устройств на EcoRouter:**

Переходим в режим конфигурации и прописываем следующее:
```
hostname <name>
```
> `<name>` - желаемое имя устройства

<br/>

**Таблица подсетей**

<table align="center">
  <tr>
    <td align="center">Сеть</td>
    <td align="center">Адрес подсети</td>
    <td align="center">Пул-адресов</td>
  </tr>
  <tr>
    <td align="center">SRV-Net (VLAN 100)</td>
    <td align="center">192.168.100.0/26</td>
    <td align="center">192.168.100.1 - 192.168.100.62</td>
  </tr>
  <tr>
    <td align="center">CLI-Net (VLAN 200)</td>
    <td align="center">192.168.200.0/28</td>
    <td align="center">192.168.200.1 - 192.168.200.14</td>
  </tr>
  <tr>
    <td align="center">BR-Net</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1 - 192.168.0.30</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1 - 192.168.99.6</td>
  </tr>
  <tr>
    <td align="center">ISP-HQ</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 172.16.4.14</td>
  </tr>
  <tr>
    <td align="center">ISP-BR</td>
    <td align="center">172.16.5.0/28</td>
    <td align="center">172.16.5.1 - 172.16.5.14</td>
  </tr>
</table>

<br/>

**Таблица адресации**

<table align="center">
  <tr>
    <td align="center">Имя устройства</td>
    <td align="center">Интерфейс</td>
    <td align="center">IPv4/IPv6</td>
    <td align="center" >Маска/Префикс</td>
    <td align="center">Шлюз</td>
  </tr>
  <tr>
    <td align="center" rowspan="3">ISP</td>
    <td align="center">ens33</td>
    <td align="center">10.12.28.5 (DHCP)</td>
    <td align="center">/24</td>
    <td align="center">10.12.28.254</td>
  </tr>
  <tr>
    <td align="center">ens34</td>
    <td align="center">172.16.5.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">ens35</td>
    <td align="center">172.16.4.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="3">HQ-RTR</td>
    <td align="center">int0</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
  </tr>
  <tr>
    <td align="center">int1</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">int2</td>
    <td align="center">192.168.200.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">int0</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
  </tr>
  <tr>
    <td align="center">int1</td>
    <td align="center">192.168.0.1</td>
    <td align="center">/27</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">ens33</td>
    <td align="center">192.168.100.62</td>
    <td align="center">/26</td>
    <td align="center">192.168.100.1</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">ens33</td>
    <td align="center">192.168.0.30</td>
    <td align="center">/27</td>
    <td align="center">192.168.0.1</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">ens33</td>
    <td align="center">192.168.200.14</td>
    <td align="center">/28</td>
    <td align="center">192.168.200.1</td>
  </tr>
</table>

> Адресация для **ISP** взята из следующего задания

<br/>

**Наcтройка IP-адресации на **HQ-SRV**, **BR-SRV**, **HQ-CLI** (настройка IP-адресации на **ISP** производится в следующем задании)** 

Приводим файлы **`options`**, **`ipv4address`**, **`ipv4route`** в директории **`/etc/net/ifaces/*имя интерфейса*/`** к следующему виду (в примере **HQ-SRV**):
```
DISABLED=no
TYPE=eth
BOOTPROTO=static
CONFIG_IPV4=yes
```
> **`options`**

```
192.168.100.62/26
```
> **`ipv4address`**

```
default via 192.168.100.1
```
> **`ipv4route`**

<br/>

**Настройка IP-адресации на EcoRouter**

Настраиваем интерфейс на **HQ-RTR**, который смотрит в сторону **ISP**:

- Создаем логический интерфейс:
```
interface int0
  description "to isp"
  ip address 172.16.4.2/28
```

- Настраиваем физический порт:
```
port ge0
  service-instance ge0/int0
    encapsulation untagged
```

- Объединеняем порт с интерфейсом:
```
interface int0
  connect port ge0 service-instance ge0/int0
```

<br/>

Настраиваем интерфейсы на **HQ-RTR**, которые смотрят в сторону **HQ-SRV** и **HQ-CLI** (с разделением на VLAN):

- Создаем два интерфейса:
```
interface int1
  description "to hq-srv"
  ip address 192.168.100.1/26
!
interface int2
  description "to hq-cli"
  ip address 192.168.200.1/28
```

- Настраиваем порт:
```
port ge1
  service-instance ge1/int1
    encapsulation dot1q 100
    rewrite pop 1
  service-instance ge1/int2
    encapsulation dot1q 200
    rewrite pop 1
```

- Объединяем порт с интерфейсами:
```
interface int1
  connect port ge1 service-instance ge1/int1
!
interface int2
  connect port ge1 service-instance ge1/int2
```

<br/>

**Адресация на BR-RTR (без разделения на VLAN) настраивается аналогично примеру выше**

<br/>

**Добавление маршрута по умолчанию в EcoRouter**

Прописываем следующее:
```
ip route 0.0.0.0 0.0.0.0 *адрес шлюза*
```

</details>

<br/>

## Задание 2

**Настройка ISP**

- Настройте адресацию на интерфейсах:

  - Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP

  - Настройте маршруты по умолчанию там, где это необходимо

  - Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28

  - Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28

  - На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

<br/>

<details>
<summary>Решение</summary>
<br/>

**Настройка интерфейса, который получает IP-адрес по DHCP**

Файл **`options`** (в директории интерфейса) приводим к следующему виду:
```
BOOTPROTO=dhcp
TYPE=eth
DISABLED=no
CONFIG_IPV4=yes
```
> **`BOOTPROTO=dhcp`** - заменили статический способ настройки адреса на динамическое получение

<br/>

**Настройка маршрута по умолчанию**

Прописываем шлюз по умолчанию:
```
default via *адрес шлюза*
```

<br/>

**Настройка интерфейсов, смотрящих в сторону HQ-RTR и BR-RTR происходит аналогично настройке в Задании 1**

<br/>

**Включение маршрутизации**

В файле **`/etc/net/sysctl.conf`** изменяем строку:
```
net.ipv4.ip_forward = 1
```

<br/>

Изменения в файле **`sysctl.conf`** применяем следующей командой:
```
sysctl -p /etc/sysctl.conf
```

</details>

<br/>

## Задание 3

**Создание локальных учетных записей**

- Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

  - Пароль пользователя sshuser с паролем P@ssw0rd

  - Идентификатор пользователя 1010

  - Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

- Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

  - Пароль пользователя net_admin с паролем P@$$word

  - При настройке на EcoRouter пользователь net_admin должен обладать максимальными привилегиями

  - При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации

<br/>

<details>
<summary>Решение</summary>
<br/>

**Создание пользователя `sshuser` на серверах**

Создаем самого пользователя:
```
useradd sshuser -u 1010
```
> опция **`-u`** позволяет указать идентификатор пользователя сразу при создании

<br/>

Задаем пароль:
```
passwd sshuser
```

<br/>

Добавляем в группу **wheel**:
```
usermod -aG wheel sshuser
```

<br/>

Добавляем строку в **`/etc/sudoers`**:
```
sshuser ALL=(ALL) NOPASSWD:ALL
```
> Позволяет запускать **sudo** без аутентификации 

<br/>

**Создание пользователя `net_admin` на Ecorouter**

Создаем самого пользователя:
```
username net_admin
```

<br/>

Задаем пароль:
```
password P@ssw0rd
```

<br/>

Присваиваем привилегии администратора:
```
role admin
```


</details>

<br/>

## Задание 4

**Настройте на интерфейсе HQ-RTR в сторону офиса HQ виртуальный коммутатор**

- Сервер HQ-SRV должен находиться в ID VLAN 100
- Клиент HQ-CLI в ID VLAN 200
- Создайте подсеть управления с ID VLAN 999
- Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

<br/>

<details>
<summary>Не решено</summary>
<br/>



</details>

<br/>

## Задание 5

**Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV**

- Для подключения используйте порт 2024
- Разрешите подключения только пользователю sshuser
- Ограничьте количество попыток входа до двух
- Настройте баннер «Authorized access only»

<br/>

<details>
<summary>Решение</summary>
<br/>

Приводим указанные строки в файле **`/etc/openssh/sshd_config`** к следующим значениям:
```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/openssh/bannermotd
AllowUsers  sshuser
```
> В параметре **AllowUsers** вместо пробела используется **`Tab`**

<br/>

Создаем файл **`bannermotd`**:
```
----------------------
Authorized access only
----------------------
```

<br/>

Перезагружаем службу:
```
systemctl restart sshd
```

</details>

<br/>

## Задание 6

**Между офисами HQ и BR необходимо сконфигурировать ip туннель**

- Сведения о туннеле занесите в отчёт

- На выбор технологии GRE или IP in IP

<br/>

<details>
<summary>Решение</summary>
<br/>

Создаем интерфейс **GRE**-туннеля на **HQ-RTR**:
```
int tunnel.0
```

<br/>

Назначаем **IP-адрес**:
```
ip add 172.16.0.1/30
```

<br/>

Выставляем параметр **MTU**:
```
ip mtu 1400
```
> В связи с добавлением служебного заголовка появляются новые требования к допустимому значению MTU при передаче пакета. Заголовок GRE имеет размерность 4 байта, 20 байт транспортный IP заголовок, заголовок IP пакета 20 байт, таким образом возникает необходимость задавать размер допустимого MTU на интерфейсах туннеля меньше стандартного значения.

<br/>

Задаем режим работы туннеля **GRE** и адреса **начала** и **конца** туннеля:
```
ip tunnel 172.16.4.2 172.16.5.2 mode gre
```

<br/>

**GRE-туннель на BR-RTR настраивается аналогично примеру выше**

</details>

<br/>

## Задание 7

**Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте link state протокол на ваше усмотрение**

- Разрешите выбранный протокол только на интерфейсах в ip туннеле

- Маршрутизаторы должны делиться маршрутами только друг с другом

- Обеспечьте защиту выбранного протокола посредством парольной защиты

- Сведения о настройке и защите протокола занесите в отчёт

<br/>

<details>
<summary>Решение</summary>
<br/>

Создаем процесс **OSPF**, указываем **идентификатор маршрутизатора**, объявляем сети на **HQ-RTR** и указываем **пассивные** интерфейсы:
```
router ospf 1
  router-id 1.1.1.1
  network 172.16.0.0/30
  network 192.168.100.0/26
  network 192.168.200.0/28
  passive-interface default
  no passive-interface tunnel.0
```

<br/>

**Маршрутизация OSPF на BR-RTR настраивается аналогично примеру выше**

</details>

<br/>

## Задание 8

**Настройка динамической трансляции адресов**

- Настройте динамическую трансляцию адресов для обоих офисов.

- Все устройства в офисах должны иметь доступ к сети Интернет

<br/>

<details>
<summary>Решение</summary>
<br/>

**Настройка NAT на ISP**

Добавляем правила **`iptables`** на ISP
```
iptables -t nat -A POSTROUTING -o ens224 -s 172.16.4.0/28 -j MASQUERADE
iptables -t nat -A POSTROUTING -o ens224 -s 172.16.5.0/28 -j MASQUERADE
```

<br/>

Сохраняем правила:
```
iptables-save > /etc/sysconfig/iptables
```

<br/>

Включаем и добавляем **`iptables`** в автозагрузку:
```
systemctl enable --now iptables
```

<br/>

**Настройка NAT на HQ-RTR**

Указываем **внутренние** и **внешние** интерфейсы:
```
int int1
  ip nat inside
!
int int2
  ip nat inside
!
int int0
  ip nat outside
```

<br/>

Создаем пул:
```
ip nat pool NAT_POOL 192.168.100.1-192.168.100.62,192.168.200.1-192.168.200.14
```

<br/>

Создаем **правило** трансляции адресов, указывая внешний интерфейс:
```
ip nat source dynamic inside-to-outside pool NAT_POOL overload interface int0
```

<br/>

**Настройка NAT на BR-RTR**

Конфигурация:
```
int int1
  ip nat inside
!
int int0
  ip nat outside
!
ip nat pool NAT_POOL 192.168.0.1-192.168.0.30
!
ip nat source dynamic inside-to-outside pool NAT_POOL overload interface int0
```

</details>

<br/>

## Задание 9

**Настройка протокола динамической конфигурации хостов**

- Настройте нужную подсеть

- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.

- Клиентом является машина HQ-CLI.

- Исключите из выдачи адрес маршрутизатора

- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.

- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.

- DNS-суффикс для офисов HQ – au-team.irpo

- Сведения о настройке протокола занесите в отчёт

<br/>

<details>
<summary>Решение</summary>
<br/>

Создаем **пул** для **DHCP-сервера**:
```
ip pool cli_pool 192.168.200.14-192.168.200.14
```

<br/>

Настраиваем сам **DHCP-сервер**:
```
dhcp-server 1
  pool cli-pool 1
  mask 255.255.255.240
  gateway 192.168.200.1
  dns 192.168.100.62
  domain-name au-team.irpo
```
> Привязка **пула**

> Указание **маски** для выдаваемых адресов из пула

> Указание **шлюза по умолчанию** для клиентов

> Указание **DNS-сервера** для клиентов

> Указание **DNS-суффикса** для офиса **HQ**

<br/>

Привязываем **DHCP-сервер** к интерфейсу (смотрящий в сторону **CLI**):
```
interface int2
  dhcp-server 1
```

</details>

<br/>

## Задание 10

**Настройка DNS для офисов HQ и BR**

- Основной DNS-сервер реализован на HQ-SRV.

- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2

- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер

<br/>

<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center">Таблица 2</p>

<br/>

<details>
<summary>Решение</summary>
<br/>

**Настройка конфигурации bind**

Изменяем содержание перечисленных строк в **`/etc/bind/options.conf`** к следующему виду:
```
listen-on { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };

forwarders { 77.88.8.8; };
recursion yes;

allow-query { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };

allow-query-cache { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };

allow-recursion { 127.0.0.1; 192.168.100.0/26; 192.168.200.0/28; 192.168.0.0/27; };
```

<br/>

Конфигурируем ключи **rndc**:
```
rndc-confgen > /etc/rndckey
```
> Делаем вывод в файл, чтобы скопировать оттуда

<br/>

Приводим файл **`/etc/bind/rndc.key`** к следующему виду:
```
//key "rndc-key" {
//  secret "@RNDC_KEY@";
//};

key "rndc-key" {
  algorithm hmac-sha256;
  secret "VTmhjyXFDo0QpaBl3UQWx1e0g9HElS2MiFDtNQzDylo=";
};
```
> Первые строки закомментировали
>
> Вставили ключ **rndc**

<br/>

Проверяем на ошибки:
```
named-checkconf
```

<br/>

Запускаем и добавляем в автозагрузку **`bind`**:
```
systemctl enable --now bind
```

<br/>

Изменяем **`resolv.conf`** интерфейса:
```
search au-team.irpo
nameserver 127.0.0.1
nameserver 192.168.100.62
nameserver 77.88.8.8
search yandex.ru
```

<br/>

**Создание и настройка прямой зоны**

Прописываем ее в **`/etc/bind/local.conf`**:
```
zone "au-team.irpo" {
  type master;
  file "au-team.irpo.db";
};
```

<br/>

Копируем шаблон прямой зоны:
```
cp /etc/bind/zone/localdomain /etc/bind/zone/au-team.irpo.db
```

<br/>

Задаем пользователя и права на файл:
```
chown named. /etc/bind/zone/au-team.irpo.db
chmod 600 /etc/bind/zone/au-team.irpo.db
```

<br/>

Приводим его к следующему виду:
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
        IN      A       192.168.100.62
hq-rtr  IN      A       192.168.100.1
br-rtr  IN      A       192.168.0.1
hq-srv  IN      A       192.168.100.62
hq-cli  IN      A       192.168.200.14
br-srv  IN      A       192.168.0.30
moodle  IN      CNAME   hq-rtr
wiki    IN      CNAME   hq-rtr
```

<br/>

Проверяем на ошибки:
```
named-checkconf -z
```

<br/>

**Создание и настройка обратных зон**

Прописываем ее в **`/etc/bind/local.conf`**:
```
zone "100.168.192.in-addr.arpa" {
  type master;
  file "100.168.192.in-addr.arpa";
};

zone "200.168.192.in-addr.arpa" {
  type master;
  file "200.168.192.in-addr.arpa";
};
```

<br/>

Копируем шаблон обратной зоны:
```
cp /etc/bind/zone/127.in-addr.arpa /etc/bind/zone/100.168.192.in-addr.arpa
cp /etc/bind/zone/127.in-addr.arpa /etc/bind/zone/200.168.192.in-addr.arpa
```

<br/>

Задаем пользователя и права на файл:
```
chown named. /etc/bind/zone/100.168.192.in-addr.arpa
chmod 600 /etc/bind/zone/100.168.192.in-addr.arpa
chown named. /etc/bind/zone/200.168.192.in-addr.arpa
chmod 600 /etc/bind/zone/200.168.192.in-addr.arpa
```

<br/>

Приводим их к следующему виду:
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
1       IN      PTR     hq-rtr.au-team.irpo.
62      IN      PTR     hq-srv.au-team.irpo.
```
```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
14      IN      PTR     hq-cli.au-team.irpo.
```

<br/>

Проверяем на ошибки:
```
named-checkconf -z
```

<br/>

Перезапускаем **`bind`**:
```
systemctl restart bind
```

<br/>

Проверяем работоспособность:
```
nslookup **IP-адрес/DNS-имя**
```

</details>

<br/>

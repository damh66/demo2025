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
<br>
  
**Настройка имен устройств на ALT Linux:**

```
hostnamectl set-hostname <FQDN>; exec bash
```
> FQDN (Fully Qualified Domain Name) - полное доменное имя
> 
> `exec bash` - обновление оболочки

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
    <td align="center" rowspan="2">HQ-RTR</td>
    <td align="center">isp-hq</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
  </tr>
  <tr>
    <td align="center">vl100</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">isp-br</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
  </tr>
  <tr>
    <td align="center">br-net</td>
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

Настройка интерфейса на **HQ-RTR**, который смотрит в сторону **ISP**:

- Создание логического интерфейса:

```
interface int0
  description "to isp"
  ip address 172.16.4.2/28
```

- Настройка физического порта:

```
port ge0
  service-instance ge0/int0
    encapsulation untagged
```

- Объединение порта с интерфейсом:

```
interface int0
  connect port ge0 service-instance ge0/int0
```

Настройка интерфейса на **HQ-RTR**, который смотрит в сторону **HQ-SRV** и **HQ-CLI** (с разделением на VLAN):

- Создание двух интерфейсов:

```
interface int1
  description "to hq-srv"
  ip address 192.168.100.1/26
!
interface int2
  description "to hq-cli"
  ip address 192.168.200.1/28
```

- Настройка порта:

```
port ge1
  service-instance ge1/int1
    encapsulation dot1q 100
    rewrite pop 1
  service-instance ge1/int2
    encapsulation dot1q 200
    rewrite pop 1
```

- Объединение портов с интерфейсами:

```
interface int1
  connect port ge1 service-instance ge1/int1
!
interface int2
  connect port ge1 service-instance ge1/int2
```

<br/>

**Аналогично настройке HQ-RTR настраиваем BR-RTR (без разделения на VLAN)**

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
<br>

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

**Настройка NAT**

Добавляем правило в **`iptables`**:

```
iptables -A POSTROUTING -t nat -j MASQUERADE
```

<br/>

**Включение маршрутизации**

В файле **`/etc/net/sysctl.conf`** изменяем строку:

```
net.ipv4.ip_forward = 1
```




</details>

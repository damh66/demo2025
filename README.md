# Demo2025 - Модуль 1

### Топология:

<p align="center">
  <img width="450" height="600" src="https://github.com/user-attachments/assets/8ee209f5-6fed-4f03-bbe3-e202155957b3"
<p\>

### Задание 1

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
  
**Настройка имен устройств:**
```
hostnamectl set-hostname <FQDN>; exec bash
```
> FQDN (Fully Qualified Domain Name) - полное доменное имя
> 
> `exec bash` - обновление оболочки

<br/>

**Таблица подсетей**

<table align="center">
  <tr>
    <td align="center">Сеть</td>
    <td align="center">Адрес подсети</td>
    <td align="center">Пул-адресов</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV (VLAN 100)</td>
    <td align="center">192.168.100.0/26</td>
    <td align="center">192.168.100.1 - 192.168.100.62</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI (VLAN 200)</td>
    <td align="center">192.168.200.0/28</td>
    <td align="center">192.168.200.1 - 192.168.200.14</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1 - 192.168.0.30</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1 - 192.168.99.6</td>
  </tr>
  <tr>
    <td align="center">ISP - HQ-RTR</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 172.16.4.14</td>
  </tr>
  <tr>
    <td align="center">ISP - BR-RTR</td>
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
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
  <tr>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="2">HQ-RTR</td>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
  <tr>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
  <tr>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">interface</td>
    <td align="center">ip address</td>
    <td align="center">netmask</td>
    <td align="center">gateway</td>
  </tr>
</table>

> Адресация для **ISP** взята из следующего задания

</details>

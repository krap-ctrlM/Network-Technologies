# Network Technologies: Comprehensive Configurations

## Full Configuration of All Devices and Services

### Описание действий по настройке

1. **Настройка Router 0:**
   - Настройка интерфейсов GigabitEthernet с поддержкой VLAN 2 и VLAN 3.
   - Активация RIP v2 и добавление соответствующих сетей.
   - Конфигурация IP-адресации для взаимодействия с другими устройствами.

2. **Настройка Router 1:**
   - Создание VLAN 4 с использованием dot1Q.
   - Активация RIP v2 для маршрутизации между сетями.
   - Конфигурация интерфейсов для подключения к другим маршрутизаторам и устройствам.

3. **Настройка Switch 0:**
   - Конфигурация портов FastEthernet для работы с VLAN 2 и VLAN 3.
   - Включение транка для передачи нескольких VLAN через порт FastEthernet0/2.
   - Активация протокола STP для предотвращения петель в сети.

4. **Настройка Switch 1:**
   - Настройка транковых портов для передачи VLAN 2-4.
   - Назначение порта для VLAN 4.
   - Включение STP для стабильности сети.

5. **Настройка DHCP:**
   - Создание пулов IP-адресов для VLAN 2 и VLAN 3.
   - Указание IP-адресов шлюзов по умолчанию для каждой VLAN.

6. **Настройка RIP:**
   - Добавление всех сетей в маршрутизаторы Router 0 и Router 1.
   - Убедиться, что опция `no auto-summary` включена для предотвращения агрегации маршрутов.

7. **Настройка HTTP и FTP:**
   - Включение служб HTTP и FTP на выделенном сервере.
   - Проверка доступности этих служб из всех VLAN.

8. **Конфигурация STP:**
   - Включение режима PVST для обеспечения стабильности сети и оптимального пути передачи данных.

### Router 0
version 15.1
service timestamps debug datetime msec
service timestamps log datetime msec
hostname Router0

interface GigabitEthernet0/0.2
 encapsulation dot1Q 2
 ip address 192.168.2.1 255.255.255.0
 ip helper-address 192.168.4.100

interface GigabitEthernet0/0.3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0

interface GigabitEthernet0/1
 ip address 10.0.0.1 255.0.0.0

router rip
 version 2
 network 10.0.0.0
 network 192.168.2.0
 network 192.168.3.0
 no auto-summary

ip cef
line con 0
 logging synchronous

### Router 1
version 15.1
service timestamps debug datetime msec
service timestamps log datetime msec
hostname Router1

interface GigabitEthernet0/0
 ip address 10.0.0.2 255.0.0.0

interface GigabitEthernet0/1.4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0

router rip
 version 2
 network 10.0.0.0
 network 192.168.4.0
 no auto-summary

ip cef
line con 0
 logging synchronous

### Switch 0
hostname Switch0

interface FastEthernet0/1
 switchport access vlan 2
 switchport mode access

interface FastEthernet0/2
 switchport trunk allowed vlan 2-3
 switchport mode trunk

interface FastEthernet0/3
 switchport access vlan 3
 switchport mode access

spanning-tree mode pvst
line con 0
 logging synchronous

### Switch 1
hostname Switch1

interface FastEthernet0/1
 switchport trunk allowed vlan 2-4
 switchport mode trunk

interface FastEthernet0/2
 switchport access vlan 4
 switchport mode access

spanning-tree mode pvst
line con 0
 logging synchronous

### DHCP Configuration
ip dhcp pool VLAN2
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1

ip dhcp pool VLAN3
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1

### HTTP and FTP Configuration
# HTTP and FTP services should be enabled and running on a dedicated server.
service http
service ftp

### RIP Configuration (General)
router rip
 version 2
 network 10.0.0.0
 network 192.168.2.0
 network 192.168.3.0
 network 192.168.4.0
 no auto-summary

### Spanning Tree Protocol
spanning-tree mode pvst

## Схема сети
![[Pic1_Lab5_Usergate_part_1.png]]
## Цель
В рамках работы был развернут стенд в VMware Workstation на базе UserGate NGFW.  
Стенд включает головной офис (NGFW-A), филиал (NGFW-B), DMZ сегмент и удалённого пользователя.  
## Описание  
Для обеспечения безопасного взаимодействия были настроены:  
- сетевые интерфейсы и зоны безопасности;  
- правила межсетевого экрана;  
- VPN Site-to-Site между филиалами;  
- VPN Remote Access для удалённого подключения пользователей.
## Конфигурация NGFW-A

#### Интерфейсы
На NGFW-A были настроены интерфейсы для управления, локальной сети, внешней сети и DMZ сегмента.

| Имя     | Зона        | IP адрес          |
| ------- | ----------- | ----------------- |
| port0   | Management  | 192.168.56.101/24 |
| port1   | Trusted     | 192.168.100.1/24  |
| port2   | Untrusted   | 192.168.1.100/24  |
| port3   | DMZ         | 192.168.200.1/24  |
| tunnel1 | VPN for RA  | 172.30.250.1/24   |
| tunnel2 | VPN for S2S | 172.30.255.1/24   |
#### Правила межсетевого экрана
Для ограничения и контроля сетевого взаимодействия были настроены правила межсетевого экрана.  Разрешён доступ из Trusted зоны в интернет и DMZ, а также ограничен доступ из DMZ во внутреннюю сеть.

| Название                                   | Действие  | Зона ист.             | Адрес ист.                 | Зона назн.        | Адрес назн.               |
| ------------------------------------------ | --------- | --------------------- | -------------------------- | ----------------- | ------------------------- |
| Block to botnets                           | Запретить | Trusted               | Любой                      | Untrusted         | [Список<br>бот-<br>сетей] |
| Block from<br>botnets                      | Запретить | Untrusted             | [Списо<br>к бот-<br>сетей] | Любая             | Любой                     |
| Blocked<br>domains                         | Запретить | Trusted               | Любой                      | Untrusted         | [blocked]                 |
| Allow trusted to<br>untrusted              | Разрешить | Trusted               | Любой                      | Untrusted         | Любой                     |
| VPN for S2S to<br>Trusted and<br>Untrusted | Разрешить | VPN<br>for<br>S2S     | Любой                      | Untrusted,Trusted | Любой                     |
| Trusted and<br>Untrusted to<br>VPN for S2S | Разрешить | Untrust<br>ed,Trusted | Любой                      | VPN for<br>S2S    | Любой                     |
| VPN for RA to<br>Trusted and<br>Untrusted  | Разрешить | VPN<br>for<br>RA      | Любой                      | Untrusted,Trusted | Любой                     |
| Allow DMZ to<br>Untrusted                  | Разрешить | DMZ                   | Любой                      | Untrusted         | Любой                     |
| Allow from Trusted to DMZ                  | Разрешить | Trusted               | Любой                      | DMZ               | Любой                     |
| Default Block                              | Запретить | Любая                 | Любой                      | Любая             | Любой                     |

#### Настройки VPN
**Серверные правила**
![[Pic2_Lab5_Usergate_part_1.png]]
**Cети VPN**
![[Pic3_Lab5_Usergate_part_1.png]]

**Профили VPN**
**RA VPN Profile:**
![[Pic4_Lab5_Usergate_part_1.png]]
![[Pic5_Lab5_Usergate_part_1.png]]
![[Pic6_Lab5_Usergate_part_1.png]]

**S2S VPN Profile:**

![[Pic7_Lab5_Usergate_part_1.png]]
![[Pic8_Lab5_Usergate_part_1.png]]
![[Pic9_Lab5_Usergate_part_1.png]]
## Конфигурация NGFW-B

#### Интерфейсы
На NGFW-B были настроены интерфейсы для управления, локальной сети, внешней сети.

| Имя     | Зона        | IP адрес          |
| ------- | ----------- | ----------------- |
| port0   | Management  | 192.168.56.103/24 |
| port1   | Trusted     | 192.168.110.1/24  |
| port2   | Untrusted   | 192.168.1.102/24  |
| tunnel3 | VPN for S2S | 172.30.255.2/24   |

#### Правила межсетевого экрана
Для ограничения и контроля сетевого взаимодействия были настроены правила межсетевого экрана.

| Название                                   | Действие  | Зона ист.                 | Адрес ист. | Зона назн.                | Адрес назн. |
| ------------------------------------------ | --------- | ------------------------- | ---------- | ------------------------- | ----------- |
| Allow trusted tountrusted              | Разрешить | Trusted                   | Любой      | Untrusted             | Любой       |
| VPN for S2S to Trusted and Untrusted | Разрешить | VPN<br>for<br>S2S         | Любой      | Untrusted,Truste<br>d | Любой       |
| Trusted and Untrusted to VPN for S2S | Разрешить | Untrust<br>ed,Trus<br>ted | Любой      | VPN forS2S            | Любой       |
| VPN for RA to<br>Trusted and Untrusted  | Разрешить | VPN<br>for<br>RA          | Любой      | Untrusted,Trusted | Любой       |
| Default Block                              | Запретить | Любая                     | Любой      | Любая                     | Любой       |

#### Настройки VPN
Проверка установленного Site-to-Site VPN туннеля
![[Pic10_Lab5_Usergate_part_1.png]]
**Профили VPN**
**S2S VPN Profile:**
![[Pic11_Lab5_Usergate_part_1.png]]
![[Pic12_Lab5_Usergate_part_1.png]]
![[Pic13_Lab5_Usergate_part_1.png]]
## Вывод
В ходе работы был развернут учебный стенд на базе UserGate NGFW.
Были настроены сетевые интерфейсы, зоны безопасности, правила межсетевого экрана, а также VPN Site-to-Site и Remote Access VPN.
Настроенная конфигурация обеспечила защищённое взаимодействие между сегментами сети и удалённый доступ пользователей.


# demo2024
# №1
Сначала я рассчитываю  IP-адреса
# Таблица
|Имя устройства  |Интерфейс           |IPv4            |Маска/Префикс   |Шлюз            |
|  ------------- | -------------      | -------------  |  ------------- |  -------------       |
|ISP             |ens192              |10.12.29.10     |/24             |10.12.29.10         |
|                |ens224              |192.168.0.162   |/30             |                      |
|                |ens225              |192.168.0.163  |/30             |                      |
|HQ-R            |ens192              |192.168.0.161   |/30             |192.168.0.162       |
|                |ens224              |192.168.0.1     |/25             |                      |
|BR-R            |ens192              |192.168.0.164   |/30             |192.168.0.163       |
|                |ens224              |192.168.0.129  |/27             |                      |
|HQ-SRV          |ens192              |192.168.0.2   |/25             |192.168.0.1           |
|BR-SRV          |ens192              |192.168.0.156   |/27             |192.168.0.129       |

# Топология
![2023-10-25_13-57-07](https://github.com/IvanTumanov123/demo2024/assets/148867523/39edd56f-da00-4eac-a9a7-040a92ba4080)

Далее я захожу в файл конфигурации 
```
nano /etc/network/interfaces
```
И ввожу следующее:
```
auto ens192
iface ens192 inet static
address 10.12.29.10
netmask 255.255.255.0
gateway 10.12.29.254

auto ens224
iface ens224 inet static
address 192.168.0.160
netmask 255.255.255.252

auto ens225
iface ens225 inet static
address 192.168.0.164
netmask 255.255.255.252
```
**Делаю всё так же и с другими виртуальными машинами**

# №1.2
Сначала я устанавливаю пакеты FRR на виртуальную машину ISP
```
apt-get install frr
```
Проверил его состояние
```
systemctl status frr
```
После я захожу в
```
nano /etc/frr/daemons
```
И меняю значение
```
ospfd=yes
```
После чего я перезагружаю FRR
```
systemctl restart frr
```
Далее я ввожу
```
vtysh
```
И ввожу данные команды
```
conf t
router ospf
 ospf router-id 192.168.1.1
 network 192.168.1.160/30 area 0
 network 192.168.2.164/30 area 0
```
После чего смотрю соседей
```
show ip ospf neighbor
```
**Такие же действия я повторяю с HQ-R и BR-R**

# №1.4
# Таблица
|Учётная запись  |Пароль           |Примечания         |
|  ------------- | -------------      | -------------  |  
|Admin           |P@ssw0rd            |ISP HQ-SRV HQ-R |
|Branch admin    |P@ssw0rd            |BR-SRV BR-R     |
|Network admin   |P@ssw0rd            |HQ-R BR-R BR-SRV|

Я захожу на ISP и добавляю пользователя
```
useradd Admin
```
Далее я задаю пароль
```
passwd Admin
Новый пароль: P@ssw0rd
Повторите ввод нового пароля: P@ssw0rd
passwd: пароль успешно обновлён
```
![image](https://github.com/IvanTumanov123/demo2024/assets/148867523/c3f8f59f-e638-4c5a-8e82-c627581ea987)

**Такие же действия я повторил и с другими виртуальными машинами.**

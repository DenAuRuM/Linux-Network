# Linux Network

## Part 1. Инструмент ipcalc
___

- Установим ipcalc при помощи команды:

``` shell
sudo apt install ipcalc
```

### 1.1. Сети и подмаски

1. Адрес сети 192.167.38.54/13

``` shell
ipcalc 192.167.38.54/13
```

![ipcalc 192.167.38.54/13](screenshots/1.1%20-%20ipcalc.png)

2. Перевод маски

- Перевести маску 255.255.255.0 в префиксную и двоичную записи:

``` shell
ipcalc 255.255.255.0
```

![ipcalc of the mask](screenshots/1.2%20-%20ipcalc%20mask.png)

- /15 в обычную и двоичную

``` shell
ipcalc 0.0.0.0/15
```
![ipcalc /15 to back](screenshots/1.2%20-%20ipcalc%20mask%20only.png)


- 11111111.11111111.11111111.11110000 в обычную и префиксную

``` shell
ipcalc 0.0.0.0/28
```

![ipcalc mask to back n prefix](screenshots/1.2%20-%20ipcalc%20mask%20to%20back.png)

3. Минимальный и максимальный хост в сети 12.167.38.4 при масках:

- /8:

``` shell
ipcalc 12.167.38.4/8
```

![hostmin n max](screenshots/1.3%20-%20hostmin%20n%20max%20with%20mask%208.png)

- 11111111.11111111.00000000.00000000:

``` shell
ipcalc 12.167.38.4/16
```

![hostmin n max with mask 16](screenshots/1.3%20-%20mask%2016.png)

- 255.255.254.0:

``` shell
ipcalc 12.167.38.4/23
```

![hostmin n hostmax with mask 23](screenshots/1.3%20-%20mask%2023.png)

- /4:

``` shell
ipcalc 12.167.38.4/4
```

![hostmin and hostmax with mask 4](screenshots/1.3%20-%20mask%204.png)

### 1.2. Localhost

1. Определить и записать в отчёт, можно ли обратиться к приложению, работающему на localhost, со следующими IP: 194.34.23.100, 127.0.0.2, 127.1.0.1, 128.0.0.1

- Можно:

``` shell
ipcalc 127.0.0.2
```

![127.0.0.2 allowed](screenshots/1.2.1%20-%20localhost%20allowed.png)

``` shell
ipcalc 127.1.0.1
```

![127.1.0.1 allowed](screenshots/1.2.1%20-%20localhost%20allow.png)

- Нельзя:

``` shell
ipcalc 194.34.23.100
```

![194.34.23.100 localhost not allowed](screenshots/1.2.1%20-%20localhost%20not%20allowed.png)

``` shell
ipcalc 128.0.0.1
```

![128.0.0.1 localhost not allowed](screenshots/1.2.1%20-%20locsalhost%20not%20allow.png)

### 1.3. Диапозоны и сегменты сетей

1. Какие из перечисленных IP можно использовать в качестве публичного, а какие только в качестве частных: 10.0.0.45, 134.43.0.2, 192.168.4.2, 172.20.250.4, 172.0.2.1, 192.172.0.1, 172.68.0.2, 172.16.255.255, 10.10.10.10, 192.169.168.1

Приватные IP-адреса:

``` shell
ipcalc 10.0.0.45
```
![10.0.0.45](screenshots/1.3.1%20ip1.png)

``` shell
ipcalc 192.168.4.2
```
![192.168.4.2](screenshots/1.3.1%20ip3.png)

``` shell
172.20.250.4
```
![172.20.250.4](screenshots/1.3.1%20ip4.png)

``` shell
ipcalc 172.16.255.255
```
![172.16.255.255](screenshots/1.3.1%20ip8.png)


``` shell
ipcalc 10.10.10.10
```
![10.10.10.10](screenshots/1.3.1%20ip9.png)


Публичные IP-адреса:

``` shell
ipcalc 134.43.0.2
```
![134.43.0.2 public](screenshots/1.3.1%20ip3.png)

``` shell
ipcalc 172.0.2.1
```
![172.0.2.1](screenshots/1.3.1%20ip5.png)

``` shell
ipcalc 192.172.0.1
```
![192.172.0.1](screenshots/1.3.1%20ip6.png)

``` shell
ipcalc 172.68.0.2
```
![172.68.0.2](screenshots/1.3.1%20ip7.png)

``` shell
ipcalc 192.169.168.1
```
![192.169.168.1](screenshots/1.3.1%20ip7.png)

2) какие из перечисленных IP адресов шлюза возможны у сети 10.10.0.0/18: 10.0.0.1, 10.10.0.2, 10.10.10.10, 10.10.100.1, 10.10.1.255

![10.10.10.10](screenshots/1.3.2.png)

* Возможны: 
```shell
10.10.0.2;
10.10.10.10;
10.10.1.255.
```
* Невозможны:
```shell
10.0.0.1;
10.10.100.1.
```

## Part 2. Статическая маршрутизация между двумя машинами
___

Перезапустим ВМ и применим текущие конфигурации netplan к работающим системам:

```shell
sudo netplan apply
```

> При помощи команды ```ip a``` смотрим существующие сетевые интерфейсы 

![ip a ws1](screenshots/2.0%20ws1.png)
![ip a ws2](screenshots/2.0%20ws2.png)

* Аналогично эту информацию можно посмотреть с помощью команды 
```shell
ifconfig -a
```

![ifconfig ws1](screenshots/2.1%20ifconfig%20ws1.png)
![ifconfig ws2](screenshots/2.1%20ifconfig%20ws2.png)

* Для данного процесса потребуется установка утилиты net-tools, если она ранее не была установлена:
```shell 
sudo apt install net-tools
```

> Описать сетевой интерфейс, соответствующий внутренней сети, на обеих ВМ:

1. ```lo``` или ```local loopback``` - это интерфейс, который служит для подключения по сети к этому же компу и не требует доп. настроек.

```shell
- ws1: 127.0.0.1/8
- ws2: 127.0.0.1/8
```

2. ```enpos3``` - первый сетевой адаптер, работающий в NAT-режиме. Нужен для доступа в локальную сеть.

> NAT (Network Address Translation) - это механизм в сетях TCP/IP, позволяющий преобразовывать IP-адреса транзитных пакетов.

```shell
- ws1: 10.0.2.15/24
- ws2: 10.0.2.15/24
```

- Задать следующие адреса и маски:

```shell
- ws1 - 192.168.100.10, маска /16, 
- ws2 - 172.24.116.8, маска /12
```

При помощи редактора Nano редактируем config:

```shell
sudo nano /etc/netplan/00-installer-config.yaml
```

![ws1 edited config](screenshots/2.2%20ws1.png)

![ws2 edited config](screenshots/2.2%20ws2.png)

> Перезапускаем сервис сети:

```shell
sudo netplan apply
```

![ws1 netplan](screenshots/2.2%20ws1%20netplan.png)
![ws2 netplan](screenshots/2.2%20ws2%20netplan.png)

### 2.1. Добавление статического маршрута вручную

> Добавить статический маршрут от одной машины до другой и обратно при помощи команды вида `ip r add`

Для ws1:

```shell
sudo ip r add 172.24.116.8 dev enp0s8
```

Для ws2:

```shell
sudo ip r add 192.168.100.10 dev enp0s8
```

> Не забываем добавить сеть в настройках VirtualBox'a и пингуем соединение между машинами

![2.2 VB settings](screenshots/2.2%20VirtualBox%20settings.png)

ws1:

```shell
sudo ping -с 4 172.24.116.8
```

![ws1 ping](screenshots/2.2%20ws1%20ping.png)

ws2:

```shell
sudo ping -с 4 192.168.100.10
```

![ws2 ping](screenshots/2.2%20ws2%20ping.png)

### 2.2. Добавление статического маршрута с сохранением

> Перезапускаем машины и добавляем статический маршрут от одной машины до другой с помощью /etc/netplan/00-installer-config.yaml

```shell
sudo nano /etc/netplan/00-installer-config.yaml
```

![saving netplan ws1](screenshots/2.2%20ws1%20save%20netplan.png)

![saving netplan ws2](screenshots/2.2%20ws2%20save%20netplan.png)


## Part 3. Утилита iperf3
___
### 3.1 Скорость соединения

> Перевести и записать в отчёт: 8 Mbps в MB/s, 100 MB/s в Kbps, 1 Gbps в Mbps

Проявим немного ясности:
- Mbps - мегабит в секунду;
- MB/s - мегабайт в секунду;
- Kbps - килобит в секунду;
- Gbps - гигабит в секунду;

```shell
- 8 Mbps = 1 MB/s
- 100 MB/s = 800 000 Kbps
- 1 Gbps = 1000 Mbps
```

### 3.2. Утилита iperf3

- Для начала, установим утилиту `iperf3`

```shell
sudo apt install iperf3
```

> Измерить скорость скорость соединения между ws1 и ws2

- Подключаемся к удаленной машине, которая будет использовать в качестве сервера, запускаем iperf3 в режиме "Сервер" благодаря флагу -s. По-умолчанию он будет прослушивать порт 5201

ws1:
```shell
iperf3 -s
```

![ws1 iperf3](screenshots/3.0%20iperf3%20ws1.png)

- На локалке, которую мы будем рассматривать как клиент (как раз, где происходит бенчмаркинг), запускаем iperf3 в режиме client. Используя флаг -c, указывая хост, на котором работает сервер (используя либо его IP-адрес, либо домен, либо имя хоста)


ws2:

![ws iperf3](screenshots/3.0%20iperf3%20ws2.png)

## 4. Сетевой экран
___
### 4.1 Утилита iptables

> Создать файл ```/etc/firewall.sh```, имитирующий фаерволл, на ```ws1``` и ```ws2```. Нужно добавить в файл подряд следующие правила:

1) на ```ws1``` применить стратегию когда в начале пишется запрещающее правило, а в конце пишется разрешающее правило (это касается пунктов 4 и 5)

2) на ```ws2``` применить стратегию когда в начале пишется разрешающее правило, а в конце пишется запрещающее правило (это касается пунктов 4 и 5)

3) открыть на машинах доступ для порта 22 (ssh) и порта 80 (http)

4) запретить echo reply (машина не должна "пинговаться”, т.е. должна быть блокировка на OUTPUT)

5) разрешить echo reply (машина должна "пинговаться")

```shell
sudo apt-get install iptables
sudo nano /etc/firewall.sh
```
![firewalls ws1](screenshots/4.0%20iptables%20ws1.png)

![firewalls ws2](screenshots/4.0%20iptables%20ws2.png)

>Запускаем файлы на обеих машинах командами:

```shell
sudo chmod +x /etc/firewall.sh
/etc/firewall.sh
```

![ws1 ping accept](screenshots/4.1%20ws1%20ping%20accept.png)

![ws2 ping accept](screenshots/4.1%20ws2%20ping%20reject.png)

- Правило в конце файло отменяет все предыдущие, которые ему противоречат. Однако, не перезаписывает их.

### 4.2 Утилита nmap

> Командой ping найти машину, которая не "пингуется", после чего утилитой nmap показать, что хост машины запущен

```shell
sudo apt-get install nmap
```
![nmap ws2](screenshots/4.2%20nmap%20ws2.png)

## 5. Статическая маршрутизация сети
___

> Поднять пять виртуальных машин (3 рабочие станции (ws11, ws21, ws22) и 2 роутера (r1, r2))

### 5.1 Настройка адресов машин

> Настроить конфигурации машин в etc/netplan/00-installer-config.yaml согласно сети на рисунке.

```shell
sudo nano /etc/netplan/00-installer-config.yaml
``` 
![ws11 netplan](screenshots/5.1%20ws11%20netplan.png)

![ws21 netplan](screenshots/5.1%20ws21%20netplan.png)

![ws22 netplan](screenshots/5.1%20ws22%20netplan.png)

![r1 netplan](screenshots/5.1%20r1%20netplan.png)

![r2 netplan](screenshots/5.1%20r2%20netplan.png)

> Перезапускаем все ВМ при помощи команды 
```shell
sudo netplan apply
```

> Командой ```ip -4 a``` проверяем, что адрес машины задан верно 
```shell
ip -4 a
```

![ws11 ip -4 a](screenshots/5.1%20ws11%20ip%20-4%20a.png)

![ws21 ip -4 a](screenshots/5.1%20ws21%20ip%20-4%20a.png)

![ws22 ip -4 a](screenshots/5.1%20ws22%20ip%20-4%20a.png)

![r1 ip -4 a](screenshots/5.1%20r1%20ip%20-4%20a.png)

![r2 ip -4 a](screenshots/5.1%20r2%20ip%20-4%20a.png)

> Пропинговать ws22 с ws21

![from ws22 to ws21](screenshots/5.1%20ping%20from%20ws21%20to%20ws22.png)

> Пропинговать r1 c ws11
![from ws11 to r1](screenshots/5.1%20from%20ws11%20to%20r1.png)

### 5.2 Включение переадресации IP-адресов.

Для включения переадресации IP, выполните команду на роутерах ```r1``` & ```r2```:
```shell
sudo sysctl -w net.ipv4.ip_forward=1
```

![r1 ip forward](screenshots/5.2%20r1%20ip-forward.png)

![r2 ip forward](screenshots/5.2%20r2%20ip-forward.png)

>Откройте файл /etc/sysctl.conf и добавьте в него следующую строку:
```net.ipv4.ip_forward = 1```

- В отчёт поместить скрин с содержанием изменённого файла /etc/sysctl.conf

![r1 ipv4 frwd](screenshots/5.2%20r1%20ipv4.png)

![r2 ipv4 frwd](screenshots/5.2%20r2%20ipv4.png)

### 5.3 Установка маршрута по-умолчанию

>Настроить маршрут по-умолчанию (шлюз) для рабочих станций. Для этого добавить default перед IP роутера в файле конфигураций

- В отчёт поместить скрин с содержанием файла etc/netplan/00-installer-config.yaml

```shell
sudo nano /etc/netplan/00-installer-config.yaml
```
![ws11 config](screenshots/5.3%20ws11%20config.png)
![ws21 config](screenshots/5.3%20ws21%20config.png)
![ws22 config](screenshots/5.3%20ws22%20config.png)

> Вызвать ip r и показать, что добавился маршрут в таблицу маршрутизации

- В отчёт поместить скрин с вызовом и выводом использованной команды.

![ws11 ip r](screenshots/5.3%20ws11%20ip%20r.png)
![ws21 ip r](screenshots/5.3%20ws21%20ip%20r.png)
![ws22 ip r](screenshots/5.3%20ws22%20ip%20r.png)

> Пропинговать с ws11 роутер r2 и показать на r2, что пинг доходит. Для этого юзаем команду:

```shell
sudo tcpdump -tn -i enp0s8
```

![ws11 ping](screenshots/5.3%20ws11%20ping.png)

![ws r1 tcpdump](screenshots/5.3%20r1%20tcpdump.png)

### 5.4 Добавление статических маршрутов

> Добавить в роутеры r1 и r2 статические маршруты в файле конфигураций. В отчёт поместить скрины с содержанием изменённого файла etc/netplan/00-installer-config.yaml для каждого роутера.

![r1 route](screenshots/5.4%20r1%20routes.png)

![r2 route](screenshots/5.4%20r2%20routes.png)

> Вызвать ```ip r``` и показать таблицы с маршрутами на обоих роутерах.

![r1 ip r](screenshots/5.4%20r1%20ip%20r.png)
![r2 ip r](screenshots/5.4%20r2%20ip%20r.png)

> Запустить команды на ws11:

```shell
ip r list 10.10.0.0/
ip r list 0.0.0.0/0
```

> Для адреса 10.10.0.0/18 был выбран маршрут, отличный от 0.0.0.0/0, потому что маршрутизатор выбирает маршрут с самой длинной маской, так как это более точное решение. По этому правилу маршрут по умолчанию никогда не будет выбран, если есть альтернативные решения.

### 5.5 Построение списка маршрутизаторов
>Запустить на ```r1``` команду dump'a

```shell
sudo tcpdump -tnv -i enp0s8
```

На ws11 мы пишем две команды:

```shell
sudo ping -c 4 10.20.0.10
sudo traceroute 10.20.0.10
```

Таким образом, мы просматриваем путь от ```ws11``` до ```ws22``` при помощи утилиты ```traceroute```:

![ws11 ping](screenshots/5.5%20ws11%20ping%20n%20trace.png)

![r1 tcpdump](screenshots/5.5%20r1%20tcpdump.png)

>В отчёте, опираясь на вывод, полученный из дампа на ```r1```, объяснить принцип работы построения пути при помощи ```traceroute```.

- Принцип работы traceroute: для определения промежуточных маршрутизаторов traceroute отправляет целевому узлу серию ICMP-пакетов, тем самым увеличивая значение TTL (Time To Live [шагов]) на 1. Эти "шаги" TTL указывают, сколько устройств (маршрутизаторов) может пройти сообщение.

- Первое сообщение отправляется с коротким шагом, первый муршрутизатор возвращает обратно ICMP-сообщение "time exceeded in transit", тогда утилита записывает адрес маршрутизатора и время, которое заняло получения ответа.

- Затем, ```traceroute``` отправляет еще сообщения, но уже с увеличенным шагом. Это заставляет сообщения пройти через следующие маршрутизаторы на пути. Так происходит до тех пор, пока одно из сообщений не достигнет цели. Когда цеелвой узел ответтит, traceroute заканчивает процесс и показывает путь до цели.

## 5.6. Использование протокола ICMP при маршрутизации
> Запустить на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды:

```shell
sudo tcpdump -n -i enp0s8 tcmp
```
![tcpdump enp0s8](screenshots/5.6%20tcpdump%20icmp.png)

> Пропинговать с ws11 несуществующий IP (например, 10.30.0.111) с помощью команды:

```shell
sudo ping -c 1 10.30.0.111
```

![ping uknown ip](screenshots/5.6%20ping%20unkown%20ip%20ws11.png)

## 6. Динамическая настройка IP с помощью DHCP
___

- Устанавливаем утилиту isc-dhcp-server:

>Для r2 настроить в файле /etc/dhcp/dhcpd.conf конфигурацию службы DHCP:

1) указать адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети. Пример файла для r2:
```shell
subnet 10.100.0.0 netmask 255.255.0.0 {}

subnet 10.20.0.0 netmask 255.255.255.192
{
    range 10.20.0.2 10.20.0.50;
    option routers 10.20.0.1;
    option domain-name-servers 10.20.0.1;
}
```

![6.0 dhcp config](screenshots/6.0%20r2%20dhcp%20config.png)

2) в файле ```/etc/resolv.conf``` прописать nameserver 8.8.8.8:

![6.0 resolv.conf](screenshots/6.0%20r2%20resolve.conf%208.8.8.8.png)

>Перезагрузить службу DHCP командой systemctl restart isc-dhcp-server. Машину ws21 перезагрузить при помощи reboot и через ip a показать, что она получила адрес. Также пропинговать ws22 с ws21.

- Для r2:

```shell
systemctl restart isc-dhcp-server
```

![6.0 auth](screenshots/6.0%20authentication.png)

- Для ws21:
```shell
reboot
ip a
```

![ws21 ip a](screenshots/6.0%20ipa%20of%20ws21.png)

> Пропинговать ws22 с ws21

![ping ws22 from ws21](screenshots/6.0%20ping%20ws22%20from%20ws21.png)

> Указать MAC адрес у ws11, для этого в ```etc/netplan/00-installer-config.yaml``` надо добавить строки: macaddress: 10:10:10:10:10:BA, dhcp4: true

![ws11 mac address](screenshots/6.0%20ws11%20mac%20address.png)

> Для r1 настроить аналогично r2, но сделать выдачу адресов с жесткой привязкой к MAC-адресу (ws11). Провести аналогичные тесты.

![r1 dhcpd](screenshots/6.0%20r1%20dhcpd.conf.png)

![r1 resolve.conf](screenshots/6.0%20r1%20nameserver%208.8.8.8.png)

![ws11 adapter 2](screenshots/6.0%20ws11%20adapter2.png)

![ws11 ip a](screenshots/6.0%20ws11%20ip%20a.png)

![ws11 ping](screenshots/6.0%20ws11%20ping%2010.10.0.1.png)

> Запросить с ws21 обновление ip-адреса

![ws21 dhclient](screenshots/6.0%20dhclient.png)

![ws21 ip a](screenshots/6.0%20ip%20a.png)

>В отчёте описать, какими опциями DHCP сервера пользовались в данном пункте.

Для обновления ip-адреса на ws21 использовалась команда dhclient. 

Ниже мы рассмотрим инфо про эту команду.

Протокол DHCP (Dynamic Host Configuration Protocol) помогает устройствам быстро получить доступ к сети, автоматически предоставляя им необходимые настройки. (Можно привести пример с рестораном. Когда ты пиходишь, тебе хозяин (DHCP-сервер) предлагает столик (IP-сеть), чтобы ты насладился обедом (пользовался сетью) и ты принимаешь предложение (принимаешь IP-адрес))

Утилита dhclient читает конифг для получения инструкций.Далее помогает устройствам быстро получить доступ к сети, автоматически запрашивая и применяя необходимые настройки благодаря протоколу DHCP.

## 7. NAT
___

Устаналиваем утилиту при помощи:

```shell
sudo apt install apache2
```

> В файле ```/etc/apache2/ports.conf``` на ws22 и r1 изменить строку ```Listen 80``` на ```Listen 0.0.0.0:80```, то есть сделать сервер Apache2 общедоступным

![ws22 apache listen](screenshots/7.0%20ws22%20listen.png)
![r1 apache listen](screenshots/7.0%20r1%20listen.png)

> Запустить веб-сервер Apache командой service apache2 start на ws22 и r1

![ws22 apache start](screenshots/7.0%20ws22%20apache2%20start.png)
![r1 apache start](screenshots/7.0%20r1%20apache2%20start.png)

> Добавить в фаервол, созданный по аналогии с фаерволом из Части 4, на r2 следующие правила:

1) удаление правил в таблице filter - ```iptables -F```
2) удаление правил в таблице "NAT" - ```iptables -F -t nat```
3) отбрасывать все маршрутизируемые пакеты - ```iptables --policy FORWARD DROP```

![r2](screenshots/7.0%20r2%20firewall.png)

>Запускать файл также, как в Части 4

![r2 firewall launch](screenshots/7.0%20r2%20firewall.sh%20.png)

При запуске файла с этими правилами, ws22 не должна "пинговаться" с r1:

![ws22 ping](screenshots/7.0%20ws22%20ping.png)

> Добавить в файл еще одно правило:

4) разрешить маршрутизацию всех пакетов протокола ICMP

![7.0 new rule for firewall](screenshots/7.0%20added%20firewall.png)

> Запускать файл также, как в 4 части

![run bash](screenshots/7.0%20run%20bash.png)

> При запуске файла с этими правилами, ws22 должна "пинговаться" с r1

![ping from ws22](screenshots/7.0%20ping%20from%20ws22.png)

> Добавить в файл ещё два правила:

5) включить SNAT, а именно маскирование всех локальных ip из локальной сети, находящейся за r2 (по обозначениям из Части 5 - сеть 10.20.0.0)

Совет: стоит подумать о маршрутизации внутренних пакетов, а также внешних пакетов с установленным соединением

6) включить DNAT на 8080 порт машины r2 и добавить к веб-серверу Apache, запущенному на ws22, доступ извне сети
Совет: стоит учесть, что при попытке подключения возникнет новое tcp-соединение, предназначенное ws22 и 80 порту

*Совет: стоит учесть, что при попытке подключения возникнет новое tcp-соединение, предназначенное ws22 и 80 порту*

- SNAT (Source Network Address Translation) - это метод трансляции адресов сетевого уровня, используемый в компьютерных сетях. Он позволяет изменять исходный адрес источника пакета при его передаче через сетевое устройство, такое как маршрутизатор или брандмауэр. SNAT является одной из форм Network Address Translation (NAT), которая широко применяется для решения проблем с адресацией в IP-сетях.

- DNAT (Destination NAT) — механизм, изменяющий адрес назначения пакета, а также порт назначения. Используется для перенаправления входящих пакетов с внешнего адреса/порта на приватный IP-адрес/порт внутри частной сети.

- FORWARD — для входящих пакетов перенаправленных на выход (заметьте, что перенаправляемые пакеты проходят сначала цепь PREROUTING, затем FORWARD и POSTROUTING).

- TCP-порт 8080 использует протокол управления передачей данных (TCP), который является одним из основных протоколов в сетях TCP/IP. TCP является протоколом с установлением соединения и требует квитирования для установки сквозной связи. Только после установления соединения пользовательские данные могут пересылаться в обоих направлениях.

- -i, --in-interface [!] имя интерфейса, через который должен быть получен обрабатываемый пакет (только для пакетов входящих в цепочки INPUT, FORWARD и PREROUTING ). Использование аргумента "!" перед именем интерфейса инвертирует результат теста. Если имя интерфейса оканчивается на "+", то это означает любой интерфейс, имя которого начинается с указанного имени. Если эта опция опущена, при обработке пакета интерфейс, с которого он был получен, не учитывается.

- -o, --out-interface [!] имя Имя интерфейса, через который отправляется обрабатываемый пакет (только для пакетов входящих в цепочки FORWARD, OUTPUT и POSTROUTING ). Использование аргумента "!" перед именем интерфейса инвертирует результат теста. Если имя интерфейса оканчивается на "+", то это означает любой интерфейс, имя которого начинается с указанного имени. Если эта опция опущена, при обработке пакета интерфейс, с которого он был получен, не учитывается.

![r2 updated firewall](screenshots/7.0%20updated%20firewall.png)

Запускать файл также, как в Части 4

![sudo firewall](screenshots/7.0%20r2%20sudo%20firewall.png)

>Проверить соединение по TCP для SNAT, для этого с ws22 подключиться к серверу Apache на r1 командой:

```shell
telnet 10.100.0.11 80
```

![telnet](screenshots/7.0%20telnet.png)

>Проверить соединение по TCP для DNAT, для этого с r1 подключиться к серверу Apache на ws22 командой ```telnet``` (обращаться по адресу r2 и порту 8080)

![r1 telnet 8080](screenshots/7.0%20r1%20telnet%208080.png)

## 8. Знакомство с SSH Tunnels
___

Устанавливаем при помощи 
```shell 
sudo apt-get install openssh-server
```

> Запустить на r2 фаервол с правилами из Части 7. Запустить веб-сервер Apache на ws22 только на localhost (то есть в файле ```/etc/apache2/ports.conf``` изменить строку Listen 80 на Listen localhost:80)

![ws22 firewall config](screenshots/8.0%20ws22.png)

![ws22](screenshots/8.0%20apache2.png)

> Воспользоваться Local TCP forwarding с ws21 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws21

```shell
sudo systemctl start ssh
```

![ws22 became ws21](screenshots/8.0%20ws22%20became%20ws21.png)




```shell
ssh -L 6666:localhost:80 10.20.0.20
```



> Воспользоваться Remote TCP forwarding c ws11 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws11. Для проверки, сработало ли подключение в обоих предыдущих пунктах, перейдите во второй терминал (например, клавишами Alt + F2) и выполните команду:

```shell
telnet 127.0.0.1 [локальный порт]
```

```shell
ssh -R 8080:localhost:80 10.20.0.20
```
![ws22 remote became ws11](screenshots/8.0%20ws22%20remote%20became%20ws11.png)

![tellnet](screenshots/8.0%20ws21%20telnet.png)

![WS11](screenshots/8.0%20ws11%20telnet.png)
# 1. Документация сети

Вся сеть должна быть строго документирована: от принципиальной схемы, до имени интерфейса.  
Прежде, чем приступить к настройке, я бы хотел привести список необходимых документов и действий:

* **Схемы сети L1, L2, L3 в соответствии с уровнями модели OSI \(Физический, канальный, сетевой\)**
* **План IP-адресации = IP-план**
* **Список VLAN**
* **Подписи \(description\) интерфейсов**
* Список устройств \(для каждого следует указать: модель железки, установленная версия IOS, объем RAM\NVRAM, список интерфейсов\)
* Метки на кабелях \(откуда и куда идёт\), в том числе на кабелях питания и заземления и устройствах
* Единый регламент, определяющий все вышеприведённые параметры и другие

> Жирным выделено то, за чем мы будем следить в рамках программы-симулятора. Разумеется, все изменения сети нужно вносить в документацию и конфигурацию, чтобы они были в актуальном состоянии.

Говоря о метках/наклейках на кабели, мы имеем ввиду это:  
![&#x41C;&#x435;&#x442;&#x43A;&#x438; &#x43D;&#x430; &#x43A;&#x430;&#x431;&#x435;&#x43B;&#x44F;&#x445;](http://img-fotki.yandex.ru/get/4008/ait-it.1/0_41ccf_cebcb444_L.jpg)

На этой фотографии отлично видно, что промаркирован каждый кабель, значение каждого автомата на щитке в стойке, а также каждое устройство.

![&#x41C;&#x435;&#x442;&#x43A;&#x438; &#x43D;&#x430; &#x43A;&#x430;&#x431;&#x435;&#x43B;&#x44F;&#x445;](http://img-fotki.yandex.ru/get/4111/ait-it.1/0_41ccd_70990884_L.jpg)

Подготовим нужные нам документы:

## Список VLAN

| № VLAN | VLAN name | Примечание |
| :--- | :--- | :--- |
| 1 | default | Не используется |
| 2 | Management | Для управления устройствами |
| 3 | Servers | Для серверной фермы |
| 4-100 |  | Зарезервировано |
| 101 | PTO | Для пользователей ПТО |
| 102 | FEO | Для пользователей ФЭО |
| 103 | Accounting | Для пользователей Бухгалтерии |
| 104 | Other | Для других пользователей |

Каждая группа будет выделена в отдельный влан. Таким образом мы ограничим широковещательные домены. Также введём специальный VLAN для управления устройствами.

Номера VLAN c 4 по 100 зарезервированы для будущих нужд.

## IP-план

| IP-адрес | Примечание | VLAN |
| :--- | :--- | :--- |
| **172.16.0.0/16** |  |  |
| **172.16.0.0/24** | Серверная ферма | 3 |
| 172.16.0.1 | Шлюз |  |
| 172.16.0.2 | Web |  |
| 172.16.0.3 | File |  |
| 172.16.0.4 | Mail |  |
| 172.16.0.5 — 172.16.0.254 | Зарезервировано |  |
| **172.16.1.0/24** | Управление | 2 |
| 172.16.1.1 | Шлюз |  |
| 172.16.1.2 | msk-arbat-dsw1 |  |
| 172.16.1.3 | msk-arbat-asw1 |  |
| 172.16.1.4 | msk-arbat-asw2 |  |
| 172.16.1.5 | msk-arbat-asw3 |  |
| 172.16.1.6 | msk-rubl-aswl |  |
| 172.16.1.6 — 172.16.1.254 | Зарезервировано |  |
| **172.16.2.0/24** | Сеть Point-to-Point |  |
| 172.16.2.1 | Шлюз |  |
| 172.16.2.2 — 172.16.2.254 | Зарезервировано |  |
| **172.16.3.0/24** | ПТО | 101 |
| 172.16.3.1 | Шлюз |  |
| 172.16.3.2 — 172.16.3.254 | Пул для пользователей |  |
| **172.16.4.0/24** | ФЭО | 102 |
| 172.16.4.1 | Шлюз |  |
| 172.16.4.2 — 172.16.4.254 | Пул для пользователей |  |
| **172.16.5.0/24** | Бухгалтерия | 103 |
| 172.16.5.1 | Шлюз |  |
| 172.16.5.2 — 172.16.5.254 | Пул для пользователей |  |
| **172.16.6.0/24** | Другие пользователи | 104 |
| 172.16.6.1 | Шлюз |  |
| 172.16.6.2 — 172.16.6.254 | Пул для пользователей |  |

Выделение подсетей в общем-то произвольное, соответствующее только числу узлов в этой локальной сети с учётом возможного роста. В данном примере все подсети имеют стандартную маску /24 \(/24=255.255.255.0\) — зачастую такие и используются в локальных сетях, но далеко не всегда. Советуем почитать о [классах сетей](http://ru.wikipedia.org/wiki/Классовая_адресация). В дальнейшем мы обратимся и к [бесклассовой адресации](http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) \([cisco](http://www.cisco.com/en/US/tech/tk365/technologies_tech_note09186a00800a67f5.shtml)\). Мы понимаем, что ссылки на технические статьи в википедии — это моветон, однако они дают хорошее определение, а мы попробуем в свою очередь перенести это на картину реального мира.

Под сетью Point-to-Point подразумеваем подключение одного маршрутизатора к другому в режиме точка-точка. Обычно берутся адреса с маской 30 \(возвращаясь к теме бесклассовых сетей\), то есть содержащие два адреса узла. Позже станет понятно, о чём идёт речь.


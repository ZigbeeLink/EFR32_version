# О ZigbeeLink #

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/ZigbeeLink.jpg" height="300">

ZigbeeLink - это проект LAN координатора Zigbee, основанного на стеке сетевых протоколов [EmberZNet Pro](https://www.silabs.com/developers/zigbee-emberznet) от Silicon Labs.

Координатор работает на модуле [Ebyte E104-BT11N-IPX](https://www.cdebyte.com/products/E104-BT11N-IPX), в основе которого лежит чип [EFR32MG21A020F1024IM32](https://www.silabs.com/wireless/zigbee/efr32mg21-series-2-socs/device.efr32mg21a020f1024im32?tab=specs) с 1024 kB Flash и мощностью до 20 dBm.
Это мультипротокольный чип, который позволяет работать не только с Zigbee сетью, но также может применяться и для приложений Bluetooth, Thread.
Обновление прошивки может производиться по LAN сети без программатора через бутлоадер (хост должен обладать такой функциональностью). 

Для связи координатора Zigbee c Ethernet используется модуль [EBT3001](https://www.cdebyte.com/products/EBT3001) производства Ebyte. Данный модуль позволяет с легкостью и без программирования добавить возможность "проброса" устройств с UART интерфейсом в Ethernet. EBT3001 позиционирется, как модуль для IoT решений и может работать в разных режимах (TCP Server, TCP Client, UDP Server, UDP Client), имеет возможность сетевых настроек (функции StaticIP, DHCP, DNS), работу с облачными решениями (Alibaba, Baidu), может работать с MQTT, ModBus. Более подробно с возможностями модуля можно ознакомиться на сайте производителя. 
Первоначальная настройка на необходимый режим работы модуля осуществляется либо через веб-интерфейс, либо через утилиту Ebyte config tool v5.0, которую можно скачать с сайта производителя. 

Для "проброса" Zigbee стика по LAN необходимо сконфигурировать устройство, как TCP сервер, а на "приемной" стороне настроить клиента соотвествующим образом, указав IP адрес и порт TCP сервера.
В качестве клиента может быть различное ПО, такое, как [HomeAssistant](https://www.home-assistant.io/), [OpenHAB](https://www.openhab.org), [SprutHub](https://wiki.spruthub.ru/%D0%9E%D0%B3%D0%BB%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5) и т.д..

Проект содерит всё необходимое (схему, BOM, производственные файлы, прошивки) для построения координатора ZigbeeLink и предоставлятся "как есть" для личного, не коммерческого изготовления и использования.

# Описание #

Для работы ZigbeeLink требуется внешнее питание, которое подается на Type-C разъем устройства. В качестве источника питания можно использовать любой 5В адаптер с током не менее 200мА.
<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Top1.JPG" height="300">

На корпусе устройства имеется 4 светодиода:
- красный светодиод D3 сигнализирует о наличии прошивки в чипе EFR32. Отсутствие свечения светодиода означает отсутствие прошивки, либо нахождении устройства в режиме бутлоадера;
- синий светодиод D5 загорается при подключении клиента к TCP серверу и гаснет при отключении клиента от сервера (данная индикация работает при активном соединении Ethernet). При первоначальной подаче питания на устроймтво и до подключения его к Ethernet синий светодиод D5 начинает мигать и загорается постоянно;  
- желтый и зеленый светодиоды D1, D2 - это светодиоды статуса Ethernet соединения (LINK, ACT), которые сигнализируют о наличии линка и сетевой активности.

Свет от светодиодов, расположенных на плате, передается через световоды, для которых просверлено 4 отверстия в корпусе. 

Обмен данными по UART в прошивке координатора реализован с аппаратным управлением потоком (Hardware Flow Control), в котором помимо Rx, Tx используются две дополнительные линии CTS, RTS. Данный вариант рекомендуется к применению, как более надежный способ обмена в нагруженных данными приложениях. При задании настроек обмена по UART на стороне софта необходимо не забывать указывать наличие аппаратного управления потоком.  

Устройство спроектировано под фабричный корпус китайской компании LK Shiny Enclosure (модель LK-USB29) размером 68х25х15 мм.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/LK-Enclosure.JPG" height="300"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Case1.jpg" height="300"> 

Ввиду малых габаритов корпуса идеей PoE пришлось пожертвовать. 

Модули Ebyte E104-BT11N бывают как с PCB антенной, так и с разъемом IPEX1 для подключения внешней антенны. В данном решении выбран модуль с разъемом под внешнюю антенну, а сама антенна представляет собой гибкую антенну, которая клеится внутри корпуса.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Antenna.JPG">

# Прошивка #

Программирование чипа EFR32 осуществляется программатором JLINK [(либо его клонами)](https://aliexpress.ru/wholesale?SearchText=jlink). Для программирования можно использовать среду [Simplicity Studio](https://www.silabs.com/developers/simplicity-studio), в модуль U2 необходимо залить файл бутлоадера bootloader-uart-xmodem-ZigbeeLink.hex, расположенный в папке [Firmware](https://github.com/ZigbeeLink/EFR32_version/tree/main/Firmware). Настоящая инструкция преполагает наличие у пользователей минимальных навыков работы с программматором и понятие SWD интерфейса, поэтому подробности по программированию не детализированы.
Файл прошивки NCP координатора ncp-uart-hw-ZigbeeLink-6.9.2.0.gbl представлят собой GBL файл, который прошивается через бутлоадер (GBL - аббревиатура от Gecko Bootloader). На момент написания данной инструкции была собрана прошивка ncp-uart-hw-ZigbeeLink-7.3.0.0.gbl, но ее поддержики в хабах пока нет, поэтому использовать нужно прошивку версии 6.9.2.0.

# Настройка #

При первом включении устройства в сеть Ethernet модуль EBT3001 имеет IP адрес, заданный по умолчанию - 192.168.3.7 [(см.даташит)](https://www.cdebyte.com/pdf-down.aspx?id=2279), поэтому, чтобы подключиться к устройству и выполнить необходимые настройки, первоначальное подключение к устройству должно осуществляться из соответствующей подсети.
Если для настройки используется WEB-интерфейс, то после ввода IP адреса устройства в строке браузера появится окно логина, где логин и пароль по умолчанию admin/admin

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Login.JPG">

Далее в WEB-интерфейсе достаточно задать IP адрес (либо DCHP), режим работы - TCP Server, Port, а также настройки подключения по UART.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Web.jpg">
Не забывайте нажимать кнопку submit для принятия настроек.

После минимально необходимых настроек можно подключиться к устройству по сети для загрузки прошивки. Сделать это можно, например, программой [Tera Term](http://www.teraterm.org/).
Для этого в окне нового подключения указываем IP адрес и порт нашего устройства.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/teraterm.JPG">

После подключения нажатие клавиши 3 и Enter вызовет меню бутлоадера, а нажатие кнопки 1 и Enter вызовет процесс загрузки прошивки.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/teraterm1.JPG">

Далее необходимо проследовать по пути, указанному на скриншоте для отправки файла прошивки на устройство

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/teraterm2.JPG">

Начнется процесс загрузки

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/teraterm3.JPG">

Если загрузка успешна, то появится сообщение Serial upload complete

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/teraterm4.JPG">

Для запуска прошивки достаточно нажать клавишу 2 и Enter, либо просто обесточить устройство и заново подать питание. Процесс запуска прошивки будет подтвержден зажиганием красного светодиода D3. При дальнейших включениях прошивка будет запускаться автоматически, а для того, чтобы снова войти в режим бутлоадера, нужно будет подать питание на устройство с одновременно зажатой кнопкой SB1.  

Существует множество разного софта для общения по TCP, поэтому воспользовавшись, например, утилитой Network Assistant, рекомендуемой Ebyte, мы можем подключиться к нашему устройству и "пингануть его", а если точнее, можно отправить команду сброса координатора и посмотреть ответ.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/netassisrtreset.JPG">

Это может быть полезно для проверки связи с удаленным координатором. Данный сброс не нарушает работы устройства.

Аналогичным образом можно увидеть и меню бутлоадера и другой обмен данными.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/NetworkAssistant-boot.JPG">

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/NetAssist.JPG">

На этом настройка устройства закончена, можно переходить к настройке вашего сервера умного дома.

## HomeAssistant ##

Добавление ZigbeeLink в HomeAssistant осуществляется всего несколькими шагами:

- жмем добавить интеграцию;
- в поиске можно ввести ZHA;
- на следующем шаге выбираем Zigbee Home Automation;
- последовательный порт - enter Manually;
- тип радиомодуля - EZSP;
- в настройках вбить Socket://IP:PORT скорость порта указать 115200, управление потоком Hardware Flow Control, где IP - IP адрес ZigbeeLink, PORT - порт TCP сервера.  
<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Zha.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Zha1.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Zha2.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Zha3.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Zha4.JPG" height="400">

## SprutHub ##

- переходим в настройки - контроллеры;
- создаем новый Zigbee контроллер, задаем ему название;
- в настройках созданного контроллера тип указываем SiliconLabs EZSP;
- в экспертных настройках проверяем, чтобы было аппаратное управление потоком и скорость 115200;
- в расширенных настройках указываем IP адрес ZigbeeLink и порт TCP сервера;
- сохраняемся и запускаем созданный контроллер.

<img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Sh1.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Sh2.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Sh3.JPG" height="400"> <img src="https://github.com/ZigbeeLink/EFR32_version/blob/main/Images/Sh4.JPG" height="400">

## Лицензия ##

ZigbeeLink распространяется под лицензией [GPL-3.0](https://opensource.org/license/gpl-3-0/)

Вопросы по проекту можно задать в группе в [Телеграм](https://t.me/ZigbeeLink)

## Тестовое задание на позицию "Разработчик С++"

Необходимо написать две программы под linux, клиент и сервер. Сервер принимает сообщения от клиента по протоколам UDP и TCP и отправляет их обратно клиенту, т.е. работает в режиме эха. Клиент читает сообщения произвольной длины с консоли и отправляет их на сервер. Протокол, по которому отправляет сообщения клиент, указывается при его запуске. Размер сообщения не фиксированный, но не более 64 кб. Важно, чтобы объем передаваемых по сети данных был минимально необходимым. Задание должно быть выполнено на языке С++ (уместное применение ООП/STL приветствуется), с использованием Berkeley Sockets для работы с сетью.

Применение библиотек и фреймвоков наподобие Qt или boost для работы с сетью при выполнении задания не допускается.

Простое дополнение к работе сервера (помимо эхо-функции при реализации максимально возможно использовать STL).
Во входном сообщении найти вхождения всех чисел от 0 до 9 и вывести на консоль:
1. их сумму;
2. все числа, в порядке убывания;
3. максимальное и минимальное значения.

Исходники нужно положить на github. Сборку настроить через CMake.

## Результат выполнения задания

### Библиотека "Network"

Содержит классы-обертки для удобной работы с Berkley Sockets:
* NetworkException - класс сетевых исключений;
* InternetAddress - класс для удобной работы с адресацией в IPv4 сетях;
* Socket - базовый класс для сокетов;
* TcpSocket - TCP сокет;
* TcpServerSocket - TCP сокет для "прослушивания" входящих TCP соединений;
* UdpSocket - UDP сокет.

### Сервер

При старте запускает сервер TCP и UDP в отдельных потоках. TCP-сервер обрабатывает каждое подключение от клиента в отдельном потоке, что позволяет работать с несколькими клиентами одновременно. UDP-сервер имеет однопоточную архитектуру, поскольку, в отличие от TCP - не имеет понятия сессионности, а работает с датаграммами (сообщениями).
Для синхронизации вывода сообщений от серверов используется класс `Logger`, который блокирует вывод следующего сообщения, до завершения вывода текущего.

По-умолчанию TCP-сервер слушает на `0.0.0.0:8080`, а UDP-сервер на `0.0.0.0:9090`. Но эти параметры можно изменить с помощью задания сделующих опций при старте:
```
--tcpport=8080 - порт TCP-сервера;
--tcpaddr=127.0.0.1 - адрес TCP-сервера;
--udpport=9090 - порт UDP-сервера;
--udpaddr=127.0.0.1 - адрес UDP-сервера.
```
При получении от клиента сообщения с некоторым набором чисел (учитываются как положительные, так и отрицательные числа) - сервер производит их сортировку (в порядке убывания), вычисляет минимальное и максимальное число, а также суммирует все полученные числа. После чего, в консоль выводится сообщение следующего вида:
```
Received 5 digits from 127.0.0.1:43334
Digits in descending form: 100 65 34 -8 -90 
Min: -90;  Max: 100
Digits sum: 101
```
### Клиент

При запуске клиента нужно задавать следующие параметры: 
```
--proto=tcp - тип подключения (возможные значения: tcp, udp);
--port=9090 - порт сервера;
--addr=127.0.0.1 - адрес сервера (опционально, по-умолчанию - 127.0.0.1).
```
Для выхода из приложения - необходимо набрать "quit".

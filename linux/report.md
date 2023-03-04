# Отчет

## PART 1: Установка ОС

- Скачала Virtual Box и iso **Ubuntu 20.04 Server LTS (**[https://ubuntu.com/download/server](https://ubuntu.com/download/server))
- вывод команды cat /etc/issue:

![Untitled](screenshots/Untitled.png)

## PART 2: Создание пользователя

- создаю нового пользователя

![Untitled](screenshots/Untitled 1.png)

- добавляю его в группу, которая называется adm

![флаг a нужен для того, чтобы польхователь не удалялся из групп, не перечисленных после G, G нужен, чтобы списком группы перечислять, но это в целом не так обязательно](screenshots/Untitled%202.png)

флаг a нужен для того, чтобы польхователь не удалялся из групп, не перечисленных после G, G нужен, чтобы списком группы перечислять, но это в целом не так обязательно

- выполняю команду cat /etc/passwd и чтобы не искать нового юзера грепаю его!
    
    ![Untitled](screenshots/Untitled%203.png)
    
    тут не видно, что юзер в группе adm сидит, 
    
    на всякий случай проверяю это
    
    ![Untitled](screenshots/Untitled%204.png)
    

## PART 3

- задаю название машины вида user-1, потом делаю sudo reboot, чтобы изменения вступили в силу
    
    ![Untitled](screenshots/Untitled%205.png)
    
    сразу после ребута все выглядит вот так
    
    ![Untitled](screenshots/Untitled%206.png)
    
- устанавливаю временную зону, соответствующую моему текущему местоположению. для этого сначала вывожу временную зону, которая у моего линукса
    
    ![Снимок экрана 2023-02-25 173201.png](screenshots/%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2023-02-25_173201.png)
    
    затем смотрю все зоны командой timedatectl list-timezones, чтобы найти нужную — она называется Europe/Moscow
    
    ![Untitled](screenshots/Untitled%207.png)
    
    задаю ее и проверяю успех:
    
    ![Untitled](screenshots/Untitled%208.png)
    
- вывела названия сетевых интерфейсов с помощью ip link show
    
    ![Untitled](screenshots/Untitled%209.png)
    
    еще для удобства (мб пригодится?) закачала net-tools, там лежит команда ifconfig, которая более наглядно все показывает
    
    ![Untitled](screenshots/Untitled%2010.png)
    
    enp0s3 — конфигурируемый сетевой инерфейс, в общем-то это про подключение компьютера к сети
    
    lo — loopback interface, он для коммуникации между процессами внутри одного устройства. адрес, который там указан (127.0.0.1) вне зависимости от устройства будет указывать на это же устройство
    
- получила ip адрес устройства, на котором работаю, от DHCP сервера

![Untitled](screenshots/Untitled%2011.png)

- определила и вывела на экран внешний ip-адрес шлюза (ip) и внутренний IP-адрес шлюза, он же ip-адрес по умолчанию (gw)
    
    ![Untitled](screenshots/Untitled%2012.png)
    
    - *U : This flag signifies that the route is up*
    - *G : This flag signifies that the route is to a gateway. If this flag is not present then we can say that the route is to a directly connected destination (Route is to a gateway router rather than to a directly connected network or host)*

![Untitled](screenshots/Untitled%2013.png)

- задала статичные (заданные вручную, а не полученные от DHCP сервера) настройки ip, gw, dns (использовать публичный DNS серверы, например 1.1.1.1 или 8.8.8.8)
    
    ![Untitled](screenshots/Untitled%2014.png)
    
    ![тут короче:
    dhcp4: false — отключаю динамические настройки
    addresses — ip адрес устройства — enp0s3
    /24 — маска сети 255.255.255.000
    gateway4 — ip адрес шлюза для подключения к внешней сети — тут могло бы быть 127.0.0.1 (что типа “это устройство”, lo), но в ВБ сетевые настройки заданы так, что 10.0.2.2 тоже работает как элиас 127.0.0.1
    nameservers — адреса серверов имен dns](screenshots/Untitled%2015.png)
    
    тут короче:
    dhcp4: false — отключаю динамические настройки
    addresses — ip адрес устройства — enp0s3
    /24 — маска сети 255.255.255.000
    gateway4 — ip адрес шлюза для подключения к внешней сети — тут могло бы быть 127.0.0.1 (что типа “это устройство”, lo), но в ВБ сетевые настройки заданы так, что 10.0.2.2 тоже работает как элиас 127.0.0.1
    nameservers — адреса серверов имен dns
    
    ```
    на самом деле с вб есть особые приколы по используемым элиасам, так, к
    к примеру:
    10.0.2.1    Router/gateway address
    10.0.2.2    Special alias to your host loopback interface (i.e., 127.0.0.1 on your development machine) -- именно его задаем как gateway4. мое наивное представление -- что машина хоста в данном случае выполняет роль маршрутизатора для удаленной машины
    10.0.2.3    First DNS server
    10.0.2.4 / 10.0.2.5 / 10.0.2.6  Optional second, third and fourth DNS server (if any)
    10.0.2.15   The emulated device's own network/ethernet interface
    127.0.0.1   The emulated device's own loopback interface
    ```
    
    ![Untitled](screenshots/Untitled%2016.png)
    
- перезагрузила виртуальную машину. убедилась , что статичные сетевые настройки (ip, gw, dns) соответствуют заданным в предыдущем пункте.
    
    ![Untitled](screenshots/Untitled%2017.png)
    
    ![Untitled](screenshots/Untitled%2018.png)
    
- Успешно пропинговала удаленные хосты 1.1.1.1 и [ya.ru](http://ya.ru/) и вставила в отчёт скрин с выводом команды. В выводе команды фраза "0% packet loss".
    
    ![Untitled](screenshots/Untitled%2019.png)
    

## **Part 4: Обновление ОС**

- смотрю, что можно наобновлять
    
    ![Untitled](screenshots/Untitled%2020.png)
    
    `sudo apt update`
    
    `sudo apt-get upgrade`
    
    как результат:
    
    ![Untitled](screenshots/Untitled%2021.png)
    
    и 
    
    ![Untitled](screenshots/Untitled%2022.png)
    

## ****Part 5: Использование команды sudo****

![Untitled](screenshots/Untitled%2023.png)

![Untitled](screenshots/Untitled%2024.png)

![Untitled](screenshots/Untitled%2025.png)

## **Part 6. Установка и настройка службы времени**

![Untitled](screenshots/Untitled%2026.png)

## **Part 7. Установка и использование текстовых редакторов**

![Untitled](screenshots/Untitled%2027.png)

- NANO
    - `nano test_NANO.txt`
        
        ![Untitled](screenshots/Untitled%2028.png)
        
    - записала “kathalee”, нажала ctrl+O, enter, ctrl+X
        
        ![Untitled](screenshots/Untitled%2029.png)
        
    - ctrl+\ → kathalee 21 enter → School 21 enter → Y (replace instance)
        
        ![Untitled](screenshots/Untitled%2030.png)
        
    - ctrl + X (потом N на save modified buffer) чтобы выйти без сохранения
        
        ![Untitled](screenshots/Untitled%2031.png)
        
    - поиск : ctrl + W, потом паттерн и энтер, чтобы повторять — просто ctrl+W и энтер
        
        ![Untitled](screenshots/Untitled%2032.png)
        
        замена вроде как двумя способами — ctrl + \ и ctrl+W ctrl+R (сначала ввести строку которую нужно заменить, потом строку, на которую заменяешь, потом можно уточнить, all occurences(A) или какие-то отдельные)
        
        ![Untitled](screenshots/Untitled%2033.png)
        
- VIM
    - `vim`
        
        ![Untitled](screenshots/Untitled%2034.png)
        
    - чтобы зайти в command mode, нажимаю esc
        
        ![Untitled](screenshots/Untitled%2035.png)
        
    - заменяю строку kathalee на 21 School 21 перейдя в command mode
        
        ![Untitled](screenshots/Untitled%2036.png)
        
    - выхожу без сохранения
        
        ![Untitled](screenshots/Untitled%2037.png)
        
    
    ![Untitled](screenshots/Untitled%2038.png)
    
    - перешла в insert mode (i), накатала чего-то, чтобы можно было искать, потом снова перешла в command mode, ввела /слово и энтер, чтобы перейти курсором
        
        ![Untitled](screenshots/Untitled%2039.png)
        
    - cw чтобы удалить слово и поставить курсор готовый писать. пишу, перехожу обратно в command mode
        
        ![Untitled](screenshots/Untitled%2040.png)
        
    - скрин про замену всех вхождений выше :)
- JOE
    - joe
    - ^K X , ввожу имя файла и сохраняю
        
        ![Untitled](screenshots/Untitled%2041.png)
        
    - чтобы выйти без сохранений, делаю ctrl+C (подтверждаю нажимая y)
        
        ![Untitled](screenshots/Untitled%2042.png)
        
    - ctrl K F для поиска по тексту
        
        ![Untitled](screenshots/Untitled%2043.png)
        
    - B, чтобы искал в обратную сторону (курсор стоит в конце текстового файла
        
        ![Untitled](screenshots/Untitled%2044.png)
        
    - меняем мартина на бобу — так же делаем поиск, только помимо B пишем R, чтобы заменить. после энтера вводим бобу
        
        ![Untitled](screenshots/Untitled%2045.png)
        
    - он предлагает менять первого мартина, потому что мы идем назад от начала последнего мартина
        
        ![Untitled](screenshots/Untitled%2046.png)
        
    - результат замены:
        
        ![Untitled](screenshots/Untitled%2047.png)
        

## Part 8. Установка и базовая настройка сервиса **SSHD**

- `sudo apt-get install openssh-server` — устанавливаю
    
    ![Untitled](screenshots/Untitled%2048.png)
    
- `sudo systemctl enable ssh` — автостарт при запуске
    
    ![Untitled](screenshots/Untitled%2049.png)
    
- конфигурация фаерволла ufw

![Untitled](screenshots/Untitled%2050.png)

- меняю порт 22 на 2022 в файле `sshd_config` : `sudo nano sshd_config` (он в /etc/ssh)

![Untitled](screenshots/Untitled%2051.png)

- рестарчу ssh чтобы изменения пришли в силу (`sudo service ssh restart`) и вывожу `netstat -tan`
    
    ![Untitled](screenshots/Untitled%2052.png)
    
    использованный протокол — очере — локальный адрес — удаленный адрес — внутреннее состояние протокола (тут LISTEN — ожидает входящих соединений)
    
    -t — только tcp порты
    
    -a — показать все сокеты, в тч используемые серверными процессами
    
    -n — показать адреса как числа, а не как символы (например 127.0.0.53 писался бы как localhost)
    
    0.0.0.0 — маршрут по умолчанию, на пк он показывается, если не подключены к сети tcp/ip, на серверах, обозначает все IPv4 — то есть в нетстате 0.0.0.0:* это любой IPv4 с любого порта (а ::::* — любой IPv6 с любого порта)
    
    в локальных адресах есть 2022 порт который мы ток что настроили и 53, который классический для TCP
    

## ****Part 9. Установка и использование утилит top, htop****

- top уже установлена, можно просто запускать
    
    
    | uptime | 4:01 (часы минуты) |
    | --- | --- |
    | количество авторизованных пользователей | 1 |
    | общая нагрузка системы | 1.09 от единственного ядра (!) за последнюю минуту, 1.03  за последние 5 минут, 1.01 за последние 15 |
    | число процессов | 113 всего, 2 сейчас работают |
    | загрузка cpu | 0.9 на систему, 99.1 на выполнение найсов (процессов с измененным приоритетом) — у джона найс 19 |
    | загрузка памяти | 201 мб использован |
    | pid процесса, занимающего больше всего памяти (сортировка по памяти — M) | 1273 (snapd, 1.9%) |
    | pid процесса, занимающего больше всего процессорного времени (R) | 1399 |
    
    ![Untitled](screenshots/Untitled%2053.png)
    
- HTOP
    - сортировка по PID (чтобы отсортировать зажимаю >)
        
        ![Untitled](screenshots/Untitled%2054.png)
        
    - сортировка по PERCENT_CPU
        
        ![Untitled](screenshots/Untitled%2055.png)
        
    - сортировка по PERCENT_MEM
        
        ![Untitled](screenshots/Untitled%2056.png)
        
    - сортировка по TIME
        
        ![Untitled](screenshots/Untitled%2057.png)
        
    - фильтр для sshd (\ для фильтра)
        
        ![Untitled](screenshots/Untitled%2058.png)
        
    - процесс syslog (/ чтобы поставить искать) — сначала показывает не syslog, поэтому жмем fn + F3 (обычно должно работать просто F3)
        
        ![Untitled](screenshots/Untitled%2059.png)
        
    - вывод hostname, clock, uptime — fn + F2
    
    ![Untitled](screenshots/Untitled%2060.png)
    

## ****Part 10. Использование утилиты fdisk****

- запускаем fdisk -l
    
    ![Untitled](screenshots/Untitled%2061.png)
    
    | название | VBOX HARDDISK |
    | --- | --- |
    | размер | 10.46 гб |
    | количество секторов | 21923584 |
    | размер swap | ?? |

## ****Part 11. Использование утилиты df****

- `df`
    
    
    |  |  | единицы измерения |
    | --- | --- | --- |
    | размер раздела | 8875924 | KB |
    | размер занятого пространства | 4488004 | KB |
    | размер свободного пространства | 3915448 | KB |
    | • процент использования | 54 |  |
    
    ![Untitled](screenshots/Untitled%2062.png)
    
- `df -Th`
    
    
    | размер раздела | 8.5 G |
    | --- | --- |
    | размер занятого пространства | 4.3 G |
    | размер свободного пространства | 3.8 G |
    | процент использования | 54% |
    
    ![Untitled](screenshots/Untitled%2063.png)
    
- тип файловой системы — ext4 — просто файловая система по умолчанию для убунты ([https://ru.wikipedia.org/wiki/Ext4](https://ru.wikipedia.org/wiki/Ext4))

## ****Part 12. Использование утилиты du****

- размер папок /home, /var, /var/log в байтах
    
    ![Untitled](screenshots/Untitled%2064.png)
    
- размер папок /home, /var, /var/log в человекочитаемом виде
    
    ![Untitled](screenshots/Untitled%2065.png)
    
- размер всего содержимого в /var/log
    
    `sudo du -h /var/log/* | less`
    
    ![Untitled](screenshots/Untitled%2066.png)
    
    ![Untitled](screenshots/Untitled%2067.png)
    

## ****Part 13. Установка и использование утилиты ncdu****

- `sudo apt-install ncdu`
- `ncdu`
    
    ![Untitled](screenshots/Untitled%2068.png)
    
    | /home | 440 kb |
    | --- | --- |
    | /var | 824.1 mb |
    
    ![Untitled](screenshots/Untitled%2069.png)
    
    | /var/log | 91.6 mb |
    | --- | --- |
    
    почти ничего не поменялось, кроме var — должно быть потому что мы только что установили ncdu))
    

## **Part 14. Работа с системными журналами**

- `cat /var/log/dmesg | less`
    
    ![драйвера устройств](screenshots/Untitled%2070.png)
    
    драйвера устройств
    
- `cat /var/log/syslog | less`
    
    ![глобальный системный журнал, в котором пишутся сообщения с момента запуска системы, от ядра Linux, различных служб, обнаруженных устройствах, сетевых интерфейсов и много другого](screenshots/Untitled%2071.png)
    
    глобальный системный журнал, в котором пишутся сообщения с момента запуска системы, от ядра Linux, различных служб, обнаруженных устройствах, сетевых интерфейсов и много другого
    
- `cat /var/log/auth.log`
    
    ![Untitled](screenshots/Untitled%2072.png)
    
    грепаю “login”
    
    ![Untitled](screenshots/Untitled%2073.png)
    
     — последняя аутентификация
    
    можно удобнее смотреть командой last, она берет из лога `/etc/log/wtmp` и там показано, что вход через терминал tty1
    
    ![Untitled](screenshots/Untitled%2074.png)
    
- перезапускаю SSHd  (`sudo systemctl restart ssh.service`)
    
    ![лежит в `/var/log/auth.log`](screenshots/Untitled%2075.png)
    
    лежит в `/var/log/auth.log`
    

## ****Part 15. Использование планировщика заданий CRON****

- `crontab -e` (выбираю 5ый эдитор, то есть нано)
- `минута час день месяц день_недели /путь/к/исполняемому/файлу` - > у нас будет */2 * * * * uptime
    
    ![Untitled](screenshots/Untitled%2076.png)
    
- строчки в журналах:
    
    ![Untitled](screenshots/Untitled%2077.png)
    
- текущий список (`crontab -l`)
    
    ![Untitled](screenshots/Untitled%2078.png)
    
- удаление (`crontab -r`) и текущий список:
    
    ![Untitled](screenshots/Untitled%2079.png)
1. Собрать образы nginx и php-fpm:
   
   docker build -t nginx:local .
   docker build -t php-fpm:local .
  
2. Запустить docker-compose.yml:
  
   docker-compose up -d
  
3. Проверить, что сайт открывается:

   curl localhost:8181



Для отладки скрипта использовал strace с параметром -с:

strace -c php index.php

Вывод показывает следующую статистику по вызовам при выполнении данного скрипта:

<img width="749" alt="изображение" src="https://user-images.githubusercontent.com/48113580/196752533-65cfb231-f703-4bfe-919b-9bb1c2444ed3.png">


Для сравнения я выполнил дебаг скрипт example.php, который выводит "Hello World":

<img width="524" alt="изображение" src="https://user-images.githubusercontent.com/48113580/196752626-46b7263d-012c-4ed9-b516-0d6c020dcaa0.png">

Тут видно, что в сравнении с index.php, кол-во read вызовов значительно меньше.

Предполагаю, что зависание сайта происходит из-за большого количества вызовов read.


В скрине выше по скрипту index.php в выводе есть ошибка в вызове read, решил ее продебажить при помощи команды strace -f -e read php index.php:

<img width="799" alt="изображение" src="https://user-images.githubusercontent.com/48113580/196925466-d2281c46-984f-4268-9704-ff922ba484e1.png">

Далее, чтобы получить более подробную картину , командой  strace -yy php index.php получил следующий вывод:

<img width="1149" alt="изображение" src="https://user-images.githubusercontent.com/48113580/196926330-17edee44-4d46-4a5c-941d-ddd1d39c42be.png">

Вывод: в контексте poll выше на картинке revents=POLLOUT означает, что сокет готов к записи, но не готов к чтению, возможно код не проверяет данный флаг, но все равно пытается считать. Возможно это так же влияет на время работы скрипта.  

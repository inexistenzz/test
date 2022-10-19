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

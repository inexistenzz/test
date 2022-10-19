1. Собрать образы nginx и php-fpm:
   
   cd nginx && docker build -t nginx:local .
   cd php-fpm && docker build -t php-fpm:local .
  
2. Запустить docker-compose.yml:
  
   docker-compose up -d
  
3. Проверить, что сайт открывается:

   curl localhost:8181



Для отладки скрипта использовал strace с параметром -с:

strace -c php index.php

Вывод показывает следующую статистику по вызовам при выполнении данного скрипта:

<pre>--------------------------------------
|        PHP BENCHMARK SCRIPT        |
--------------------------------------
Start : 2022-10-19 15:45:28
Server : @
PHP version : 7.4.32
Platform : Linux
--------------------------------------
test_math                 : 0.356 sec.
test_stringmanipulation   : 0.383 sec.
test_loops                : 0.308 sec.
test_ifelse               : 0.196 sec.
--------------------------------------
Total time:               : 1.243 sec.</pre>% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 25.49    0.002417          10       224           mmap
 13.36    0.001267          17        72           recvfrom
 10.30    0.000977           8       117         1 read
  9.69    0.000919          14        63         6 openat
  8.19    0.000777           7       101           rt_sigaction
  6.25    0.000593           8        66           fstat
  5.58    0.000529           8        64           close
  4.46    0.000423           6        70           mprotect
  4.46    0.000423         423         1           execve
  2.12    0.000201           9        22           futex
  1.79    0.000170           8        20           getpid

Для сравнения я выполнил дебаг скрипт example.php, который выводит "Hello World":

% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 35.20    0.002792          12       223           mmap
 13.29    0.001054          17        62         6 openat
  7.86    0.000623          10        57           read
  7.80    0.000619           9        65           fstat
  7.45    0.000591           8        69           mprotect
  6.97    0.000553           9        59           close
  5.67    0.000450         450         1           execve
  5.16    0.000409           5        80           rt_sigaction
  2.57    0.000204          18        11           munmap
  1.83    0.000145           8        18           futex
  1.54    0.000122           8        15           brk
  1.30    0.000103          11         9         1 lstat
  0.57    0.000045          22         2           getdents64
  0.45    0.000036          12         3         2 access

Тут видно, что в сравнении с index.php, кол-во read вызовов значительно меньше.

Предполагаю, что зависание сайта происходит из-за большого количества вызовов read.

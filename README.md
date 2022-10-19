**1. Собрать образы nginx и php-fpm:
  
  cd nginx && docker build -t nginx:local .
  cd php-fpm && docker build -t php-fpm:local .
  
**2. Запустить docker-compose.yml:
  
  docker-compose up -d
  
**3. Проверить, что сайт открывается:

  curl localhost:8181

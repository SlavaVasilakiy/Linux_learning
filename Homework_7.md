# Task №7

* Установить в виртуальную машину или VDS Docker, <br> 
настроить набор контейнеров через docker compose по инструкции <br>
по ссылке: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose-ru. <br>
Часть с настройкой certbot и HTTPS опустить, если у вас нет настоящего домена и белого IP.
* *Запустить два контейнера, связанные одной сетью (используя документацию). <br>
Первый контейнер БД (например, образ mariadb:10.8), второй контейнер — phpmyadmin. <br>
Получить доступ к БД в первом контейнере через второй контейнер (веб-интерфейс phpmyadmin).

# Solution №7

### Установить в виртуальную машину или VDS Docker

Для установки Docker:

```linux
sudo apt install docker-compose
```

Проверим работоспособность:

```linux
sudo docker run hello-world
```

### Настроить набор контейнеров через docker compose по инструкции по ссылке:

В примере рассматривается поднятие сайта  WordPress с помощью Docker Compose.
* Пропускаем шаги с установкой Docker, Nginx, Apache, etc...
* Во-первых, создайте директорию проекта для настройки WordPress с именем wordpress и перейдите в эту директорию:

    ```linux
    mkdir wordpress && cd wordpress
    ```
  
* Затем создайте директорию для файла конфигурации:

    ```linux
    mkdir nginx-conf
    ```
  
* Откройте файл с помощью nano или своего любимого редактора:

    ```linux
    nano nginx-conf/nginx.conf
    ```
  
* В этом файле мы добавим серверный блок с директивами для имени нашего сервера и корневой директории документов, а 
  также блок расположения для направления запросов сертификатов от клиента Certbot, <br> 
  обработки PHP и запросов статичных активов.
* Добавьте в файл следующий код. Обязательно замените example.com на ваше доменное имя:

    ```nano
    server {
        listen 80;
        listen [::]:80;

        server_name example.com www.example.com;

        index index.php index.html index.htm;

        root /var/www/html;

        location / {
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass wordpress:9000;
                fastcgi_index index.php;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
        }

        location ~ /\.ht {
                deny all;
        }

        location = /favicon.ico {
                log_not_found off; access_log off;
        }
        location = /robots.txt {
                log_not_found off; access_log off; allow all;
        }
        location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
                expires max;
                log_not_found off;
        }
    }
    ```

* В главной директории проекта ~/wordpress, откройте файл с именем .env:

```linux
nano .env
```

* Добавьте в файл следующие имена и значения переменных: <br> 
  Обязательно предоставьте здесь ваши собственные значения для каждой переменной:

```nano
MYSQL_ROOT_PASSWORD=your_root_password
MYSQL_USER=your_wordpress_database_user
MYSQL_PASSWORD=your_wordpress_database_password
```

* Поскольку ваш файл .env содержит чувствительную информацию, вы можете убедиться, <br> 
  что в файлы .gitignore и .dockerignore вашего проекта включены инструкции, <br> 
  указывающие Git и Docker, какие файлы не копировать в ваши репозитории Git и образы Docker.

* Если вы планируете использовать Git для контроля версий, инициализируйте текущую рабочую директорию <br>
  в качестве репозитория с помощью git init:

```linux
git init
```
* Затем откройте файл .gitignore:

```nano
.env
```

* Также в качестве дополнительной меры предосторожности рекомендуется <br> 
  добавить .env в файл .dockerignore, чтобы он не потерялся в ваших контейнерах, <br> 
  когда вы используете эту директорию в качестве контекста для сборки. <br>
  Откройте файл:

```linux
nano .dockerignore
```

* Ниже вы можете дополнительно добавить файлы и директории, связанные с разработкой вашего приложения:

```nano
.env
.git
docker-compose.yml
.dockerignore
```

* Ваш файл docker-compose.yml будет содержать определения службы для вашей настройки. Служба в Compose — это 
  запущенный контейнер, а определения службы содержат информацию о том, как каждый контейнер будет работать.

* Используя Compose, вы можете определить различные службы для запуска приложений с несколькими контейнерами, 
  поскольку Compose позволяет привязать эти службы к общим сетям и томам. Это будет полезно для нашей текущей 
  настройки, поскольку мы создадим различные контейнеры для нашей базы данных, приложения WordPress и веб-сервера. 
  Также мы создадим контейнер для запуска клиента Certbot, чтобы получить сертификаты для нашего веб-сервера.

* Откройте файл docker-compose.yml:

```linux
nano docker-compose.yml
```

* Добавьте следующий код:

```nano
version: '3'

services:
  db:
    image: mysql:8.0
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on:
      - db
    image: wordpress:5.1.1-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

  webserver:
    depends_on:
      - wordpress
    image: nginx:1.15.12-alpine
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
    networks:
      - app-network

    volumes:
      - wordpress:/var/www/html
    command: certonly --webroot --webroot-path=/var/www/html --email sammy@example.com --agree-tos --no-eff-email --staging -d example.com -d www.example.com

volumes:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge
```

* Создайте контейнеры с помощью команды ```sudo docker-compose up``` и флага ```-d```, которые будут запускать 
  контейнеры
  db, wordpress и webserver в фоновом режиме:

```linux
sudo docker-compose up -d
```

* Дожидаемся установки всех примочек.
* Заходим на адрес ```127.0.0.1```.
* Откроется страница админки.
* Вводим нужные данные.
* Всё готово !

#### Чтобы удалить все старания:

1) Останавливаем запущенные контейнеры:

```linux
sudo docker stop webserver db wordpress
```

2) Удаляем контейнеры:

```linux
sudo docker rm webserver db wordpress
```

3) Удаляем образы:

```linux
sudo docker rmi nginx:1.15.12-alpine wordpress:5.1.1-fpm-alpine mysql:8.0
```
4) Удаляем разделы:

```linux
sudo docker volume rm wordpress_dbdata wordpress_wordpress
```

5) Удаляем сеть:

```linux
sudo docker network rm wordpress_app-network
```

### *Запустить два контейнера, связанные одной сетью (используя документацию). <br> Первый контейнер БД (например, образ mariadb:10.8), второй контейнер — phpmyadmin. <br> Получить доступ к БД в первом контейнере через второй контейнер (веб-интерфейс phpmyadmin).

```linux
sudo docker network create -d bridge hw7-bridge-network
```

```linux
sudo docker create --name mariadb -e "MARIADB_USER=user" -e "MARIADB_PASSWORD=password" -e "MARIADB_ROOT_PASSWORD=rootp" --network hw7-bridge-network  mariadb:10.8
```

```linux
sudo docker start mariadb
```

```linux
sudo docker create --name phpmyadmin -e "PMA_ARBITRARY=1" -p 8080:80 --network hw7-bridge-network phpmyadmin
```

```linux
sudo docker start phpmyadmin
```

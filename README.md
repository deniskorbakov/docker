# Docker для лакальной разработки на laravel

<h3>Стек: </h3>
<ul dir="auto">
  <li>nginx</li>
  <li>php:8.1</li>
  <li>mysql</li>
  <li>phpmyadmin</li>
</ul>

# Добавляем файлы в проект
<br>
<pre lang="dotenv" class="notranslate"><code>git clone https://github.com/deniskorbakov/docker.git
</code></pre>

# Редактируем файлы под себя:

<h3>Изменить файл docker-compose.yml</h3>

<br>

<ul dir="auto">
  <li>изменить nginx на название своего проекта - container_name: project_nginx</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>services:
    nginx:
        image: nginx:latest
        volumes:
            - ./:/var/www
            - ./_docker/nginx/conf.d:/etc/nginx/conf.d
        ports:
            - "8876:80"
        depends_on:
            - app
        container_name: project_nginx
</code></pre>

<br>

<ul dir="auto">
  <li>изменить php на название своего проекта - container_name: project_app</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>services:
    app:
        build:
            context: .
            dockerfile: _docker/app/Dockerfile

        user: '1000:1000'

        volumes:
            - ./:/var/www
        container_name: project_app
</code></pre>

<br>

<ul dir="auto">
  <li>изменить mysql на название своего проекта - container_name: project_db</li>
  <li>изменить на своё навзание базы данных - MYSQL_DATABASE: lardocker</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>services:
   db:
        image: mysql:8.0
        restart: always
        volumes:
            - ./tmp/db:/var/lib/mysql
        environment:
            MYSQL_DATABASE: lardocker
            MYSQL_ROOT_PASSWORD: root
        ports:
            - 8101:3306
        command: mysqld --character-set-server=utf8 --collation-server=utf8_unicode_ci
        container_name: project_db
</code></pre>

# Изменяем владельца файлов


<br>

<h3>в консоль пишем команду</h3>


<pre lang="dotenv" class="notranslate"><code>id
</code></pre>

должно появиться: 

<pre lang="dotenv" class="notranslate"><code>uid=1000(deniskorbakov) gid=1000(deniskorbakov)
</code></pre>

подставляем свои значение uid:gid в docker-compose.yml вместо user: '1000:1000'

<pre lang="dotenv" class="notranslate"><code>app:
        build:
            context: .
            dockerfile: _docker/app/Dockerfile

        user: '1000:1000'

        volumes:
            - ./:/var/www
        container_name: project_app
</code></pre>

# Запускаем докер


<br>

Поскольку один из наших контейнеров (php) использует Dockerfile в качестве своего образа, и мы впервые запускаем эти контейнеры, 
первое, что нам нужно сделать, это запустить команду сборки для генерации данных изображения

<pre lang="dotenv" class="notranslate"><code>docker-compose build
</code></pre>

Уделите этому около 1-2 минут, и вы должны были увидеть успешно созданные и успешно помеченные сообщения в вашем терминале. 
Затем вы можете приступить к фактическому запуску контейнеров с помощью:

<pre lang="dotenv" class="notranslate"><code>docker-compose up -d
</code></pre>

# Настройка Laravel


<br>

Откройте .env файл вашего проекта и измените следующие строки:

<ul dir="auto">
  <li>
  DB_CONNECTION=mysql <br>
  DB_HOST=db <br>
  DB_PORT=3306 <br>
  DB_DATABASE=lardocker - если меняли то название вашей базы <br>
  DB_USERNAME=root - по умолчанию <br>
  DB_PASSWORD=root - по умолачаю <br>
  
  DB_HOST=db - Это имя происходит от службы db, 
  которую мы создали в файле docker-compose.yml, и 
  спользуется в сети Docker для ссылки на службу из других контейнеров.
  </li>
  
  <li>APP_URL=http://localhost:8876 - Добавьте номер порта, который вы указали в нашем контейнере nginx(в нашем случае 8876), чтобы он указывал на разрешимый адрес.</li>
</ul>

Заходим в наш контейнер php и пропишем там команды:

<ul dir="auto">
 <li>команда для входа в контейнер</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>docker exec -it "project_app либо что вы подставили" bash
</code></pre>

уже в самом контейнере внутри будем писать следующие команды:

<ul dir="auto">
 <li>сгенерируем ключ</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>php artisan key:generate
</code></pre>

<ul dir="auto">
 <li>очистим кэш</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>php artisan config:cache
</code></pre>

<ul dir="auto">
 <li>сделаем миграцию</li>
</ul>

<pre lang="dotenv" class="notranslate"><code>php artisan migrate
</code></pre>

# Если выше указанные этапы прошли хорошо то переходим по ссылке:


<br>

<a>http://localhost:8876<a>









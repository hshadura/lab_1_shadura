# lab_1_shadura

Лаб1.Варіант 2 (спочатку не все спрацьовувало, через корпоративну пошту, сворила новий акк, тому трошки з запізненнями)
Створила репозиторій lab_1_shadura на GitHub.

Створила папку .github/workflows і в ній файл flake8.yml, де описала workflow для автоматичної перевірки стилю Python-коду за допомогою Flake8.

Створила нову гілку (test-branch) і додала файл test.py.

Створила Pull Request для об’єднання змін з test-branch у main.

GitHub Actions автоматично запускає Flake8, перевіряючи код на стильові помилки.

Якщо помилки знайдено — у вкладці Actions показується “Failed” і звіт з деталями.


Лб2. Варіант 7 (довго не виходило, бо була не включена віртуалізація)
Створила на робочому столі папку docker_task7.

У цій папці створила файл data.txt з текстом: "Це мій перший файл у Docker контейнері!".

У тій самій папці створила файл Dockerfile з таким вмістом:
FROM alpine:latest
COPY data.txt /data.txt
CMD ["cat", "/data.txt"]

Відкрила термінал у цій папці: у рядку шляху (адресі папки) написала cmd і натиснула Enter.

У терміналі виконала команду для створення образу: docker build -t copy-file .

Після успішної збірки образу запустила контейнер командою: docker run copy-file.

Результат роботи в терміналі:
Це мій перший файл у Docker контейнері!


Лб3
Завдання 1. CMS + база даних + інтерфейс адміністрування.
Для виконання завдання була створена папка docker_lab3_task1. У ній створено файл docker-compose.yml з описом трьох сервісів — WordPress, MySQL і phpMyAdmin. Для збереження даних створено два томи: wordpress_data та mysql_data, а для зв’язку між контейнерами — мережу cms_net. Вміст файлу:

version: '3.8'
services:
wordpress:
image: wordpress:latest
ports:
- "8080:80"
environment:
WORDPRESS_DB_HOST: db
WORDPRESS_DB_USER: user
WORDPRESS_DB_PASSWORD: password
WORDPRESS_DB_NAME: wordpress
volumes:
- wordpress_data:/var/www/html
networks:
- cms_net
db:
image: mysql:5.7
environment:
MYSQL_DATABASE: wordpress
MYSQL_USER: user
MYSQL_PASSWORD: password
MYSQL_ROOT_PASSWORD: rootpassword
volumes:
- mysql_data:/var/lib/mysql
networks:
- cms_net
phpmyadmin:
image: phpmyadmin/phpmyadmin
ports:
- "8081:80"
environment:
PMA_HOST: db
PMA_USER: user
PMA_PASSWORD: password
networks:
- cms_net
volumes:
wordpress_data:
mysql_data:
networks:
cms_net:

Після цього в терміналі була виконана команда docker-compose up -d. Після завантаження образів у браузері відкрито адресу http://localhost:8080
, де запустився інтерфейс WordPress, і http://localhost:8081
, де відкрився phpMyAdmin. База даних wordpress створена автоматично, і між сервісами забезпечено повну взаємодію.

Завдання 3. 
Було створено папку docker_lab3_task3 і в ній файл docker-compose.yml із трьома сервісами — api, db і logger. Сервіси об’єднані мережею micro_net, а для бази даних створено том db_storage. Вміст файлу:

version: '3.8'
services:
api:
build: ./api
ports:
- "7000:7000"
environment:
DB_HOST: db
LOGGER_URL: http://logger:9000/log

depends_on:
- db
- logger
networks:
- micro_net
db:
image: postgres:latest
environment:
POSTGRES_USER: user
POSTGRES_PASSWORD: password
POSTGRES_DB: microservice
volumes:
- db_storage:/var/lib/postgresql/data
networks:
- micro_net
logger:
build: ./logger
ports:
- "9000:9000"
networks:
- micro_net
volumes:
db_storage:
networks:
micro_net:

У каталозі api створено Flask-сервер, який зберігає дані у базі PostgreSQL і відправляє лог у сервіс logger через HTTP-запит. У каталозі logger — Python-сервер, який приймає повідомлення і виводить їх у консоль. Після виконання команди docker-compose up -d усі сервіси запустилися, API отримував запити, записував дані у базу, а logger виводив повідомлення про виконані дії.


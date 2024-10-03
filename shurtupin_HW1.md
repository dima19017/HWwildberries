# 1. Развернуть ВМ (Linux) с PostgreSQL
Работаю на своем тестовом кластере, который создан в тестовом окружении WB cloud
# 2. Залить тайские перевозки
wget https://storage.googleapis.com/thaibus/thai_small.tar.gz && tar -xf thai_small.tar.gz && psql < thai.sql
psql -c "\l" | grep thai
 thai        | postgres   | UTF8     | C.UTF-8    | C.UTF-8    |
# 3. Посчитать количество поездок
psql -d thai
thai=# select count (*) from book.tickets;
5185505
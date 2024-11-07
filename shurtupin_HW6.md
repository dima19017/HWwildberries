# 1. Развернуть ВМ (Linux) с PostgreSQL
сделано в предыдущих ДЗ  
# 2. Залить Тайские перевозки
сделано в предыдущих ДЗ  
# 3. Проверить скорость выполнения сложного запроса (приложен в конце файла скриптов)
time psql -c "наш запрос" -d thai  
real    0m2.446s  
# 4. Навесить индексы на внешние ключ
CREATE INDEX idx_seat_fkbus ON book.seat(fkbus);  
CREATE INDEX idx_tickets_fkride ON book.tickets(fkride);  
CREATE INDEX idx_ride_fkschedule ON book.ride(fkschedule);  
CREATE INDEX idx_ride_fkbus ON book.ride(fkbus);  
CREATE INDEX idx_schedule_fkroute ON book.schedule(fkroute);  
CREATE INDEX idx_busroute_fkbusstationfrom ON book.busroute(fkbusstationfrom);  
# 5. Проверить, помогли ли индексы на внешние ключи ускориться
time psql -c "наш запрос" -d thai  
real    0m1.689s  
Скорость хоть и изменилась значительно, но есть подозрения, что просто страницы в кеш ушли. Перезагрузим инстанс:  
real    0m1.719s  
Теперь можно считать, что мы ускорились  
# 1. Сгенерировать таблицу с 1 млн JSONB документов
CREATE TABLE thai_transport (  
    id SERIAL PRIMARY KEY,  
    data JSONB  
);  
INSERT INTO thai_transport (data)  
SELECT jsonb_build_object(  
    'ticket_id', i,  
    'destination', 'Bangkok',  
    'price', (random() * 1000)::int,  
    'status', 'in_transit'  
)  
FROM generate_series(1, 1000000) AS s(i);  
# 2. Создать индекс
CREATE INDEX idx_thai_transport_data ON thai_transport USING GIN (data);  
# 3. Обновить 1 из полей в json
UPDATE thai_transport SET data = jsonb_set(data, '{status}', '"delivered"');  
# 4. Убедиться в блоатинге TOAST
CREATE EXTENSION IF NOT EXISTS pgstattuple;  
SELECT free_percent FROM pgstattuple('thai_transport');  
50.07  
# 5. Придумать методы избавится от него и проверить на практике
VACUUM FULL thai_transport; но нужна эксклюзивная блокировка  
pg_repack -t thai_transport но это стороннее расширение  
Если мы говорим о предотвращении блоатинга в момент вставки:  
Разделение вставки на небольшие батчи с последующим вакуумированием после каждой итерации  
Уменьшить fillfactor  для таблицы  
# 6. Не забываем про блоатинг индексов*
REINDEX INDEX CONCURRENTLY idx_thai_transport_data;  
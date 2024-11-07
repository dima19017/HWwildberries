# 1. Создать таблицу с продажами.
CREATE TABLE sales (  
    id SERIAL PRIMARY KEY,  
    date DATE,  
    amount NUMERIC  
);  
INSERT INTO sales (date, amount)  
SELECT  
    date_trunc('day', (CURRENT_DATE - (i || ' days')::interval)),  
    round((random() * 1000)::numeric, 2)  
FROM generate_series(1, 365) AS s(i);  
# 2. Реализовать функцию выбор трети года (1-4 месяц - первая треть, 5-8 - вторая и тд)
а. через case (убрал $$)

CREATE OR REPLACE FUNCTION year_third(month INT)  
RETURNS INT AS  
BEGIN  
    RETURN CASE  
        WHEN month IS NULL THEN NULL  
        WHEN month BETWEEN 1 AND 4 THEN 1  
        WHEN month BETWEEN 5 AND 8 THEN 2  
        WHEN month BETWEEN 9 AND 12 THEN 3  
        ELSE NULL  
    END;  
END;  
LANGUAGE plpgsql;  

b. * (бонуса в виде зачета дз не будет) используя  
математическую операцию (лучше 2+ варианта)  
CREATE OR REPLACE FUNCTION year_third(month INT)  
RETURNS INT AS   
BEGIN  
    RETURN CASE  
        WHEN month IS NULL THEN NULL  
        WHEN month >= 1 AND month <= 12 THEN (month -   1) / 4 + 1  
        ELSE NULL  
    END;  
END;  
 LANGUAGE plpgsql;  

# 3. Проверить скорость выполнения сложного запроса (приложен в конце файла скриптов)

# с. предусмотреть NULL на входе

# 4. Вызвать эту функцию в SELECT из таблицы с продажами, убедиться, что всё отработало
 
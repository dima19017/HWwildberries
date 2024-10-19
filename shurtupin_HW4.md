1. Создать таблицу accounts(id integer, amount numeric);
CREATE TABLE accounts(id integer, amount numeric);
2. Добавить несколько записей и подключившись через 2 терминала добиться ситуации взаимоблокировки (deadlock).
INSERT INTO accounts (id, amount) VALUES 
(1, 100.50),
(2, 250.75),
(3, 300.00);
Взаимоблокировка:
терминал 1: BEGIN;
терминал 2: BEGIN;
терминал 1: UPDATE accounts SET amount = amount - 100 WHERE id = 1;
терминал 2: UPDATE accounts SET amount = amount - 100 WHERE id = 2;
терминал 1: UPDATE accounts SET amount = amount + 100 WHERE id = 2;
терминал 2: UPDATE accounts SET amount = amount + 100 WHERE id = 1;
ERROR:  deadlock detected
DETAIL:  Process 500213 waits for ShareLock on transaction 58740; blocked by process 497678.
Process 497678 waits for ShareLock on transaction 58741; blocked by process 500213.
3. Посмотреть логи и убедиться, что информация о дедлоке туда попала.
tail -f  /var/log/postgresql/postgresql.log
2024-10-19 06:47:50 GMT [500213-9] [local] postgres@wb [app: psql]LOG:  process 500213 detected deadlock while waiting for ShareLock on transaction 58740 after 1000.118 ms
2024-10-19 06:47:50 GMT [500213-10] [local] postgres@wb [app: psql]DETAIL:  Process holding the lock: 497678. Wait queue: .
2024-10-19 06:47:50 GMT [500213-11] [local] postgres@wb [app: psql]CONTEXT:  while updating tuple (0,8) in relation "accounts"
2024-10-19 06:47:50 GMT [500213-12] [local] postgres@wb [app: psql]STATEMENT:  UPDATE accounts SET amount = amount + 100 WHERE id = 1;
2024-10-19 06:47:50 GMT [500213-13] [local] postgres@wb [app: psql]ERROR:  deadlock detected
2024-10-19 06:47:50 GMT [500213-14] [local] postgres@wb [app: psql]DETAIL:  Process 500213 waits for ShareLock on transaction 58740; blocked by process 497678.
        Process 497678 waits for ShareLock on transaction 58741; blocked by process 500213.
        Process 500213: UPDATE accounts SET amount = amount + 100 WHERE id = 1;
        Process 497678: UPDATE accounts SET amount = amount + 100 WHERE id = 2;
2024-10-19 06:47:50 GMT [500213-15] [local] postgres@wb [app: psql]HINT:  See server log for query details.
2024-10-19 06:47:50 GMT [500213-16] [local] postgres@wb [app: psql]CONTEXT:  while updating tuple (0,8) in relation "accounts"
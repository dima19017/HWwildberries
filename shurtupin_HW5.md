# Развернуть асинхронную реплику (можно использовать 1 ВМ, просто рядом кластер развернуть и подключиться через localhost):
pg_createcluster 14 main2 --port 5434  
pg_createcluster 14 main3 --port 5435  
pg_ctlcluster 14 main2 start  
wget https://storage.googleapis.com/thaibus/thai_small.tar.gz && tar -xf thai_small.tar.gz && psql < thai.sql  
psql -p 5434 -c "CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD '';  
psql -p 5434 -c "SELECT pg_create_physical_replication_slot('test');"  
rm -rf /var/lib/postgresql/14/main3  
pg_basebackup -h localhost -p 5434 -U replicator -R -S test -D /var/lib/postgresql/14/main3  
Ver Cluster Port Status          Owner    Data directory               Log file   
14  main2   5434 online          postgres /var/lib/postgresql/14/main2 /var/log/postgresql/postgresql-14-main2.log       
14  main3   5435 online,recovery postgres /var/lib/postgresql/14/main3 /var/log/postgresql/postgresql-14-main3.log    
# ❖ тестируем производительность по сравнению с сингл инстансом
Синг инстанс чтение tps = 24353  
Синг инстанс запись tps = 5540  
С асинхронной репликой чтение tps = 24679  
С асинхронной репликой запись tps = 4601  
С синхронной репликой чтение tps = 22716  
С синхронной репликой запись tps = 4019  

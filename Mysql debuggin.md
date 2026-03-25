https://dev.mysql.com/doc/refman/8.4/en/innodb-information-schema-examples.html#:~:text=1:3:2-,Identifying%20a%20Blocking%20Query%20After%20the%20Issuing%20Session%20Becomes%20Idle,SELECT%20THREAD_ID%2C%20SQL_TEXT%20FROM%20performance_schema.

SHOW FULL PROCESSLIST;

SHOW STATUS LIKE 'Threads%';

SELECT * from information_schema.INNODB_TRX;


SELECT * FROM sys.innodb_lock_waits w
	



SELECT r.trx_id waiting_trx_id,
	r.trx_mysql_thread_id waiting_thread,
	b.trx_id blocking_trx_id,
	b.trx_mysql_thread_id blocking_thread
FROM performance_schema.data_lock_waits w
	JOIN information_schema.innodb_trx b ON b.trx_id = w.blocking_engine_transaction_id
	JOIN information_schema.innodb_trx r ON r.trx_id = w.requesting_engine_transaction_id;

SELECT 
    r.trx_id AS waiting_trx_id, 
    r.trx_mysql_thread_id AS waiting_thread, 
    r.trx_query AS waiting_query,
    b.trx_id AS blocking_trx_id, 
    b.trx_mysql_thread_id AS blocking_thread, 
    b.trx_query AS blocking_query
FROM performance_schema.data_lock_waits w
JOIN information_schema.innodb_trx b ON b.trx_id = w.blocking_engine_transaction_id
JOIN information_schema.innodb_trx r ON r.trx_id = w.requesting_engine_transaction_id;

SELECT 
    object_schema AS `db`, 
    object_name AS `table`, 
    lock_type, 
    lock_mode, 
    lock_status, 
    thread_id 
FROM performance_schema.data_locks;

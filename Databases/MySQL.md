
## Login:
------

```
mysql -uUSER -p -P 6446 --protocol=TCP (gonna ask to provide password)
mysql -uUSER -pPASS -P 6446 --protocol=TCP

A testar:
mysql -u user -p -P 6446 --protocol=TCP "select from TABLE where equipamentid in (select equipamentid from (SELECT * from ...)
```

## Show Databases/Tables/Columns:
--------------

```
show databases;
user databaseX;
show tables;
show tables from DATABASE X;
show columns from TABLE;
```

## Selecting Data:
--------------


* Select geral:

```
select * from TABLE;
select * from TABLE limit 10;
```

* Select de Colunas

```
select COLUMN from TABLE where
select COLUMN from TABLE limit 10;
select * from TABLE limit 10
```

* Select com Where:

```
select COLUMN from TABLE1 where COLUMN='2021-01-16' limit 10;
select COLUMN from TABLE1 where COLUMN="2020-01-16" limit 10;
select COLUMN from TABLE1 where COLUMN like "2020-01-%" limit 10;

select USERID from USERS where USERID like "100%";
select COLUMN from TABLE where COLUMN="valor_exato"; 
```

## Selecionar multiplas COLUMNS

```
select COLUMN1,COLUMN2 from TABLE limit 10;
select ACCOUNTID,ACCOUNTNUMBER from ACCOUNT limit 10
```

* Contando registros:

```
select count(*) COLUMN from TABLE;
select count(COLUMN) from TABLE;
select count(ACCOUNTID) from ACCOUNT;

select count(COLUMN) from TABLE where COLUMN_1 like "2020-01-%" and COLUMN_2='FAILED';
select count(EVENTLOGID) from EVENT_LOG where LASTUPDATED like "2021-04-%";
select count(STATUS) from EVENT_LOG where LASTUPDATED like "2020-01-%" and STATUS='FAILED';

```

## Pendentes:

```
Trucante, Inner Join, Update
Clustering (joining missing nodes on mysqlrouter)
```





Links: https://www.sqltutorial.org <p>
Links: https://www.w3schools.com/sql/

------
## Login:

* **Options:**

```
-e - Execute command
-D - DATABASE to user
-u - USER
-p - PASS
-P - PORT
-h - HOST
--protocol=

```

* **Default:**

```
mysql -uroot -pPASS -D DATABASE -h host

Setting HOST:
mysql -uroot -h HOST -p
```


* **MySQL router connection:**

```
mysql -uUSER -p -P 6446 --protocol=TCP (gonna ask to provide password)
mysql -uUSER -pPASS -P 6446 --protocol=TCP
```

* **Using a specific DATABASE:**

```
mysql -uUSER -pPASS -D DATABASE -P 6446 --protocol=TCP
```

* **Executing Commands:**

```
mysql -uUSER -p -D DATABASE -P 6446 --protocol=TCP -e "select ACCOUNTID from ACCOUNT limit 10;"
```

--------------
## Show Databases/Tables/Columns:

```
show databases;
user databaseX;
show tables;
show tables from DATABASE X;
show columns from TABLE;
```

--------------
## Selecting Data:

* **Select geral:**

```
select * from TABLE;
select * from TABLE limit 10;
```

* **Select Columns**

```
select COLUMN from TABLE limit 10;
select COLUMN from TABLE where COLUMN...;

```

* **Select with Where:**

```

select COLUMN from TABLE where COLUMN="valor_exato";
select COLUMN from TABLE1 where COLUMN='2021-01-16' limit 10;

select USERID from USERS where USERID like "100%";
select COLUMN from TABLE1 where COLUMN like "2020-01-%" limit 10;
 
```

## Selecting multiples COLUMNS

```
select COLUMN1,COLUMN2 from TABLE limit 10;
select ACCOUNTID,ACCOUNTNUMBER from ACCOUNT limit 10
```

* **Counting registers:**

```
select count(*) from TABLE;
select count(COLUMN) from TABLE;
select count(ACCOUNTID) from ACCOUNT;
```

* **Conditional counting:**

```
select count(COLUMN) from TABLE where COLUMN_1 like "2020-01-%" and COLUMN_2='FAILED';
select count(EVENTLOGID) from EVENT_LOG where LASTUPDATED like "2021-04-%";
select count(STATUS) from EVENT_LOG where LASTUPDATED like "2020-01-%" and STATUS='FAILED';

```

--------------
## Pending notes:

```
Trucante, Inner Join, Update
Clustering (joining missing nodes on mysqlrouter)
```





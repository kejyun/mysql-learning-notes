# 資料庫編碼

## 取得資料庫編碼

```sql
use <DATABASE_NAME>
SELECT @@character_set_database, @@collation_database;
```

```sql
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = '<DATABASE_NAME>';
```

```sql
SELECT * FROM INFORMATION_SCHEMA.SCHEMATA;
```

## 參考資料
* [MySQL :: MySQL 8.0 Reference Manual :: 10.3.3 Database Character Set and Collation](https://dev.mysql.com/doc/refman/8.0/en/charset-database.html)
# Invalid default value for timestamp field

在異動資料庫欄位時，發生 timestamp 欄位沒辦法將預設值設定為 '0000-00-00 00:00:00'，表示 `資料庫的模式（Server SQL Modes）` 有 `NO_ZERO_DATE`，所以才導致資料庫的時間必須依定要指定時間。


## 檢查目前所有的 SQL Mode

在資料庫中下查詢，查詢目前的所有 sql_mode

```sql
SHOW VARIABLES LIKE 'sql_mode'
-- ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

確認一下資料庫中的 sql_mode 有沒有 `NO_ZERO_IN_DATE`

## 修正 NO_ZERO_IN_DATE 問題

**1. 修改 MySQL 設定檔**

```shell
$ sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

**2. 加入 sql_mode 設定**

可以將原本的設定全部設定進去，然後把 `NO_ZERO_IN_DATE` 移除掉即可

```shell
sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

**3. 重新啟動 MySQL**

```shell
sudo service mysql restart
```

## 參考資料
* [mysql - Invalid default value for 'create_date' timestamp field - Stack Overflow](https://stackoverflow.com/questions/9192027/invalid-default-value-for-create-date-timestamp-field)
* [MySQL :: MySQL 5.7 Reference Manual :: 5.1.10 Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_no_zero_date)
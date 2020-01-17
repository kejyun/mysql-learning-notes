# MySQL 5.7+ ONLY_FULL_GROUP_BY SQL Mode 問題


## 問題狀況

在 MySQL 5.7 以上的版本執行 GROUP BY 的指令時，顯示了 `ONLY_FULL_GROUP_BY` 的問題，無法完成執行此語法

```sql
ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP BY clause
and contains nonaggregated column 'test.web_log.user_id' which is not functionally
dependent on columns in GROUP BY clause; this is incompatible
with sql_mode=only_full_group_by
```

但原本的 GROUP BY 語法在 5.6 版本以下是可以執行的，以舊有版本舉例來說，我們建立一個新的 `web_log` 資料表來做測試，此資料表建立了 4 個欄位分別是 `id（編號）` 、 `page_url（網址）` 、 `user_id（使用者編號）` 及 `created_at（建立時間）`，然後再這個資料表塞入假資料當作測試

```sql
mysql> create table web_log ( id int auto_increment primary key, page_url varchar(100), user_id int, created_at timestamp);
Query OK, 0 rows affected (0.03 sec)

mysql> insert into web_log(page_url , user_id, created_at) values('/index.html',1,'2019-04-17 12:21:32'),
    -> ('/index.html',2,'2019-04-17 12:21:35'),('/news.php',1,'2019-04-17 12:22:11'),('/store_offers.php',3,'2019-04-17 12:22:41'),
    -> ('/store_offers.php',2,'2019-04-17 12:23:04'),('/faq.html',1,'2019-04-17 12:23:22'),('/index.html',3,'2019-04-17 12:32:25'),
    -> ('/news.php',2,'2019-04-17 12:32:38');
Query OK, 7 rows affected (0.01 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> select * from web_log;
+----+--------------------+---------+---------------------+
| id | page_url           | user_id | created_at          |
+----+--------------------+---------+---------------------+
|  1 | /index.html        |       1 | 2019-04-17 12:21:32 |
|  2 | /index.html        |       2 | 2019-04-17 12:21:35 |
|  3 | /news.php          |       1 | 2019-04-17 12:22:11 |
|  4 | /store_offers.php  |       3 | 2019-04-17 12:22:41 |
|  5 | /store_offers.html |       2 | 2019-04-17 12:23:04 |
|  6 | /faq.html          |       1 | 2019-04-17 12:23:22 |
|  7 | /index.html        |       3 | 2019-04-17 12:32:25 |
|  8 | /news.php          |       2 | 2019-04-17 12:32:38 |
+----+--------------------+---------+---------------------+
```

將目前連線資料庫 session 的 `模式（Mode）` 設定值清空，使用 GROUP BY 查詢 `各個頁面網址（page_url）` 的瀏覽次數是多少次


```sql
# lets turn the sql mode to "forgiving"
mysql> set session sql_mode='';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT page_url, user_id, COUNT(*) AS visits
    -> FROM web_log
    -> GROUP BY page_url ORDER BY COUNT(*) DESC;
+-------------------+---------+--------+
| page_url          | user_id | visits |
+-------------------+---------+--------+
| /index.html       |       1 |      3 |
| /news.php         |       1 |      2 |
| /store_offers.php |       3 |      2 |
| /faq.html         |       1 |      1 |
+-------------------+---------+--------+
4 rows in set (0.00 sec)
```

這樣執行 GROUP BY 可以正常的撈取到資料，但其實這個語法對於 SQL 在處理資料來說是不太正確的，在這個查詢中可以看到將 `page_url` GROUP BY，所以我們可以撈到唯一值的 `page_url` 資料，但語法中包含 `user_id`，這個語法的 `user_id` 對於資料庫來說，會不曉得要抓哪一筆資料，因為它可以是 1、2 或 3 的值

所以當 SQL Mode 設定為 `ONLY_FULL_GROUP_BY` 的時候，會跑出像下方這樣的錯誤訊息

```sql
mysql> SET SESSION sql_mode='ONLY_FULL_GROUP_BY';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT page_url, user_id, COUNT(*) AS visits
    -> FROM web_log
    -> GROUP BY page_url ORDER BY COUNT(*) DESC;
ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP BY clause
and contains nonaggregated column 'test.web_log.user_id' which is not functionally
dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

## 解決辦法

### 解法 1. 重新撰寫 Query

把一些不確定性因素的欄位移除 GROUP BY 的語法，像下方把 `user_id` 移出這段語法，就可以正常撈取資料了

```sql
mysql> SET SESSION sql_mode='ONLY_FULL_GROUP_BY';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT page_url, COUNT(*) AS visits
    -> FROM web_log
    -> GROUP BY page_url ORDER BY COUNT(*) DESC;
+-------------------+--------+
| page_url          | visits |
+-------------------+--------+
| /index.html       |      3 |
| /news.php         |      2 |
| /store_offers.php |      2 |
| /faq.html         |      1 |
+-------------------+--------+
```

### 解法 2. 修改 SQL Mode

不使用任何的 SQL Mode，或是將 `ONLY_FULL_GROUP_BY` 從 SQL Mode 中移出

```sql
#set the complete "forgiving" mode
mysql> SET GLOBAL sql_mode='';

# alternatively you can set sql mode to the following
mysql> SET GLOBAL sql_mode='STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION';
```

### 解法 3. 使用整合性的欄位函數

在欄位中使用 `MAX()` 、 `MIN()` 、 `GROUP_CONCAT()` 、 `ANY_VALUE()` 之類的函數，確保欄位值是計算後的唯一值

```sql
mysql> SET SESSION sql_mode='ONLY_FULL_GROUP_BY';

mysql> SELECT page_url, MAX(user_id), COUNT(*) AS visits FROM web_log GROUP BY page_url ORDER BY COUNT(*) DESC;
+-------------------+--------------+--------+
| page_url          | MAX(user_id) | visits |
+-------------------+--------------+--------+
| /index.html       |            3 |      3 |
| /news.php         |            2 |      2 |
| /store_offers.php |            3 |      2 |
| /faq.html         |            1 |      1 |
+-------------------+--------------+--------+

mysql> SELECT page_url, GROUP_CONCAT(user_id), COUNT(*) AS visits FROM web_log GROUP BY page_url ORDER BY COUNT(*) DESC;
+-------------------+-----------------------+--------+
| page_url          | GROUP_CONCAT(user_id) | visits |
+-------------------+-----------------------+--------+
| /index.html       |                 1,2,3 |      3 |
| /news.php         |                   1,2 |      2 |
| /store_offers.php |                   3,2 |      2 |
| /faq.html         |                     1 |      1 |
+-------------------+-----------------------+--------+

mysql> SELECT page_url, ANY_VALUE(user_id), COUNT(*) AS visits FROM web_log GROUP BY page_url ORDER BY COUNT(*) DESC;
+-------------------+--------------------+--------+
| page_url          | ANY_VALUE(user_id) | visits |
+-------------------+--------------------+--------+
| /index.html       |                  1 |      3 |
| /news.php         |                  1 |      2 |
| /store_offers.php |                  3 |      2 |
| /faq.html         |                  1 |      1 |
+-------------------+--------------------+--------+
```

以正規的寫法，建議用 *解法 1* 去解決 SQL 問題，這畢竟是之後的標準規定，讓自己的語法能夠與時俱進的符合標準比較好

若系統中的語法過於複雜，用 *解法 2* 可以比較快去解決這樣的問題，但建議還是盡快修正這個語法讓語法能符合標準規範

# 參考資料
* [Solve Query Failures Regarding ONLY_FULL_GROUP_BY SQL Mode - Percona Database Performance Blog](https://www.percona.com/blog/2019/05/13/solve-query-failures-regarding-only_full_group_by-sql-mode/)
* [MySQL :: MySQL 5.7 Reference Manual :: 5.1.10 Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html)
* [MySQL :: MySQL 8.0 Reference Manual :: 5.1.11 Server SQL Modes](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html)

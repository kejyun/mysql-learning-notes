# 鎖定資料 Lock

鎖定資料有 `shared lock (sharedLock)` 與 `lock for update (lockForUpdate)`，兩者都可以避面同一行資料被其他的 transaction update

## 不同的地方

## *定義*

> sharedLock locks only for write, lockForUpdate also prevents them from being selected

`shared lock (sharedLock)` 與 `lock for update (lockForUpdate)` 兩者都是鎖定類型的 transaction，但這兩個鎖定類型的 transaction 不會避免非鎖定的 transaction 去讀取到資料。

### sharedLock

其他的 Session 可以 Select，但無法 Update 資料


如果 Session_1 使用 sharedLock 去讀取資料並對資料進行修改，而 Session_2 也使用 sharedLock 去讀取資料並對資料進行修改，則 Session_2 會等到 Session_1 commit 之後，才能對資料進行 update，若 Session_1 與 Session_2 都有修改到同樣欄位資料，則會變成最後修改的 Session_2 的資料。

*使用情境*

有 `parent` 及 `child` 兩個資料表，而兩者資料是有關聯性的，當要新增資料至 `child` 資料表時，必須確保 `parent` 資料表有資料，才不會讓 `child` 找不到他的 `parent`，此時可以使用 sharedLock 去避免資料被異動（刪除、更新）

```sql
SELECT * FROM parent WHERE NAME = 'Jones' FOR SHARE;
```


### lockForUpdate

可以避免被其他鎖定型 transaction 進行 Select

當需要對同一欄位確保資料 update 是正確的，如計數器，則必須使用 lockForUpdate 來鎖定資料

當 `parent` 內有一個計數器欄位紀錄總共有多少 `child`，則為了數字正確更新，必須避免其他鎖定型的 transaction 進行資料異動

```sql
SELECT counter_field FROM child_codes FOR UPDATE;
UPDATE child_codes SET counter_field = counter_field + 1;
```


## NOWAIT & SKIP LOCKED

### NOWAIT

若有資料被鎖定，不會等待其他 transaction commit 後再去讀取資料，直接拋出錯誤

### SKIP LOCKED

若有資料被鎖定，不會等待其他 transaction commit 後再去讀取資料，直接忽略該資料


## 範例

***Session 1 鎖定資料***

```sql
--
# Session 1:

mysql> CREATE TABLE t (i INT, PRIMARY KEY (i)) ENGINE = InnoDB;

mysql> INSERT INTO t (i) VALUES(1),(2),(3);

mysql> START TRANSACTION;
-- 鎖定資料 2 的資料，避免被讀取
mysql> SELECT * FROM t WHERE i = 2 FOR UPDATE;
+---+
| i |
+---+
| 2 |
+---+
```

***Session 2 撈取資料，使用 NOWAIT***

```sql

# Session 2:

mysql> START TRANSACTION;
-- NOWAIT 遇到資料 2 被 Lock 拋出錯誤
mysql> SELECT * FROM t WHERE i = 2 FOR UPDATE NOWAIT;
ERROR 3572 (HY000): Do not wait for lock.
```

***Session 3 撈取資料，使用 SKIP LOCKED***

```sql
# Session 3:

mysql> START TRANSACTION;

-- SKIP LOCKED 遇到資料 2 被 Lock，忽略資料 2
mysql> SELECT * FROM t FOR UPDATE SKIP LOCKED;
+---+
| i |
+---+
| 1 |
| 3 |
+---+
```




## 參考資料
* [MySQL :: MySQL 8.0 Reference Manual :: 15.5.2.4 Locking Reads](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html)
* [Database: Query Builder - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/5.2/queries#pessimistic-locking)
* [transactions - MySQL InnoDB: Difference Between `FOR UPDATE` and `LOCK IN SHARE MODE` - Stack Overflow](https://stackoverflow.com/questions/32827650/mysql-innodb-difference-between-for-update-and-lock-in-share-mode)
* [MySQL InnoDB Transaction – 交易指南 (SELECT … FOR UPDATE) – YIDAS Code](https://code.yidas.com/mysql-innodb-transaction/)

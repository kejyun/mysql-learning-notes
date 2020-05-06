# 視圖檢視表 View

## 撈取目前 View 結構

執行 `SHOW CREATE VIEW` 後就可以看到你 View 的結構為何

```sql
SHOW CREATE VIEW `view_name`
```

VIEW 的結構會顯示在 `Create View` 那個欄位，會顯示這個 VIEW 是用哪個 SQL 語法撈取資料的

```
mysql> SHOW CREATE VIEW v\G
*************************** 1. row ***************************
                View: v
         Create View: CREATE ALGORITHM=UNDEFINED
                      DEFINER=`bob`@`localhost`
                      SQL SECURITY DEFINER VIEW
                      `v` AS select 1 AS `a`,2 AS `b`
character_set_client: utf8mb4
collation_connection: utf8mb4_0900_ai_ci
```


## 參考資料
* [MySQL :: MySQL 8.0 Reference Manual :: 13.7.7.13 SHOW CREATE VIEW Statement](https://dev.mysql.com/doc/refman/8.0/en/show-create-view.html)
* [SQL VIEW 檢視表 / 視圖 - SQL 語法教學 Tutorial](https://www.fooish.com/sql/view.html)

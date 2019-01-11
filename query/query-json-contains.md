# JSON_CONTAINS 搜尋 JSON 資料

## 資料表

| id  | tags |
|---|---|
| 1  | ["KJ","KeJyun"]  |
| 2  | ["Taiwan","Thailand","Japan"]  |
| 3  | ["Basketball", "Baseball"]  |


## SQL

找出標籤欄位 JSON 資料含有 `KJ` 的資料，會抓出 id 為 `1` 的資料

```sql
select * from `tags`
where
JSON_CONTAINS(tags, '["KJ"]')
```


找出標籤欄位 JSON 資料含有 `Taiwan` 及 `Thailand` 的資料，會抓出 id 為 `2` 的資料

```sql
select * from `tags`
where
JSON_CONTAINS(tags, '["Taiwan", "Thailand"]')
```

找出標籤欄位 JSON 資料含有 `KJ` 及 `Thailand` 的資料，會找不到資料，沒有一個 JSON 資料同時含有這兩筆資料

```sql
select * from `tags`
where
JSON_CONTAINS(tags, '["KJ", "Taiwan"]')
```



## 參考資料
* [MySQL :: MySQL 5.7 Reference Manual :: 12.17.3 Functions That Search JSON Values](https://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-contains)
* [Working With JSON Data In MySQL - Part 1 of 3](https://benjaminlistwon.com/blog/working-with-json-data-in-mysql-part-1-of-3/)

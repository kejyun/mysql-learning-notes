# 時間

## date

> 格式：YYYY-MM-DD

> e.g. 2016-01-01

## datetime

> 格式：YYYY-MM-DD HH:MM:SS

> e.g. 2016-01-01 23:59:59

時間範圍：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59

## timestamp

> 格式：YYYY-MM-DD HH:MM:SS

> e.g. 2016-01-01 23:59:59


時間範圍：1970-01-01 00:00:00 ~ 2038-01-09 03:14:07


## datetime 與 timestamp 差異

* timestamp 會受時區影響，當時區不一樣則顯示時間會不一樣
* datetime 保存為絕對值資料，不隨時區影響
* 儲存空間
    * timestamp: 4 bytes
    * timestamp: 8 bytes
* 時間範圍
    * timestamp: 1970-01-01 00:00:00 ~ 2038-01-09 03:14:07
    * datetime:  1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
* 索引速度
    * timestamp > datetime
    * 蠻合理的，畢竟儲存的空間差異一倍，需要比較的東西比較多

## 參考資料
* [MySQL date、datetime和timestamp类型的区别 - 知乎](https://zhuanlan.zhihu.com/p/23663741)
* [MySQL: Datetime Versus Timestamp Data Types](https://www.tech-recipes.com/rx/22599/mysql-datetime-vs-timestamp-data-type/)
* [MySQL :: MySQL 5.7 Reference Manual :: 11.7 Data Type Storage Requirements](https://dev.mysql.com/doc/refman/5.7/en/storage-requirements.html)
* [Should I use the datetime or timestamp data type in MySQL? - Stack Overflow](https://stackoverflow.com/questions/409286/should-i-use-the-datetime-or-timestamp-data-type-in-mysql/20718367#20718367)
* [MySQL date、datetime和timestamp类型的区别_数据库_yinn-CSDN博客](https://blog.csdn.net/qq_35440678/article/details/53164675)

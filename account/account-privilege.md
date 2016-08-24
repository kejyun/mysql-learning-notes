# 權限

## 資料

| 項目  | explain  | 說明 | 目標 |
|---|---|---|---|---|
| SELECT  | Select_priv | 查詢數據行 | 表 |
| INSERT  | Insert_priv | 插入數據行 | 表 |
| UPDATE  | Update_priv | 更新數據行 | 表 |
| DELETE  | Delete_priv | 刪除數據表中的數據行 | 表 |
| FILE  | File_priv | 作業系統文件 | 在伺服器上的文件存取 |

## 結構

| 項目  | explain  | 說明 | 目標 |
|---|---|---|---|---|
| CREATE  | Create_priv | 創建數據庫和數據表 | 資料庫、表或索引 |
| ALTER  | Alter_priv | 可修改表和索引的結構 | 表 |
| INDEX  | Index_priv | 建立或刪除索引 | 表 |
| DROP  | Drop_priv |  刪除數據表和數據行 | 資料庫或表 |
| CREATE TEMPORARY TABLES  | - | 創建臨時數據表 | - |
| SHOW VIEW  | - | 查看視圖權限 | - |
| CREATE ROUTINE  | - | 創建存儲過程權限 | - |
| ALTER ROUTINE  | - | 更改存儲過程權限 | - |
| EXECUTE  | - | 執行存儲過程 | - |
| CREATE VIEW  | - | 創建視圖權限 | - |
| EVENT  | - | - | - |
| TRIGGER  | - | - | - |


# 管理

| 項目  | explain  | 說明 | 目標 |
|---|---|---|---|---|
| GRANT  | Grant_priv | 可把本帳號的權限授予其它用戶 | 資料庫或表 |
| SUPER  |  | 可用kill終止線程以及進行超級用戶操作 |  |
| PROCESS  | Process_priv | 查看運行著的線程資訊 | 伺服器管理 |
| RELOAD  | Reload_priv | 重新加載權限表或刷新日誌及緩衝區 | 伺服器管理 |
| SHUTDOWN  | Shutdown_priv | 關閉數據庫伺服器 | 伺服器管理 |
| SHOW DATABASES  |  | 可運行SHOW DATABASES指令 |  |
| LOCK TABLES  |  | 鎖定指定數據表 |  |
| REFERENCES  | References_priv |  | 資料庫或表 |
| REPLICATION CLIENT  |  | 可查詢主/從伺服器主機名 |  |
| REPLICATION SLAVE  |  | 運行一個鏡像從伺服器 |  |
| CREATE USER  |  | 創建用戶權限 |  |



## 參考資料
* [MySQL中文參考手冊-6 MySQL 存取權限系統](http://twpug.net/docs/mysql323/manual_Privilege_system.html)
* [MYSQL教程:MySQL用戶帳號管理 @ Web Design :: 痞客邦 PIXNET ::](http://dettori.pixnet.net/blog/post/5258187-mysql%E6%95%99%E7%A8%8B%3Amysql%E7%94%A8%E6%88%B6%E5%B8%B3%E8%99%9F%E7%AE%A1%E7%90%86)



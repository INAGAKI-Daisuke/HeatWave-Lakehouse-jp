# Lab 07: PAR URLを使用してデータをMySQL HeatWave Lakehouseに取り込む

## Auto Loadingによる取り込み

このセクションでは、自動並列ロード プロシージャと、プロシージャの一部として呼び出される自動スキーマ アドバイザーを使用したデータのロードについて説明します。

このプロセスでは、既存の外部テーブル定義は必要なく、自動スキーマ アドバイザ分析に基づいて外部テーブルが作成されます。 


## 手順

### **Step 7.1:アクセスするPAR URL:**
- 2023/10/30 limit
- 
  https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/
  


### **Step 7.2:データベースの作成**
- データを展開するデータベース[_**autotpch100**_]を作成します

```
mysqlsh --user=admin --password=Oracle.123 --host=<mysql_private_ip_address> --port=3306 --sql
```

```
create database autotpch100;
```

 実行できたら _**Step 7.3**_ に進みます。

### **Step 7.3:データベースの設定**
- 変数にデータベースを設定します

```
SET @db_list = '["autotpch100"]';
```
### **Step 7.4:テーブル情報を設定**
- 変数にテーブル名及び参照先を設定します

```
SET @ext_tables = '[{
          "db_name": "autotpch100",
          "tables": [
            {
              "table_name": "customer",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/customer.tbl"}
              ]
            },
            {
              "table_name": "lineitem",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/lineitem.tbl"}
              ]
            },
            {
              "table_name": "orders",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/orders.tbl"}
              ]
            },
            {
              "table_name": "part",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/part.tbl"}
              ]
            },
            {
              "table_name": "partsupp",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/partsupp.tbl"}
              ]
            },
            {
              "table_name": "region",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/region.tbl"}
              ]
            },
            {
              "table_name": "supplier",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/supplier.tbl"}
              ]
            },
            {
              "table_name": "nation",
              "dialect": { "format": "csv", "field_delimiter": "|", "record_delimiter": "|\\n" },
              "file": [
                {"par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/nation.tbl"}
              ]
            }
          ]
        }]';

```

### **Step 7.5:オプションを設定**
- テーブル情報のフォーマットを設定します
```
SET @options = JSON_OBJECT('external_tables', CAST(@ext_tables AS JSON));
```

### **Step 7.6:Lakehouseへ取り込み**
- MySQL HeatWave Lakehouseへの取り込みを実施します
```
CALL sys.heatwave_load(@db_list, @options);
```
## 実行結果  
```
+------------------------------------------+
| INITIALIZING HEATWAVE AUTO PARALLEL LOAD |
+------------------------------------------+
| Version: 2.31                            |
|                                          |
| Load Mode: normal                        |
| Load Policy: disable_unsupported_columns |
| Output Mode: normal                      |
|                                          |
+------------------------------------------+
6 rows in set (0.0108 sec)

+--------------------------------------------------------------------------------------------------------------------+
| LAKEHOUSE AUTO SCHEMA INFERENCE                                                                                    |
+--------------------------------------------------------------------------------------------------------------------+
| Verifying external lakehouse tables: 8                                                                             |
|                                                                                                                    |
| SCHEMA                   TABLE                    TABLE IS           RAW     NUM. OF      ESTIMATED     SUMMARY OF |
| NAME                     NAME                     CREATED      FILE SIZE     COLUMNS      ROW COUNT     ISSUES     |
| ------                   -----                    --------     ---------     -------      ---------     ---------- |
| `autotpch100`            `customer`               NO            2.29 GiB           8        15.01 M                |
| `autotpch100`            `lineitem`               NO           74.11 GiB          16       600.69 M                |
| `autotpch100`            `nation`                 NO            2.17 KiB           4             25                |
| `autotpch100`            `orders`                 NO           16.57 GiB           9       150.08 M                |
| `autotpch100`            `part`                   NO            2.28 GiB           9        20.01 M                |
| `autotpch100`            `partsupp`               NO           11.37 GiB           5        80.03 M                |
| `autotpch100`            `region`                 NO           389 bytes           3              5                |
| `autotpch100`            `supplier`               NO          136.25 MiB           7            1 M                |
|                                                                                                                    |
| New schemas to be created: 0                                                                                       |
| External lakehouse tables to be created: 8                                                                         |
|                                                                                                                    |
+--------------------------------------------------------------------------------------------------------------------+
17 rows in set (0.0108 sec)

+------------------------------------------------------------------------+
| OFFLOAD ANALYSIS                                                       |
+------------------------------------------------------------------------+
| Verifying input schemas: 1                                             |
| User excluded items: 0                                                 |
|                                                                        |
| SCHEMA                       OFFLOADABLE    OFFLOADABLE     SUMMARY OF |
| NAME                              TABLES        COLUMNS     ISSUES     |
| ------                       -----------    -----------     ---------- |
| `autotpch100`                          8             61                |
|                                                                        |
| Total offloadable schemas: 1                                           |
|                                                                        |
+------------------------------------------------------------------------+
10 rows in set (0.0108 sec)

+-----------------------------------------------------------------------------------------------------------------------------+
| CAPACITY ESTIMATION                                                                                                         |
+-----------------------------------------------------------------------------------------------------------------------------+
| Default encoding for string columns: VARLEN (unless specified in the schema)                                                |
| Estimating memory footprint for 1 schema(s)                                                                                 |
|                                                                                                                             |
|                                TOTAL       ESTIMATED       ESTIMATED       TOTAL     DICTIONARY      VARLEN       ESTIMATED |
| SCHEMA                   OFFLOADABLE   HEATWAVE NODE      MYSQL NODE      STRING        ENCODED     ENCODED            LOAD |
| NAME                          TABLES       FOOTPRINT       FOOTPRINT     COLUMNS        COLUMNS     COLUMNS            TIME |
| ------                   -----------       ---------       ---------     -------     ----------     -------       --------- |
| `autotpch100`                      8       62.03 GiB        4.31 MiB          29              0          29       18.23 min |
|                                                                                                                             |
| Sufficient MySQL host memory available to load all tables.                                                                  |
| Sufficient HeatWave cluster memory available to load all tables.                                                            |
|                                                                                                                             |
+-----------------------------------------------------------------------------------------------------------------------------+
12 rows in set (0.0108 sec)

+---------------------------------------------------------------------------------------------------------------------------------------+
| EXECUTING LOAD                                                                                                                        |
+---------------------------------------------------------------------------------------------------------------------------------------+
| HeatWave Load script generated                                                                                                        |
|   Retrieve load script containing 16 generated DDL command(s) using the query below:                                                  |
| Deprecation Notice: "heatwave_load_report" will be deprecated, please switch to "heatwave_autopilot_report"                           |
|   SELECT log->>"$.sql" AS "Load Script" FROM sys.heatwave_autopilot_report WHERE type = "sql" ORDER BY id;                            |
|                                                                                                                                       |
| Adjusting load parallelism dynamically per internal/external table.                                                                   |
| Using current parallelism of 32 thread(s) as maximum for internal tables.                                                             |
|                                                                                                                                       |
| Using SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION |
|                                                                                                                                       |
| Proceeding to load 8 table(s) into HeatWave.                                                                                          |
|                                                                                                                                       |
| Applying changes will take approximately 18.22 min                                                                                    |
|                                                                                                                                       |
+---------------------------------------------------------------------------------------------------------------------------------------+
14 rows in set (0.0108 sec)

+------------------------------------------+
| LOADING TABLE                            |
+------------------------------------------+
| TABLE (1 of 8): `autotpch100`.`customer` |
| Commands executed successfully: 2 of 2   |
| Warnings encountered: 0                  |
| Table loaded successfully!               |
|   Total columns loaded: 8                |
|   Elapsed time: 1.09 min                 |
|                                          |
+------------------------------------------+
7 rows in set (0.0108 sec)

+------------------------------------------+
| LOADING TABLE                            |
+------------------------------------------+
| TABLE (2 of 8): `autotpch100`.`lineitem` |
| Commands executed successfully: 2 of 2   |
| Warnings encountered: 0                  |
| Table loaded successfully!               |
|   Total columns loaded: 16               |
|   Elapsed time: 5.98 min                 |
|                                          |
+------------------------------------------+
7 rows in set (0.0108 sec)

+----------------------------------------+
| LOADING TABLE                          |
+----------------------------------------+
| TABLE (3 of 8): `autotpch100`.`nation` |
| Commands executed successfully: 2 of 2 |
| Warnings encountered: 0                |
| Table loaded successfully!             |
|   Total columns loaded: 4              |
|   Elapsed time: 3.09 s                 |
|                                        |
+----------------------------------------+
7 rows in set (0.0108 sec)

+----------------------------------------+
| LOADING TABLE                          |
+----------------------------------------+
| TABLE (4 of 8): `autotpch100`.`orders` |
| Commands executed successfully: 2 of 2 |
| Warnings encountered: 0                |
| Table loaded successfully!             |
|   Total columns loaded: 9              |
|   Elapsed time: 1.57 min               |
|                                        |
+----------------------------------------+
7 rows in set (0.0108 sec)

+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| LOADING TABLE                                                                                                                                                                 |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| TABLE (5 of 8): `autotpch100`.`part`                                                                                                                                          |
| Commands executed successfully: 1 of 2                                                                                                                                        |
| Warnings encountered: 0                                                                                                                                                       |
| Table failed to load!                                                                                                                                                         |
|   Command failed: "ALTER TABLE `autotpch100`.`part` SECONDARY_LOAD;"                                                                                                          |
|     Error: Unable to load table from external source: Col 2 of row starting at {object: part.tbl in namespace: idazzjlcjqzj, bucket: tpch-100}:1137409355: Data was truncated |
|   Elapsed time: 22.61 s                                                                                                                                                       |
|                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
8 rows in set (0.0108 sec)

+------------------------------------------+
| LOADING TABLE                            |
+------------------------------------------+
| TABLE (6 of 8): `autotpch100`.`partsupp` |
| Commands executed successfully: 2 of 2   |
| Warnings encountered: 0                  |
| Table loaded successfully!               |
|   Total columns loaded: 5                |
|   Elapsed time: 1.28 min                 |
|                                          |
+------------------------------------------+
7 rows in set (0.0108 sec)

+----------------------------------------+
| LOADING TABLE                          |
+----------------------------------------+
| TABLE (7 of 8): `autotpch100`.`region` |
| Commands executed successfully: 2 of 2 |
| Warnings encountered: 0                |
| Table loaded successfully!             |
|   Total columns loaded: 3              |
|   Elapsed time: 3.50 s                 |
|                                        |
+----------------------------------------+
7 rows in set (0.0108 sec)

+------------------------------------------+
| LOADING TABLE                            |
+------------------------------------------+
| TABLE (8 of 8): `autotpch100`.`supplier` |
| Commands executed successfully: 2 of 2   |
| Warnings encountered: 0                  |
| Table loaded successfully!               |
|   Total columns loaded: 7                |
|   Elapsed time: 14.34 s                  |
|                                          |
+------------------------------------------+
7 rows in set (0.0108 sec)

+-------------------------------------------------------------------------------+
| LOAD SUMMARY                                                                  |
+-------------------------------------------------------------------------------+
|                                                                               |
| SCHEMA                          TABLES       TABLES      COLUMNS         LOAD |
| NAME                            LOADED       FAILED       LOADED     DURATION |
| ------                          ------       ------      -------     -------- |
| `autotpch100`                        7            1           52    10.65 min |
|                                                                               |
+-------------------------------------------------------------------------------+
6 rows in set (0.0108 sec)

Query OK, 0 rows affected (0.0108 sec)
```

### **Step 7.7:エラー発生時の対応**

```
alter table part MODIFY col_2 varchar(64) NOT NULL COMMENT 'RAPID_COLUMN=ENCODING=VARLEN';
```
```
ALTER TABLE `autotpch100`.`part` SECONDARY_LOAD;
```


## コマンド一覧
```
SHOW CREATE TABLE `autotpch100`.`part` \G;
```
```
SHOW CREATE TABLE `autotpch100`.`customer` \G;
```


- Auto Parallel Load が予期せず停止した場合のエラー情報を表示

```
SELECT log FROM sys.heatwave_load_report WHERE type="error";
```

- 警告を表示して、テーブルをロードできない理由を確認
```
SELECT log FROM sys.heatwave_load_report WHERE type="warn";
```
- 生成されたロード スクリプトを表示して、実行されるコマンドを確認
```
SELECT log->>"$.sql" AS "Load Script"
          FROM sys.heatwave_load_report 
          WHERE type = "sql" ORDER BY id;
```

## まとめ

今回はAuto Loadによるデータの取り込みでした。次のステップではユーザがカラムを設定する形でデータの取り込みを行います



**[<< Lab 06](/Lab06/README.md)** | **[Home](../README.md)** | **[Lab 08 >>](/Lab08/README.md)**

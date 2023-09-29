# Lab 06: オブジェクトストレージのファイルをMySQL HeatWave Lakehouseに取り込む

## 学べること
- HeatWaveに接続し、Bulket上のデータを取り込む

- リソースプリンシパルを使用する
- 事前認証リクエストURLの使用する

## 手順

### **リソースプリンシパルを使用する:**
- ソースプリンシパルは、動的グループとポリシーから成ります。下記の例のように動的グループを作ると、コンパートメント内の全てのMySQL DBシステムが動的グループに含まれるようになります

![](./images/HW35_hw.png)
![](./images/HW36_hw.png)

- ポリシーによって、オブジェクトストアから動的グループへの読み取りと書き込みのいずれか、または両方のアクセスを許可します。下記のポリシーの例では、コンパートメント内の全てのオブジェクトへの読み取りが許可されています。MySQL DBシステムは動的グループに含まれることで、アクセスできるようになります
  
![](./images/HW37_hw.png)

- 下記はリソースプリンシパルを使用した時のテーブル作成DDL文の例です。ENGINE_ATTRIBUTE内のprefixは、バケット内のファイルのパスです。例えばmyschema/mytable/は、myschema/mytable/下にあるmy-bucket内のすべてのファイルを示します。同様に、dialectはファイル形式や区切り文字などを定義します。オブジェクトストア内のデータファイルは、MySQL DBシステムと同じリージョンに配置されている必要があります
```
create database testdb;
use testdb;
CREATE TABLE MYTABLE ( M_OKEY bigint NOT NULL, M_PKEY int NOT NULL,
                                              M_NBR int NOT NULL,
                                             …..,
                                             PRIMARY KEY (M_OKEY,M_NBR))  ENGINE=lakehouse
                                             ENGINE_ATTRIBUTE='{"file": [
                                                 {"namespace": "mynamespace",
                                                   "region": "us-ashburn-1", "bucket": "my_bucket",
                                                   "prefix": "myschema/mytable"}],
                                                   "dialect": {"format": "csv", "is_strict_mode": false, "field_delimiter": "|", "record_delimiter": "|\\n"}}';
```
  

### **Step 6.2:**
- MySQL ShellプロンプトでMDSにデータが格納できるか確認します。
```
util.loadDump("/home/opc/tpch_dump", {dryRun: true, resetProgress:true, ignoreVersion:true})
```
![](./images/HW36_hw.png)

上記のコマンドは予行演習オプション(dryRun: true)が指定されていおり、実行時に何もエラーが発生しなければ次のコマンドを実行してデータを格納します。
```
util.loadDump("/home/opc/tpch_dump", {dryRun: false, resetProgress:true, ignoreVersion:true})
```
![](./images/HW37_hw.png)

### **Step 6.3:**
- MySQL Shellで以下のコマンドを実行してデータが格納されたことを確認します。

```
\sql

SHOW DATABASES;
```
(実行結果)
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| tpch               |
+--------------------+
```
続いて、以下のコマンドを実行します。
```
USE tpch;

SHOW TABLES;
```
(実行結果)
```
+----------------+
| Tables_in_tpch |
+----------------+
| customer       |
| lineitem       |
| nation         |
| orders         |
| part           |
| partsupp       |
| region         |
| supplier       |
+----------------+
```

### **Step 6.4:**
- それでは、クエリを実行してみましょう。 
同じプロンプトで以下のSQLの実行時間を確認します。(約12-13秒):
```
SELECT
    l_returnflag,
    l_linestatus,
    SUM(l_quantity) AS sum_qty,
    SUM(l_extendedprice) AS sum_base_price,
    SUM(l_extendedprice * (1 - l_discount)) AS sum_disc_price,
    SUM(l_extendedprice * (1 - l_discount) * (1 + l_tax)) AS sum_charge,
    AVG(l_quantity) AS avg_qty,
    AVG(l_extendedprice) AS avg_price,
    AVG(l_discount) AS avg_disc,
    COUNT(*) AS count_order
FROM
    lineitem
WHERE
    l_shipdate <= DATE '1998-12-01' - INTERVAL '90' DAY
GROUP BY l_returnflag , l_linestatus
ORDER BY l_returnflag , l_linestatus;
```
```
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| l_returnflag | l_linestatus | sum_qty     | sum_base_price  | sum_disc_price    | sum_charge          | avg_qty   | avg_price    | avg_disc | count_order |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| A            | F            | 37734107.00 |  56586554400.73 |  53758257134.8700 |  55909065222.827692 | 25.522006 | 38273.129735 | 0.049985 |     1478493 |
| N            | F            |   991417.00 |   1487504710.38 |   1413082168.0541 |   1469649223.194375 | 25.516472 | 38284.467761 | 0.050093 |       38854 |
| N            | O            | 74476040.00 | 111701729697.74 | 106118230307.6056 | 110367043872.497010 | 25.502227 | 38249.117989 | 0.049997 |     2920374 |
| R            | F            | 37719753.00 |  56568041380.90 |  53741292684.6040 |  55889619119.831932 | 25.505794 | 38250.854626 | 0.050009 |     1478870 |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
4 rows in set (12.6570 sec)
```
- 以下のコマンドを実行して、MySQL Shellを終了します。
  
```
 \exit
```

## まとめ
次の演習でHeatwaveサービスを有効にしてクエリを再度実行し、結果を比較してみましょう！


**[<< Lab 05](/Lab05/README.md)** | **[Home](../README.md)** | **[Lab 07 >>](/Lab07/README.md)**

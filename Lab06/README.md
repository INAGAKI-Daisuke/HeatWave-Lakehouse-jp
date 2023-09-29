# Lab 06: オブジェクトストレージのファイルをMySQL HeatWave Lakehouseに取り込む

## 学べること
- HeatWaveに接続し、Bulket上のデータを取り込む

- リソースプリンシパルを使用する
- 事前認証リクエストURLの使用する

## リソースプリンシパルを使用する:

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
  




## PAR URLを使用する:
- OCIオブジェクトストアバゲット内のファイルまたはフォルダに対し、PAR(事前認証リクエスト)を作成することができます。PARが作成されると、一意のURLが生成されます。このURLを知っているユーザならば誰でも、curlやwgetなどの標準HTTPツールを使って、PARで識別されるオブジェクトストレージのリソースにアクセスできます
  
![](./images/HW38_hw.png)

- 下記はPARを使用したテーブル作成DDL文です。リージョンやバケット、プレフィックスなどの詳細はPARの一部にあるため、下の例では明示していません。
```
CREATE TABLE MYTABLE ( M_OKEY bigint NOT NULL,  
                                               ...  
                                               ...
                                              PRIMARY KEY (M_OKEY,M_NBR))
                                              ENGINE=lakehouse
       ENGINE_ATTRIBUTE='{"file": [{"file": [{"par": "https://objectstorage.us-ashburn-1.oraclecloud.com/p/uXpola..32I/n/mynamespace/b/mybucket/o/path/filename.txt"}],
                              "dialect": {"format": "csv",
                                                 "is_strict_mode": false, "field_delimiter": "|",
                                                "record_delimiter": "|\\n"}}';
```


## まとめ
次の演習で事前認証リクエストURLの使用して実際にデータを取り込みを行います


**[<< Lab 05](/Lab05/README.md)** | **[Home](../README.md)** | **[Lab 07 >>](/Lab07/README.md)**

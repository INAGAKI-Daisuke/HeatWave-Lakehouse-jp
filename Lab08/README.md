# Lab 08: PAR URLを使用してデータをMySQL HeatWave Lakehouseに取り込む

## Manually Loadingによる取り込み

このセクションでは、マニュアルによるテーブル情報の作成によるデータのロードについて説明します。


## 参考情報

TPCHに対応したサンプルテーブルは　**[ https://github.com/oracle/heatwave-tpch ]**　に公開されています


## 手順

### **Step 8.1:データベースの作成**

    データを展開するデータベース[tpch100]を作成します
```
create database tpch100;
alter database tpch100 CHARACTER SET ascii COLLATE ascii_bin;
use tpch100;
```



### **Step 8.2:テーブルの作成**
```
CREATE TABLE LINEITEM ( L_ORDERKEY    BIGINT NOT NULL COMMENT 'RAPID_COLUMN=DATA_PLACEMENT_KEY=1', 
                        L_PARTKEY     INTEGER NOT NULL, 
                        L_SUPPKEY     INTEGER NOT NULL,
                        L_LINENUMBER  INTEGER NOT NULL, 
                        L_QUANTITY    DECIMAL(15,2) NOT NULL, 
                        L_EXTENDEDPRICE  DECIMAL(15,2) NOT NULL,
                        L_DISCOUNT    DECIMAL(15,2) NOT NULL, 
                        L_TAX         DECIMAL(15,2) NOT NULL,
                        L_RETURNFLAG  CHAR(1) NOT NULL,
                        L_LINESTATUS  CHAR(1) NOT NULL,
                        L_SHIPDATE    DATE NOT NULL, 
                        L_COMMITDATE  DATE NOT NULL,
                        L_RECEIPTDATE DATE NOT NULL, 
                        L_SHIPINSTRUCT CHAR(25) NOT NULL, 
                        L_SHIPMODE    CHAR(10) NOT NULL,
                        L_COMMENT     VARCHAR(44) NOT NULL, 
                        PRIMARY KEY (L_ORDERKEY, L_LINENUMBER))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{"region":"<region>", 
       "namespace":"<namespace>", 
       "bucket":"<bucket_name>", 
       "name":"<lineitem_file_location>"}]}';
```
- ENGINE_ATTRIBUTEをPAR URLに変更します。
```  
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/lineitem.tbl"}]
     }';
```

### **Step 8.3:全てのテーブルを作成**

```
CREATE TABLE LINEITEM ( L_ORDERKEY    BIGINT NOT NULL COMMENT 'RAPID_COLUMN=DATA_PLACEMENT_KEY=1', 
                        L_PARTKEY     INTEGER NOT NULL, 
                        L_SUPPKEY     INTEGER NOT NULL,
                        L_LINENUMBER  INTEGER NOT NULL, 
                        L_QUANTITY    DECIMAL(15,2) NOT NULL, 
                        L_EXTENDEDPRICE  DECIMAL(15,2) NOT NULL,
                        L_DISCOUNT    DECIMAL(15,2) NOT NULL, 
                        L_TAX         DECIMAL(15,2) NOT NULL,
                        L_RETURNFLAG  CHAR(1) NOT NULL,
                        L_LINESTATUS  CHAR(1) NOT NULL,
                        L_SHIPDATE    DATE NOT NULL, 
                        L_COMMITDATE  DATE NOT NULL,
                        L_RECEIPTDATE DATE NOT NULL, 
                        L_SHIPINSTRUCT CHAR(25) NOT NULL, 
                        L_SHIPMODE    CHAR(10) NOT NULL,
                        L_COMMENT     VARCHAR(44) NOT NULL, 
                        PRIMARY KEY (L_ORDERKEY, L_LINENUMBER))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/lineitem.tbl"}]
     }';
```
```
CREATE TABLE ORDERS  ( O_ORDERKEY       BIGINT NOT NULL, 
                       O_CUSTKEY        INTEGER NOT NULL, 
                       O_ORDERSTATUS    CHAR(1) NOT NULL,
                       O_TOTALPRICE     DECIMAL(15,2) NOT NULL,
                       O_ORDERDATE      DATE NOT NULL, 
                       O_ORDERPRIORITY  CHAR(15) NOT NULL,
                       O_CLERK          CHAR(15) NOT NULL,
                       O_SHIPPRIORITY   INTEGER NOT NULL,
                       O_COMMENT  VARCHAR(79) NOT NULL,
                       PRIMARY KEY (O_ORDERKEY))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/orders.tbl"}]
     }';
```

```
create table CUSTOMER ( C_CUSTKEY     INTEGER NOT NULL, 
                        C_NAME        VARCHAR(25) NOT NULL, 
                        C_ADDRESS     VARCHAR(40) NOT NULL, 
                        C_NATIONKEY   INTEGER NOT NULL,
                        C_PHONE       CHAR(15) NOT NULL, 
                        C_ACCTBAL     DECIMAL(15,2) NOT NULL, 
                        C_MKTSEGMENT  CHAR(10) NOT NULL, 
                        C_COMMENT     VARCHAR(117) NOT NULL, 
                        PRIMARY KEY (C_CUSTKEY)) 
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/customer.tbl"}]
     }';
```

```
CREATE TABLE SUPPLIER ( S_SUPPKEY     INTEGER NOT NULL, 
                        S_NAME        CHAR(25) NOT NULL, 
                        S_ADDRESS     VARCHAR(40) NOT NULL,
                        S_NATIONKEY   INTEGER NOT NULL, 
                        S_PHONE       CHAR(15) NOT NULL, 
                        S_ACCTBAL     DECIMAL(15,2) NOT NULL,
                        S_COMMENT     VARCHAR(101) NOT NULL, 
                        PRIMARY KEY (S_SUPPKEY))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/supplier.tbl"}]
     }';
```

```
CREATE TABLE NATION ( N_NATIONKEY      INTEGER NOT NULL, 
                      N_NAME           CHAR(25) NOT NULL, 
                      N_REGIONKEY      INTEGER NOT NULL, 
                      N_COMMENT        VARCHAR(152) NOT NULL, 
                      PRIMARY KEY (N_NATIONKEY)) 
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/nation.tbl"}]
     }';
```
```
CREATE TABLE REGION ( R_REGIONKEY       INTEGER NOT NULL, 
                      R_NAME            CHAR(25) NOT NULL, 
                      R_COMMENT         VARCHAR(152) NOT NULL, 
                      PRIMARY KEY (R_REGIONKEY)) 
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/region.tbl"}]
     }';
```
```
CREATE TABLE PART  ( P_PARTKEY          INTEGER NOT NULL, 
                     P_NAME             VARCHAR(55) NOT NULL, 
                     P_MFGR             CHAR(25) NOT NULL,
                     P_BRAND            CHAR(10) NOT NULL, 
                     P_TYPE             VARCHAR(25) NOT NULL, 
                     P_SIZE             INTEGER NOT NULL,
                     P_CONTAINER        CHAR(10) NOT NULL,  
                     P_RETAILPRICE      DECIMAL(15,2) NOT NULL,
                     P_COMMENT          VARCHAR(23) NOT NULL, 
                     PRIMARY KEY (P_PARTKEY))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/part.tbl"}]
     }';
```
```
CREATE TABLE PARTSUPP ( PS_PARTKEY      INTEGER NOT NULL, 
                        PS_SUPPKEY      INTEGER NOT NULL, 
                        PS_AVAILQTY     INTEGER NOT NULL,
                        PS_SUPPLYCOST   DECIMAL(15,2)  NOT NULL, 
                        PS_COMMENT      VARCHAR(199) NOT NULL,
                        PRIMARY KEY (PS_PARTKEY, PS_SUPPKEY))
ENGINE=lakehouse 
secondary_engine=rapid 
ENGINE_ATTRIBUTE='{"file": 
    [{ "par": "https://objectstorage.ap-tokyo-1.oraclecloud.com/p/hu8s-4CnB04hFZiU2-qB2OYLrsoyL1vF_8AVs82oeHAVjyM1E2DDWASTT_fQumCj/n/idazzjlcjqzj/b/tpch-100/o/partsupp.tbl"}]
     }';
```

### **Step 8.3:MySQL HeatWave Lakehouseへのロード**
- secondary_loadを実行し、対象テーブルに対して指定したファイルをHeatWaveへ取り込みを実施します

```
alter table LINEITEM secondary_load;
alter table ORDERS secondary_load;
alter table CUSTOMER secondary_load;
alter table SUPPLIER secondary_load;
alter table NATION secondary_load;
alter table REGION secondary_load;
alter table PART secondary_load;
alter table PARTSUPP secondary_load;
```

### **Step 8.3:**
- 以下のコマンドを実行してMySQL Shellを使用してMDSに接続します。
```
mysqlsh --user=admin --password=Oracle.123 --host=<mysql_private_ip_address> --port=3306 --js
```

- - MySQL ShellプロンプトでMDSにデータが格納できるか確認します。
```
util.loadDump("/home/opc/oac_hands_on", {dryRun: true, resetProgress:true, ignoreVersion:true})
```
![](./images/cloud-shell-16.png)

上記のコマンドは予行演習オプション(dryRun: true)が指定されていおり、実行時に何もエラーが発生しなければ次のコマンドを実行してデータを格納します。
```
util.loadDump("/home/opc/oac_hands_on", {dryRun: false, resetProgress:true, ignoreVersion:true})
```
![](./images/cloud-shell-17.png)

### **Step 8.4:**
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
| dbuser01           |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| tpch               |
+--------------------+
```
続いて、以下のコマンドを実行します。
```
USE dbuser01;

SHOW TABLES;
```
(実行結果)
```
+--------------------+
| Tables_in_dbuser01 |
+--------------------+
| CUSTOMER           |
| RFM                |
+--------------------+
```

### **Step 8.5:**
- MySQL Shellで以下のコマンドを実行してdbuser01データベース内のテーブルをHeatWaveにロードします。

```
CALL sys.heatwave_load (JSON_ARRAY("dbuser01"),NULL);
```


### **Step 8.6:**

- 画面左上のメニューから  _**アナリティクスとAI -> アナリティクス・クラウド**_ を選択します。
  

![](./images/one.png)

### **Step 8.7:**
_**インスタンスの作成**_ をクリックして表示されたウインドウで、名前に _**OACDemo**_ と入力し、容量を _**2 OCPU**_ にします。また、機能セットが _**Enterprise Analytics**_ 、ライセンス・タイプが _**ライセンス込み**_ であることを確認して、 _**作成**_ をクリックします。
_**Note:**_ インスタンスの作成には _**15-20 分程度**_ かかります。

![](./images/two.png)

### **Step 8.8:**
ステータスが _**ACTIVE**_ になったら、左下の _**プライベート・アクセス・チャネル**_ をクリックし、 _**プライベート・アクセス・チャネルの構成**_ をクリックします。

![](./images/three.png)

### **Step 8.9:**
表示されたウインドウで、名前に _**PrivateChannel**_ 、仮想クラウド・ネットワークを _**analytics_vcn_test**_ 、サブネットを _**パブリック・サブネット-analytics_vcn_test**_ にします。

また、DNSゾーンの _**DNSゾーンとしての仮想クラウド・ネットワークのドメイン名(analyticsvcntes.oraclevcn.com)**_ にチェックを入れます。そして、追加で表示されたDNSゾーンの入力行を右側の _**✕**_ をクリックして削除します。その後、 _**構成**_ をクリックしてプライベート・アクセス・チャネルを構成します。

_**Note:**_ プライベート・アクセス・チャネルの構成には _**40分-50分程度**_ かかります。 

![](./images/four.png)

### **Step 8.10:**
ステータスが _**ACTIVE**_ になったら、_**分析ホームページ**_ ボタンを押します。

![](./images/five.png)

### **Step 8.11:**
MySQL Database Serviceへの接続を作成します。右上の _**作成**_ をクリックし、_**接続**_ を選択します。

![](./images/six.png)

### **Step 8.12:**

接続タイプを選択する画面が表示されるので、 _**MySQL**_ を選択します。

![](./images/HW36.PNG)



表示されるウインドウに接続に必要な情報を入力します。接続名には _**HeatWave**_ を入力します。
また、ホスト名にはMDSの _**Internal FQDN**_ を入力する必要があることに注意して下さい。Internal FQDNを確認するためには、MDSの詳細ページを参照する必要があります。 

![](./images/eight.png)


データベース名には _**dbuser01**_ を入力して下さい。また、ユーザー名、パスワードはMDS作成時に指定したものを入力して下さい。

```
username: admin
password: Oracle.123
```
必須項目を入力し終わったら、 _**保存**_ をクリックします。

![](./images/seven.png)

接続が正常に作成出来れば、Oracle Analytics Cloud から MySQL Database Service for Heatwawe接続してデータを参照できるようになります。

この後のOACのハンズオン手順は、[こちら](https://objectstorage.ap-osaka-1.oraclecloud.com/p/lUQ4xrSZputspcfF9LGd21C3ey62MPA4KVBGNdalPEGA7aB2jgdY4XZ4MyIoMA9e/n/idazzjlcjqzj/b/workshop/o/OAC%E4%BD%93%E9%A8%93%E3%83%8F%E3%83%B3%E3%82%BA%E3%82%AA%E3%83%B3.zip)の資料で説明します。また、[こちら](https://videohub.oracle.com/channel/Oracle+Japan+Analyitcs+Channel/228666053)の動画でもハンズオン手順を案内していますが、この動画ではDBにHeatWaveではなく、Oracle Autonomous Data Warehouse(ADW)を使用していることに注意して下さい。



### **備考:**

・OACからのアクセスでHeatWaveが使えているかどうかは、ステータス変数 _**rapid_query_offload_count**_ の値が増加するかどうかで確認できます。確認する時は、 MySQL ShellでMDSに接続し、SQLモードにした状態で以下のコマンドを実行します。

```
SHOW STATUS LIKE 'rapid_query_offload_count';
```

・OACからのアクセスで思うようにパフォーマンスが出ず、 _**rapid_query_offload_count**_ の値も増加しない場合は、HeatWaveが正しく使えていない可能性がありますので、MySQLお問い合わせ窓口(MySQL-Sales_jp_grp@oracle.com)までお問い合わせ下さい。なお、HeatWaveでは一部対応できていないデータ型、関数などがあります。現在のHeatWaveの制限事項については、[こちら](https://dev.mysql.com/doc/heatwave/en/heatwave-limitations.html)のドキュメントを参照して下さい。(HeatWaveのバージョンが上がる毎に、制限事項は減りつつあります)

**[<< Lab 07](/Lab07/README.md)** | **[Home](../README.md)** | **[Lab 09 >>](/Lab09/README.md)**

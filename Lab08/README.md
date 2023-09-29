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


**[<< Lab 07](/Lab07/README.md)** | **[Home](../README.md)** | **[Lab 09 >>](/Lab09/README.md)**

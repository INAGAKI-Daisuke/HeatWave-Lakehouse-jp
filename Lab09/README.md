# Lab 09: SQL一覧

- TPC-H Query 1 - Pricing Summary Report

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
    LINEITEM
WHERE
    l_shipdate <= DATE '1998-12-01' - INTERVAL '90' DAY
GROUP BY l_returnflag , l_linestatus
ORDER BY l_returnflag , l_linestatus;
```
- TPC-H Query 2 - Minimum Cost Supplier Query
  ```
  SELECT 
   s_acctbal, s_name, n_name, p_partkey, p_mfgr, 
       s_address, s_phone, s_comment
FROM 
     PART, 
     SUPPLIER, 
     PARTSUPP, 
     NATION, 
     REGION 
WHERE 
    p_partkey = ps_partkey
    AND s_suppkey = ps_suppkey
    AND s_nationkey = n_nationkey
    AND n_regionkey = r_regionkey
    AND p_size = 15  
    AND p_type LIKE '%BRASS'  
    AND r_name = 'EUROPE'  
    AND ps_supplycost = ( 
             SELECT 
                 MIN(ps_supplycost)  
             FROM 
                 PARTSUPP, 
                 SUPPLIER, 
                 NATION, 
                 REGION 
             WHERE 
                s_suppkey = ps_suppkey
                AND s_nationkey = n_nationkey
                AND n_regionkey = r_regionkey
                AND p_partkey = ps_partkey 
                AND r_name = 'EUROPE'  
    ) 
 ORDER BY 
    s_acctbal desc, n_name, s_name, p_partkey 
LIMIT 100;
```

- TPC-H Query 3 - Shipping Priority
```
SELECT   
    l_orderkey,
    SUM(l_extendedprice * (1 - l_discount)) AS revenue,
    o_orderdate,
    o_shippriority
FROM
    CUSTOMER,
    ORDERS,
    LINEITEM
WHERE
    c_mktsegment = 'BUILDING'
    AND c_custkey = o_custkey
    AND l_orderkey = o_orderkey
    AND o_orderdate < DATE '1995-03-15'
    AND l_shipdate > DATE '1995-03-15'
GROUP BY l_orderkey , o_orderdate , o_shippriority
ORDER BY revenue DESC , o_orderdate
LIMIT 10;
```

**[<< Lab 08](/Lab08/README.md)** | **[Home](../README.md)** | 

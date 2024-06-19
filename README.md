# Sonar_bugreports
This page lists all bugs found by Sonar (https://github.com/Syang111/Sonar). Up to now, we have found xx unique bugs in MySQL, TiDB, MariaDB and SQLite,  with xx verified and xx fixed by developers.

To alleviate the burden on developers in identifying the root cause, we have simplified some test cases for bugs discovered by Sonar before reporting them to developers.

## MySQL

* #1 [http://bugs.mysql.com/114196](https://bugs.mysql.com/bug.php?id=114196)

  **Status**: fixed

  **Version**: 8.0.35

  **Test case**

  ```sql
  CREATE TABLE IF NOT EXISTS t0(c0 DECIMAL);
  REPLACE INTO t0(c0) VALUES(0.1);
  
  SELECT t0.c0 FROM t0 WHERE t0.c0 <= - 0.8; -- expected:{} actual:{0}
  ```

* #2[ http://bugs.mysql.com/113586](https://bugs.mysql.com/bug.php?id=113586)

  **Status**: Verified

  **Version**: 8.0

  **Test case**

  ```sql
  CREATE TABLE t0(c0 DECIMAL) ;
  REPLACE INTO t0(c0) VALUES("512");

  -- sql1
  
  SELECT f1 FROM (
  SELECT t0.c0 >> IFNULL("\r8*&t", NULL) AS f1 FROM t0
  ) AS t WHERE f1!=123;
  
  +------+
  | f1   |
  +------+
  |    2 |
  +------+
  1 row in set (0.00 sec)
  
  -- sql2
  
  SELECT f1 FROM (
  SELECT t0.c0 >> (IFNULL("\r8*&t", NULL)) AS f1,
  ((t0.c0 >> IFNULL("\r8*&t", NULL)) != 123) IS TRUE AS flag FROM t0
  ) AS t WHERE flag=1;
  
  +------+
  | f1   |
  +------+
  |  512 |
  +------+
  1 row in set (0.00 sec)

  ```
  
* #3[ http://bugs.mysql.com/114149](https://bugs.mysql.com/bug.php?id=114149)

  **Status**: Verified

  **Version**: 8.0

  **Test case**

  ```sql
  CREATE TABLE IF NOT EXISTS t0(c0 SMALLINT(157)); 
  INSERT INTO t0(c0) VALUES(1); 
  
  SELECT f1 FROM (SELECT (t0.c0 - SUBDATE('2022-07-06' ,INTERVAL 47 MINUTE)) AS f1 FROM t0) AS t; -- expected:{-2021} actual:{-20220705231299}
  ```


* #4[ http://bugs.mysql.com/114224](https://bugs.mysql.com/bug.php?id=114224)

  **Status**: Verified
 
  **Version**: 8.0
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c0 TINYINT);
  CREATE INDEX i0 ON t0(c0 DESC); 
  INSERT IGNORE INTO t0(c0) VALUES("c");
  
  SELECT t0.c0 FROM t0 WHERE t0.c0=ABS(0.1); -- expected:{} actual:{0}
  ```

* #5[ http://bugs.mysql.com/114282](https://bugs.mysql.com/bug.php?id=114282)

  **Status**: Verified
 
  **Version**: 8.0.35
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c0 FLOAT) ;
  INSERT INTO t0(c0) VALUES(NULL);
  
  SELECT COALESCE(t0.c0, 400145287) AS f1 FROM t0 GROUP BY c0 HAVING (f1) & (0.6979778231950815);
  Empty set (0.01 sec)
  
  SELECT f1 FROM (SELECT COALESCE(t0.c0, 400145287) AS f1, (COALESCE(t0.c0, 400145287) & (0.6979778231950815)) IS TRUE AS flag FROM t0 GROUP BY c0 HAVING flag=1) as t;
  +-----------+
  | f1        |
  +-----------+
  | 400145000 |
  +-----------+
  ```

* #6[ http://bugs.mysql.com/114384](https://bugs.mysql.com/bug.php?id=114384)

  **Status**: Verified
 
  **Version**: 8.0.15,8.0.36
 
  **Test case**
 
  ```sql
  CREATE TABLE IF NOT EXISTS t1(c0 SMALLINT) ;
  INSERT INTO t1(c0) VALUES(0.49);
  CREATE INDEX i0 ON t1(c0);
  
  SELECT t1.c0 FROM t1 WHERE t1.c0 IN (LOG(0.6261534882548163));
  +------+
  | c0   |
  +------+
  |    0 |
  +------+
  1 row in set (0.00 sec)
  
  SELECT c0 FROM (SELECT t1.c0, (t1.c0 IN (LOG(0.6261534882548163))) IS TRUE AS flag FROM t1) as t WHERE flag=1;
  Empty set (0.00 sec)
  ```

* #7[ http://bugs.mysql.com/114383](https://bugs.mysql.com/bug.php?id=114383)

  **Status**: Verified
 
  **Version**: 8.0.23,8.0.35
 
  **Test case**
 
  ```sql
  CREATE TABLE IF NOT EXISTS t0(c0 INT) ;
  REPLACE DELAYED INTO t0(c0) VALUES(-1);
  
  SELECT ALL f1 FROM (SELECT ALL (MAX(DATE('2024-01-01 ')) OVER (PARTITION BY t0.c0)) AS f1 FROM t0) as t WHERE (f1) > (1.105003755E9);
  +------------+
  | f1         |
  +------------+
  | 2024-01-01 |
  +------------+
  1 row in set, 2 warnings (0.00 sec)
  
  SELECT f1 FROM (SELECT (MAX(DATE('2024-01-01 ')) OVER (PARTITION BY t0.c0)) AS f1, ((MAX(DATE('2024-01-01 ')) OVER (PARTITION BY t0.c0)) > (1.105003755E9)) IS TRUE AS flag FROM t0) as t WHERE flag=1;
  Empty set, 2 warnings (0.00 sec)
  ```

* #8[ http://bugs.mysql.com/114382](https://bugs.mysql.com/bug.php?id=114382)

  **Status**: Verified
 
  **Version**: 8.0.11,8.0.36
 
  **Test case**
 
  ```sql
  CREATE TABLE IF NOT EXISTS t1(c0 TINYINT(199)) ;
  INSERT LOW_PRIORITY IGNORE INTO t1(c0) VALUES (1950654919);

  SELECT f1 FROM (SELECT (LEAST(CURDATE(), (- (t1.c0)), (~ (t1.c0)),  EXISTS (SELECT 1))) AS f1 FROM t1) as t WHERE f1;
  +------+
  | f1   |
  +------+
  | -127 |
  +------+
  1 row in set, 3 warnings (0.00 sec)
  
  SELECT f1 FROM (SELECT (LEAST(CURDATE(), (- (t1.c0)),  EXISTS (SELECT 1))) AS f1, (LEAST(CURDATE(), (- (t1.c0)),   EXISTS (SELECT 1)) ) IS TRUE AS flag FROM t1) as t WHERE flag=1;
  Empty set, 6 warnings (0.00 sec)
  ```

* #9[ http://bugs.mysql.com/114381](https://bugs.mysql.com/bug.php?id=114381)

  **Status**: Verified
 
  **Version**: 8.0.19,8.0.35
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c0 DOUBLE) ;
  CREATE TABLE t1 LIKE t0;
  INSERT IGNORE INTO t1(c0) VALUES(1);
  INSERT INTO t0(c0) VALUES(0.1);

  SELECT t1.c0 AS _c0 FROM t1, t0 WHERE (( EXISTS (SELECT 1 )) < ((NOT t0.c0))) IN (((t1.c0) XOR ("")) / (TIMEDIFF('3939-09-13 16:49:10.309835', '4722-09-08 23:55:52.675528')));
  +------+
  | _c0  |
  +------+
  |    1 |
  +------+
  1 row in set, 1 warning (0.00 sec)

  SELECT _c0 FROM (SELECT t1.c0 AS _c0, ((( EXISTS (SELECT 1 )) < ((NOT t0.c0))) IN (((t1.c0) XOR ("")) / (TIMEDIFF('3939-09-13 16:49:10.309835', '4722-09-08 23:55:52.675528')))) IS TRUE AS flag FROM t1, t0) as t     WHERE flag=1;
  Empty set, 1 warning (0.00 sec)
  ```

* #10[ http://bugs.mysql.com/114380](https://bugs.mysql.com/bug.php?id=114380)

  **Status**: Verified
 
  **Version**: 8.0.13,8.0.36
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c1 FLOAT) ;
  INSERT INTO t0(c1) VALUES(0.1);
  
  SELECT f1 FROM (SELECT (LEAST('-2',  EXISTS (SELECT 1  ), CAST(t0.c1 AS SIGNED), TIMESTAMP('2024-03-17'))) AS f1 FROM t0) as t WHERE f1;
  +------+
  | f1   |
  +------+
  | -2   |
  +------+
  1 row in set, 3 warnings (0.00 sec)
  
  SELECT f1 FROM (SELECT (LEAST('-2',  EXISTS (SELECT 1  ), CAST(t0.c1 AS SIGNED), TIMESTAMP('2024-03-17'))) AS f1, (LEAST('-2',  EXISTS (SELECT 1  ), CAST(t0.c1 AS SIGNED), TIMESTAMP('2024-03-17'))) IS TRUE AS    
  flag FROM t0) as t WHERE flag=1;
  Empty set, 6 warnings (0.00 sec)
  ```

* #11[ http://bugs.mysql.com/114379](https://bugs.mysql.com/bug.php?id=114379)

  **Status**: Verified
 
  **Version**: 8.0.21,8.0.35
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c0 FLOAT) ;
  CREATE TABLE t1(c0 DECIMAL);
  INSERT INTO t1(c0) VALUES(1);
  CREATE INDEX i1 ON t0((t0.c0 IS NOT TRUE), ((t0.c0 IS NOT FALSE) & (NULL XOR t0.c0)));

  SELECT t1.c0 AS _c0 FROM t1 LEFT OUTER JOIN t0 ON 1 WHERE t0.c0 IS NOT TRUE;
  +------+
  | _c0  |
  +------+
  |    1 |
  +------+
  1 row in set (0.00 sec)
  
  SELECT _c0 FROM (SELECT  t1.c0 AS _c0, (t0.c0 IS NOT TRUE) IS TRUE AS flag FROM t1 LEFT OUTER JOIN t0 ON 1 ) as t WHERE flag=1;
  Empty set (0.00 sec)
  ```

* #12[ http://bugs.mysql.com/114378](https://bugs.mysql.com/bug.php?id=114378)

  **Status**: Verified
 
  **Version**: 8.0
 
  **Test case**
 
  ```sql
  CREATE TABLE t0(c0 FLOAT);
  INSERT INTO t0(c0) VALUES(-1);
  
  SELECT f1 FROM (SELECT ((t0.c0) ^ (CONVERT_TZ('2001-10-22 16:13:59.9', 'GMT','MET'))) AS f1 FROM t0) as t WHERE (f1) > (1); 
  +----------------------+
  | f1                   |
  +----------------------+
  | 18446724062687370255 |
  +----------------------+
  1 row in set (0.00 sec)
  
  SELECT ALL f1 FROM (SELECT ((t0.c0) ^ (CONVERT_TZ('2001-10-22 16:13:59.9', 'GMT','MET'))) AS f1, (((t0.c0) ^ (CONVERT_TZ('2001-10-22 16:13:59.9', 'GMT','MET'))) > (1)) IS TRUE AS flag FROM t0) as t WHERE flag=1;
  +----------------------+
  | f1                   |
  +----------------------+
  | 18446724062687370215 |
  +----------------------+
  1 row in set (0.00 sec)
  ```

* #13[ http://bugs.mysql.com/113578](https://bugs.mysql.com/bug.php?id=113578)

  **Status**: Verified
 
  **Version**: 8.0
 
  **Test case**
 
  ```sql
  CREATE TABLE IF NOT EXISTS t1(c0 int) ;
  REPLACE INTO t1(c0) VALUES(1),(2);
  
  SELECT AVG(-1.7E308) AS f1 FROM t1 HAVING f1;
  +------+
  | f1   |
  +------+
  |    0 |
  +------+
  1 row in set (0.00 sec)
  
  mysql> SELECT f1 FROM (SELECT AVG(-1.7E308) AS f1, AVG(-1.7E308) IS TRUE AS flag FROM t1 HAVING flag=1) AS tmp_t;
  +-------------------------+
  | f1                      |
  +-------------------------+
  | -1.7976931348623157e308 |
  +-------------------------+
  1 row in set (0.00 sec)
  ```

## TiDB

* #1 [https://github.com/pingcap/tidb/issues/51842](https://github.com/pingcap/tidb/issues/51842)

  **Status**: Verified

  **Version**: v6.4.0, v7.6.0

  **Test case**

  ```sql
  CREATE TABLE t0(c0 DOUBLE);
  REPLACE INTO t0(c0) VALUES (0.40194983109852933);
  CREATE VIEW v0(c0) AS SELECT CAST(')' AS TIME) FROM t0 WHERE '0.030417148673465677';
  
  mysql> SELECT f1 FROM (SELECT NULLIF(v0.c0, 1371581446) AS f1 FROM v0, t0) AS t WHERE f1 <=> 1292367147;
  +------+
  | f1   |
  +------+
  | NULL |
  +------+
  1 row in set, 3 warnings (0.01 sec)
  
  mysql> SELECT f1 FROM (SELECT NULLIF(v0.c0, 1371581446) AS f1, (NULLIF(v0.c0, 1371581446) <=> 1292367147 ) IS TRUE AS flag FROM v0, t0) AS t WHERE flag=1;
  Empty set, 3 warnings (0.00 sec)
  ```

## SQLite

* #1 [https://sqlite.org/forum/forumpost/9dcb5f4c4a](https://sqlite.org/forum/forumpost/9dcb5f4c4a)

  **Status**: fixed

  **Version**: 3.43.0

  **Test case**

  ```sql
  CREATE TABLE t0 (c0 DOUBLE );
  INSERT INTO t0(c0) VALUES (1),(2),(3);

  SELECT SUM(1.7976931348623157E308) as aggr FROM t0 WHERE c0 > 1; -- {NULL}

  SELECT aggr FROM (SELECT SUM(1.7976931348623157E308) as aggr, (c0 > 1) is true as flag FROM t0) WHERE flag=1; -- {}
  ```


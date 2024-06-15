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

  **Version**: 8.0+

  **Test case**

  ```sql
  CREATE TABLE t0(c0 DECIMAL) ;
  REPLACE INTO t0(c0) VALUES("512");
 
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

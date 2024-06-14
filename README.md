# Sonar_bugreports
This page lists all bugs found by Sonar (https://github.com/Syang111/Sonar). Up to now, we have found xx bugs in MySQL, TiDB, MariaDB, SQLite xx of which have been verified by developers.


# impomysql_bugreports

This page lists all bugs found by impomysql (https://github.com/qaqcatz/impomysql_binary). Up to now, we have found 41 bugs in MySQL, MariaDB, TiDB and OceanBase, 39 of which have been confirmed by developers.

## MySQL

* #1 http://bugs.mysql.com/108936

  **Status**: Verified

  **Version**: 8.0.29, 8.0.30

  **Test case**

  ```sql
  drop table if exists t;
  create table t (c1 int);
  insert into t values (1), (2), (3);
  
  mysql> SELECT ~f1 FROM (SELECT REPEAT(1234567890, 3) AS f1 FROM t) AS t1; --sql1
  +------+
  | ~f1  |
  +------+
  |    0 |
  |    0 |
  |    0 |
  +------+
  3 rows in set, 3 warnings (0.00 sec)
  
  mysql> SELECT ~f1 FROM (SELECT DISTINCT REPEAT(1234567890, 3) AS f1 FROM t) AS t1; --sql2
  +---------------------+
  | ~f1                 |
  +---------------------+
  | 9223372036854775808 |
  +---------------------+
  1 row in set, 1 warning (0.00 sec)
  ```

* #2 http://bugs.mysql.com/108937

  **Status**: Verified

  **Version**: 5.6.17, 8.0.30

  **Test case**

  ```sql
  drop table if exists t;
  create table t (c1 double);
  insert into t values (79.1819),(12.991),(1);
  
  mysql> SELECT c1-DATE_SUB('2008-05-25', INTERVAL 1 HOUR_MINUTE) AS f1 FROM t HAVING f1 != 0; -- sql1
  +------------+
  | f1         |
  +------------+
  | -1928.8181 |
  |  -1995.009 |
  |      -2007 |
  +------------+
  3 rows in set (0.00 sec)
  
  mysql> SELECT c1-DATE_SUB('2008-05-25', INTERVAL 1 HOUR_MINUTE) AS f1 FROM t HAVING 1; -- sql2
  +---------------------+
  | f1                  |
  +---------------------+
  | -20080524235820.816 |
  | -20080524235887.008 |
  |     -20080524235899 |
  +---------------------+
  3 rows in set (0.00 sec)
  ```

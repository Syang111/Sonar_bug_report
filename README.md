# Sonar_bugreports
This page lists all bugs found by Sonar (https://github.com/Syang111/Sonar). Up to now, we have found xx unique bugs in MySQL, TiDB, MariaDB and SQLite,  with xx verified and xx fixed by developers.

To alleviate the burden on developers in identifying the root cause, we have simplified the test cases for bugs discovered by Sonar before reporting them to developers.

## MySQL

* #1 [http://bugs.mysql.com/108936](https://bugs.mysql.com/bug.php?id=114196)

  **Status**: fixed

  **Version**: 8.0.35

  **Test case**

  ```sql
  CREATE TABLE IF NOT EXISTS t0(c0 DECIMAL);
  REPLACE INTO t0(c0) VALUES(0.1);
  SELECT t0.c0 FROM t0 WHERE t0.c0 <= - 0.8; expected:{} actual{0}
  ```

* #2 http://bugs.mysql.com/108937

  **Status**: Verified

  **Version**: 5.6.17, 8.0.30

  **Test case**

  ```sql

  ```

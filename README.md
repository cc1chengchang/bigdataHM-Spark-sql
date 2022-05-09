
## 作业一：为 Spark SQL 添加一条自定义命令
SHOW VERSION；
显示当前 Spark 版本和 Java 版本。

see question1.diff

## 作业二：构建 SQL 满足如下要求
通过 set spark.sql.planChangeLog.level=WARN，查看：
构建一条 SQL，同时 apply 下面三条优化规则：
CombineFilters
CollapseProject
BooleanSimplification

answer
(base) chchang@chchang-a01 data % cat ./customer.csv
name;age;job
Jorge;30;Developer
Bob;32;Developer
anil;22;Developer
George;18;Developer


spark-sql> CREATE EXTERNAL TABLE IF NOT EXISTS t_customer(name string, age int, job string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ';' LOCATION '/Users/chchang/work/learning/BigData/data';

spark-sql> select a.job from (select name, job, age from t_customer where 1="1" and age > 5) a where a.age<30;
Developer
Developer
Time taken: 6.989 seconds, Fetched 2 row(s)

see question2_1.log

构建一条 SQL，同时 apply 下面五条优化规则：
ConstantFolding
PushDownPredicates
ReplaceDistinctWithAggregate
ReplaceExceptWithAntiJoin
FoldablePropagation

answer
spark-sql> (select a.job , a.age + (100 + 80) , Now() z from ( select distinct name, age , job from t_customer ) a where a.age>10 order by z)  except (select a.job , a.age + (100 + 80), Now() z from ( select distinct name, age , job from t_customer ) a where a.name = "George");
Developer	212	2022-05-08 23:11:01.593
Developer	210	2022-05-08 23:11:01.593
Developer	202	2022-05-08 23:11:01.593
Time taken: 3.161 seconds, Fetched 3 row(s)
spark-sql>

see question2_2.log


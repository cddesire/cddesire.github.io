---
layout:     post
title:      "Spark DataFrame示例"
date:       2018-07-12 18:30:00
author:     "Daniel"
header-img: "img/post-bg-tool.jpg"
tags:
    - Spark

---


### SQLContext

``` scala
val sqlContext = new org.apache.spark.sql.SQLContext(sc)
```

### 读取数据
``` scala
import sqlContext.implicits._
val input = sqlContext.read.json("src/main/resources/cars1.json")
input.registerTempTable("cars")
val result = sqlContext.sql("select * from cars")
result.show()
```

### 查询
``` scala
val cars = sqlContext.sql("select count(*) from cars").collect().foreach(println)
```

### 查看数据及类型
``` scala
df.show(2)
df.take(2).foreach(println)
df.head(3)

df.printSchema()
df.dtypes.foreach(println)
df.columns.foreach(println)
df.limit(3).show()
```

### StructType StructField
``` scala
val structureData = Seq(
  Row(Row("James ","","Smith"),"36636","M",3100),
  Row(Row("Michael ","Rose",""),"40288","M",4300),
  Row(Row("Robert ","","Williams"),"42114","M",1400),
  Row(Row("Maria ","Anne","Jones"),"39192","F",5500),
  Row(Row("Jen","Mary","Brown"),"","F",-1)
)

val structureSchema = new StructType()
  .add("name",new StructType()
    .add("firstname",StringType)
    .add("middlename",StringType)
    .add("lastname",StringType))
  .add("id",StringType)
  .add("gender",StringType)
  .add("salary",IntegerType)

val df2 = spark.createDataFrame(spark.sparkContext.parallelize(structureData),structureSchema)


val arrayStructureData = Seq(
  Row(Row("James ","","Smith"),List("Cricket","Movies"),Map("hair"->"black","eye"->"brown")),
  Row(Row("Michael ","Rose",""),List("Tennis"),Map("hair"->"brown","eye"->"black")),
  Row(Row("Robert ","","Williams"),List("Cooking","Football"),Map("hair"->"red","eye"->"gray")),
  Row(Row("Maria ","Anne","Jones"),null,Map("hair"->"blond","eye"->"red")),
  Row(Row("Jen","Mary","Brown"),List("Blogging"),Map("white"->"black","eye"->"black"))
)

val arrayStructureSchema = new StructType()
  .add("name",new StructType().add("firstname",StringType).add("middlename",StringType).add("lastname",StringType)).add("hobbies", ArrayType(StringType))
  .add("properties", MapType(StringType,StringType))

val df5 = spark.createDataFrame(spark.sparkContext.parallelize(arrayStructureData),arrayStructureSchema)

// case class 转 schema

case class Name(first:String,last:String,middle:String)
case class Employee(fullName:Name,age:Integer,gender:String)
import org.apache.spark.sql.catalyst.ScalaReflection
val schema = ScalaReflection.schemaFor[Employee].dataType.asInstanceOf[StructType]
val encoderSchema = Encoders.product[Employee].schema
encoderSchema.printTreeString()
```

### 排序
``` scala
import sqlContext.implicits._
df.orderBy($"col1".desc)
df.sort($"name".desc).show()
df.sort($"name", $"age".desc)

import org.apache.spark.sql.functions._
df.orderBy(desc("name")).show()

.sort($"avg(age)".desc).show()
```

### 统计
``` scala
df.groupBy("name").count().show()

df.select(avg($"age").as("avg_age")).show()
df.select($"name", $"age" + 1).show()

// 过滤
//Condition

df.filter(df("state") === "OH").show(false)

//SQL Expression

df.filter("gender == 'M'").show(false)

//multiple condition

df.filter(df("state") === "OH" && df("gender") === "M").show(false)

//Array condition

df.filter(array_contains(df("languages"),"Java")).show(false)

//Struct condition

df.filter(df("name.lastname") === "Williams").show(false)

df.where($"age" > 300).show()

df.agg(max($"age")).show()

df.groupBy("gender", "state").agg(("age","sum"),("age","min"),("age","count")).show()

val stat = dataDF.select(explode(col(c)))
            .groupBy("key")
            .agg(
                count("value").as("counts"),
                min("value").as("mins"),
                max("value").as("maxs"),
                mean("value").as("means"),
                stddev("value").as("stddevs")
            )
            .select(
                collect_list("key"),
                collect_list("counts"),
                collect_list("mins"),
                collect_list("maxs"),
                collect_list("means"),
                collect_list("stddevs")
            )
            .collect()(0)
val labels = stat.getAs[Seq[String]](0).toArray
val counts = stat.getAs[Seq[Long]](1).toArray
val mins = stat.getAs[Seq[Double]](2).toArray
val maxs = stat.getAs[Seq[Double]](3).toArray
val means = stat.getAs[Seq[Double]](4).toArray
val stddevs = stat.getAs[Seq[Double]](5).toArray

df1.unionAll(df2).show()
df1.intersect(df2).show()
df1.except(df2).show()

df.describe('age', 'height').show()

df.select('name').distinct().show()

// df 一列变数组

val names = df.select("name").distinct().collect().map(_.getAs[String]("name"))
```

### 聚合函数
``` scala
import spark.implicits._

val simpleData = Seq(("James", "Sales", 3000),
  ("Michael", "Sales", 4600),
  ("Robert", "Sales", 4100),
  ("Maria", "Finance", 3000),
  ("James", "Sales", 3000),
  ("Scott", "Finance", 3300),
  ("Jen", "Finance", 3900),
  ("Jeff", "Marketing", 3000),
  ("Kumar", "Marketing", 2000),
  ("Saif", "Sales", 4100)
)
val df = simpleData.toDF("employee_name", "department", "salary")
df.show()

//avg

println("avg: " + df.select(avg("salary")).collect()(0)(0))
//collect_list

df.select(collect_list("salary")).show(false)
//collect_set

df.select(collect_set("salary")).show(false)
//countDistinct

val df2 = df.select(countDistinct("department", "salary"))
df2.show(false)
println("Distinct Count of Department & Salary: " + df2.collect()(0)(0))
println("count: "+ df.select(count("salary")).collect()(0))
//first

df.select(first("salary")).show(false)

//last

df.select(last("salary")).show(false)

// stats

df.select(max("salary")).show(false)
df.select(min("salary")).show(false)
df.select(mean("salary")).show(false)
df.select(stddev("salary"), stddev_samp("salary"),
stddev_pop("salary")).show(false)
df.select(sum("salary")).show(false)
df.select(sumDistinct("salary")).show(false)
df.select(variance("salary"),var_samp("salary"), var_pop("salary")).show(false)
```

### 窗口函数
``` scala
import org.apache.spark.sql.functions._
import org.apache.spark.sql.expressions.Window
import spark.implicits._

val simpleData = Seq(("James", "Sales", 3000),
  ("Michael", "Sales", 4600),
  ("Robert", "Sales", 4100),
  ("Maria", "Finance", 3000),
  ("James", "Sales", 3000),
  ("Scott", "Finance", 3300),
  ("Jen", "Finance", 3900),
  ("Jeff", "Marketing", 3000),
  ("Kumar", "Marketing", 2000),
  ("Saif", "Sales", 4100)
)
val df = simpleData.toDF("employee_name", "department", "salary")
df.show()
//row_number

val windowSpec  = Window.partitionBy("department").orderBy("salary")
df.withColumn("row_number",row_number.over(windowSpec)).show()
//rank

df.withColumn("rank",rank().over(windowSpec)).show()
//dens_rank

df.withColumn("dense_rank",dense_rank().over(windowSpec)).show()
//percent_rank

df.withColumn("percent_rank",percent_rank().over(windowSpec)).show()
//ntile

df.withColumn("ntile",ntile(2).over(windowSpec)).show()
//cume_dist

df.withColumn("cume_dist",cume_dist().over(windowSpec)).show()
//lag

df.withColumn("lag",lag("salary",2).over(windowSpec)).show()
//lead

df.withColumn("lead",lead("salary",2).over(windowSpec)).show()

//Aggregate Functions

val windowSpecAgg  = Window.partitionBy("department")
val aggDF = df.withColumn("row",row_number.over(windowSpec))
  .withColumn("avg", avg(col("salary")).over(windowSpecAgg))
  .withColumn("sum", sum(col("salary")).over(windowSpecAgg))
  .withColumn("min", min(col("salary")).over(windowSpecAgg))
  .withColumn("max", max(col("salary")).over(windowSpecAgg))
  .where(col("row")===1).select("department","avg","sum","min","max")
  .show()
```

### 时间函数
``` scala
Seq(("2019-01-23")).toDF("Input").select(
      current_date().as("current_date"),
      col("Input"),
      date_format(col("Input"), "MM-dd-yyyy").as("format")
    ).show()

Seq(1).toDF("seq").select(
    from_unixtime(unix_timestamp()).as("timestamp_1"),
    from_unixtime(unix_timestamp(),"MM-dd-yyyy HH:mm:ss").as("timestamp_2"),
    from_unixtime(unix_timestamp(),"dd-MM-yyyy HH:mm:ss").as("timestamp_3"),
    from_unixtime(unix_timestamp(),"HH:mm:ss").as("timestamp_4")
  ).show()

val df = Seq(("2019-07-01 12:01:19.000","07-01-2019 12:01:19.000", "07-01-2019"))
    .toDF("timestamp_1","timestamp_2","timestamp_3")
    .select(
      unix_timestamp(col("timestamp_1")).as("timestamp_1"),
      unix_timestamp(col("timestamp_2"), "MM-dd-yyyy HH:mm:ss").as("timestamp_2"),
      unix_timestamp(col("timestamp_3"), "MM-dd-yyyy").as("timestamp_3"),
      unix_timestamp().as("timestamp_4")
   )
val df2 = df.select(
    from_unixtime(col("timestamp_1")).as("timestamp_1"),
    from_unixtime(col("timestamp_2"),"MM-dd-yyyy HH:mm:ss").as("timestamp_2"),
    from_unixtime(col("timestamp_3"),"MM-dd-yyyy").as("timestamp_3"),
    from_unixtime(col("timestamp_4")).as("timestamp_4")
  )

import org.apache.spark.sql.functions._
import org.apache.spark.sql.types.{LongType, TimestampType}
val df = Seq(1).toDF("seq").select(
current_timestamp().as("current_time"),
unix_timestamp().as("milliseconds")
)
//Convert milliseconds to timestamp

df.select(
col("milliseconds").cast(TimestampType).as("current_time"),
  col("milliseconds").cast("timestamp").as("current_time2")
).show(false)
//convert timestamp to milliseconds

df.select(
unix_timestamp(col("current_time")).as("unix_milliseconds"),
col("current_time").cast(LongType).as("time_to_milli")
).show(false)

Seq(1).toDF("seq").select(
    current_timestamp().as("current_date"),
    date_format(current_timestamp(),"yyyy MM dd").as("yyyy MM dd"),
    date_format(current_timestamp(),"MM/dd/yyyy hh:mm").as("MM/dd/yyyy"),
    date_format(current_timestamp(),"yyyy MMM dd").as("yyyy MMMM dd"),
    date_format(current_timestamp(),"yyyy MMMM dd E").as("yyyy MMMM dd E")
  ).show(false)     
```

### 处理
``` scala
df.na.drop().show()
df.drop("speed").show()
df.dropDuplicates().show()
```

### 透视 重塑
``` scala
df.groupBy("book_id").pivot("tag_id").agg(round(avg("dummy"))).na.fill(0)
# 性能更好

df.groupBy("book_id","tag_id").sum("dummy").groupBy("book_id").pivot("tag_id").sum("sum(dummy)")

// unpivot

val sourceDf = Seq(
    ("gary",None,   None,   Some(3),None),
    ("mary",None,   None,   None,   Some(3)),
    ("john",Some(3),Some(3),Some(2),Some(2))
).toDF("salesperson", "camera", "large_phone", "notebook", "small_phone")
val unpivotedDf = sourceDf
    .select($"salesperson", expr("stack(4,'camera',camera,'large_phone',large_phone,'notebook',notebook,'small_phone',small_phone) as (device, amount_sold)"))
    .filter($"amount_sold".isNotNull) // must explicitly remove nulls

```

### 数学函数
``` scala
// 方差

df.stat.cov('rand1', 'rand2')
// 相关性

df.stat.corr('id', 'id')
```

### lit 添加新的一列
``` scala
import org.apache.spark.sql.types.IntegerType
import spark.sqlContext.implicits._
import org.apache.spark.sql.functions._
val data = Seq(("111",50000),("222",60000),("333",40000))
val df = data.toDF("EmpId","Salary")
val df2 = df.select(col("EmpId"),col("Salary"),lit("1").as("lit_value1"))
df2.show()
val df3 = df2.withColumn("lit_value2",
  when(col("Salary") >=40000 && col("Salary") <= 50000, lit("100").cast(IntegerType))
    .otherwise(lit("200").cast(IntegerType))
)
df3.show()
```

### withColumn
``` scala
import org.apache.spark.sql.{Row, SparkSession}
import org.apache.spark.sql.types.{ArrayType, IntegerType, MapType, StringType, StructType}
import org.apache.spark.sql.functions._

df.withColumn("age", col("age").cast("Integer"))
df.withColumn("salary", col("salary")*100)
df.withColumn("Country", lit("USA")).withColumn("anotherColumn",lit("anotherValue"))
df1 = df.withColumnRenamed("age", "old_age")
val df2 = df1.withColumn("new_age", df1.col("old_age").cast("int")).drop("old_age")

val df3 = df2.selectExpr("cast(age as int) age", "cast(isGraduated as string) isGraduated", "cast(jobStartDate as string) jobStartDate")
```

### 内嵌的column重命名
``` scala
val schema2 = new StructType().add("fname",StringType).add("middlename",StringType).add("lname",StringType)
# 1

df.select(col("name").cast(schema2), col("dob"), col("gender"),col("salary")).printSchema()
# 2

df.select(col("name.firstname").as("fname"), 
  col("name.middlename").as("mname"),
  col("name.lastname").as("lname"),
  col("dob"),col("gender"),col("salary"))
  .printSchema()
# 3

val df4 = df.withColumn("fname",col("name.firstname"))
      .withColumn("mname",col("name.middlename"))
      .withColumn("lname",col("name.lastname"))
      .drop("name")
# 4

val old_columns = Seq("dob","gender","salary","fname","mname","lname")
val new_columns = Seq("DateOfBirth","Sex","salary","firstName","middleName","lastName")
val columnsList = old_columns.zip(new_columns).map(f=>{col(f._1).as(f._2)})
val df5 = df4.select(columnsList:_*) 
```

### 批量重命名
``` scala
val df = Seq(
  ("1", "a", "c1", "d1"),
  ("2", "b", "c2", "d2"),
  ("3", "c", "c3", "d3")
).toDF("pk1", "pk2", "col1", "col2")
val primaryKeys = Array("pk1", "pk2")
val prefix = "w1."
val renamedColumns = df.columns.map(
  c => if ( primaryKeys contains c ) df(c).as(c) else df(c).as(s"$prefix$c")
)
val dfNew = df.select(renamedColumns: _*)
```

### 条件判断
``` scala
val df2 = df.withColumn("new_gender", when(col("gender") === "M","Male")
	.when(col("gender") === "F","Female")
	.otherwise("Unknown"))

val df4 = df.select(col("*"), when(col("gender") === "M","Male")
      .when(col("gender") === "F","Female")
      .otherwise("Unknown").alias("new_gender"))

val df4 = df.select(col("*"),
      expr("case when gender = 'M' then 'Male' " +
                       "when gender = 'F' then 'Female' " +
                       "else 'Unknown' end").alias("new_gender"))
// || &&

val dataDF = Seq(
      (66, "a", "4"), (67, "a", "0"), (70, "b", "4"), (71, "d", "4"
      )).toDF("id", "code", "amt")
dataDF.withColumn("new_column",
       when(col("code") === "a" || col("code") === "d", "A")
      .when(col("code") === "b" && col("amt") === "4", "B")
      .otherwise("A1"))
      .show()
```

### join
``` scala
val emp = Seq((1,"Smith",-1,"2018",10,"M",3000),
  (2,"Rose",1,"2010",20,"M",4000),
  (3,"Williams",1,"2010",10,"M",1000),
  (4,"Jones",2,"2005",10,"F",2000),
  (5,"Brown",2,"2010",30,"",-1),
  (6,"Brown",2,"2010",50,"",-1)
)
val empColumns = Seq("emp_id","name","superior_emp_id","branch_id","dept_id","gender","salary")
import spark.sqlContext.implicits._
val empDF = emp.toDF(empColumns:_*)
empDF.show(false)
val dept = Seq(("Finance",10,"2018"),
  ("Marketing",20,"2010"),
  ("Marketing",20,"2018"),
  ("Sales",30,"2005"),
  ("Sales",30,"2010"),
  ("IT",50,"2010")
)
val deptColumns = Seq("dept_name","dept_id","branch_id")
val deptDF = dept.toDF(deptColumns:_*)
deptDF.show(false)
//Using multiple columns on join expression

empDF.join(deptDF, empDF("dept_id") === deptDF("dept_id") && empDF("branch_id") === deptDF("branch_id"), "inner").show(false)
//Using Join with multiple columns on where clause

empDF.join(deptDF).where(empDF("dept_id") === deptDF("dept_id") && empDF("branch_id") === deptDF("branch_id")).show(false)
//Using Join with multiple columns on filter clause

empDF.join(deptDF).filter(empDF("dept_id") === deptDF("dept_id") && empDF("branch_id") === deptDF("branch_id")).show(false)

val selfDF = empDF.as("emp1").join(empDF.as("emp2"), col("emp1.superior_emp_id") === col("emp2.emp_id"), "inner")
  .select(col("emp1.emp_id"), col("emp1.name"), col("emp2.emp_id").as("superior_emp_id"), col("emp2.name").as("superior_emp_name"))
selfDF.show(false)
```

### Map、Array explode
``` scala
import spark.implicits._

val arrayData = Seq(
  Row("James",List("Java","Scala"),Map("hair"->"black","eye"->"brown")),
  Row("Michael",List("Spark","Java",null),Map("hair"->"brown","eye"->null)),
  Row("Robert",List("CSharp",""),Map("hair"->"red","skin"->"yellow")),
  Row("Washington",null,null),
  Row("Jefferson",List(),Map())
)
val arraySchema = new StructType().add("name",StringType).add("knownLanguages", ArrayType(StringType)).add("properties", MapType(StringType,StringType))

val df = spark.createDataFrame(spark.sparkContext.parallelize(arrayData),arraySchema)
df.printSchema()
df.show(false)

df.select($"name", explode($"knownLanguages")).show(false)
df.select($"name", explode($"properties")).show(false)
df.select($"name", posexplode($"knownLanguages")).show(false)
df.select($"name",posexplode($"properties")).show(false)
```

### UDF
``` scala
def isAdult(age: Int) = if (age < 18) false else true

val userData = Array(("Leo", 16), ("Marry", 21), ("Jack", 14), ("Tom", 18))
//创建测试df

val userDF = sc.parallelize(userData).toDF("name", "age")
import org.apache.spark.sql.functions._
//注册自定义函数（通过匿名函数）

val strLenUdf = udf((str: String) => str.length())
//注册自定义函数（通过实名函数）

val isAdultUdf = udf(isAdult _)
//通过withColumn添加列

userDF.withColumn("name_len", strLenUdf(col("name"))).withColumn("isAdult", isAdultUdf(col("age"))).show
//通过select添加列

userDF.select(col("*"), strLenUdf(col("name")) as "name_len", isAdultUdf(col("age")) as "isAdult").show
```

### 全部列转double
``` scala
import org.apache.spark.sql.types._
val data = Array(("1", "2", "3", "4", "5"), ("6", "7", "8", "9", "10"))
val df = spark.createDataFrame(data).toDF("col1", "col2", "col3", "col4", "col5")

import org.apache.spark.sql.functions._
df.select(col("col1").cast(DoubleType)).show()

val colNames = df.columns

var df1 = df
for (colName <- colNames) {
  df1 = df1.withColumn(colName, col(colName).cast(DoubleType))
}
df1.show()

val cols = colNames.map(f => col(f).cast(DoubleType))
df.select(cols: _*).show()
val name = "col1,col3,col5"
df.select(name.split(",").map(name => col(name)): _*).show()
df.select(name.split(",").map(name => col(name).cast(DoubleType)): _*).show()
```

### DataFrame foreachPartition
``` scala
dataDF.foreachPartition(partitionOfRecords => {
    val ret = new ListBuffer[UserContReadBook]
    partitionOfRecords.foreach(row => {
        val contRead = UserContReadBook(row.getAs[String]("user_id"), row.getAs[String]("book_id"), row.getAs[Double]("score"))
        ret.append(contRead)
    })
    putToRedis(ret, prefix)
})
```

### 拼接多列
``` scala
df.withColumn("FullName",concat(col("fname"),lit(','), col("mname"),lit(','),col("lname")))

df.withColumn("FullName",concat_ws(",",col("fname"),col("mname"),col("lname"))).show(false)
```

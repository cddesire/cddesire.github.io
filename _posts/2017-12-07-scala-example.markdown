---
layout:     post
title:      "Scala 学习笔记"
date:       2017-12-06 11:00:00
author:     "Daniel"
header-img: "img/post-bg-java.jpg"
tags:
    - Scala

---

> 本笔记部分是根据[Scala Collections 提示和技巧](http://colobu.com/2015/07/02/Scala-Collections-Tips-and-Tricks/)的博客整理的 Scala 知识点。



#### 1、高阶函数
``` scala
case class Email(subject: String, text: String, sender: String, recipient: String)

def complement[A](predicate: A => Boolean) = (a: A) => !predicate(a)

def any[A](predicates: (A => Boolean)*): A => Boolean =
          a => predicates.exists(pred => pred(a))

def none[A](predicates: (A => Boolean)*) = complement(any(predicates: _*))

def every[A](predicates: (A => Boolean)*) = none(predicates.view.map(complement(_)): _*)

type EmailFilter = Email => Boolean

def newMailsForUser(mails: Seq[Email], f: EmailFilter): Seq[Email] = mails.filter(f)

// 产生EmailFilter的工厂方法
val sentByOneOf: Set[String] => EmailFilter =
          senders =>
            email => senders.contains(email.sender)

val notSentByAnyOf: Set[String] => EmailFilter =
        sentByOneOf.andThen(complement(_))

type SizeChecker = Int => Boolean

val minimumSize1: Int => EmailFilter =
          n =>
            email => email.text.size >= n

val sizeConstraint: SizeChecker => EmailFilter =
          f =>
            email => f(email.text.size)

val minimumSize: Int => EmailFilter =
          n =>
            sizeConstraint(_ >= n)

val maximumSize: Int => EmailFilter =
          n =>
            sizeConstraint(_ <= n)
val mails = Email(
        subject = "It's me again, your stalker friend!",
        text = "Hello my friend! How are you?",
        sender = "johndoe@example.com",
        recipient = "me@example.com") :: Nil

val filter: EmailFilter = every(
            notSentByAnyOf(Set("johndoe@example.com")),
            minimumSize(100),
            maximumSize(10000)
          )

newMailsForUser(mails, filter) // returns an empty list
```

#### 2、模式匹配表达式
``` scala
case class Player(name: String, score: Int)
def message(player: Player) = player match {
  case Player(_, score) if score > 100000 => "Get a job, dude!"
  case Player(name, _) => "Hey, $name, nice to see you again!"
}
def printMessage(player: Player) = println(message(player))
```

#### 3、for 语句中的模式
``` scala
def gameResults(): Seq[(String, Int)] =
  ("Daniel", 3500) :: ("Melissa", 13000) :: ("John", 7000) :: Nil
def hallOfFame = for {
    (name, score) <- gameResults()
    if (score > 5000)
  } yield name
```

#### 4、偏函数
``` scala
def wordsWithoutOutliers(wordFrequencies: Seq[(String, Int)]): Seq[String] =
  wordFrequencies.filter { case (_, freq) if freq > 3 && freq < 25 } 
    .map {case (w, _) => w}

// 上面的例子中，先过滤序列后对剩下的元素映射，需要遍历2次序列。
// collect可以将给定的偏函数应用到序列每一个元素，首先检查偏函数在其上面是否有定义，如果没有定义直接忽略，否则将偏函数应用到这个元素上。

def collect[B](pf: PartialFunction[A, B]): Seq[B]

val pf = new PartialFunction[(String, Int), String] {
  def apply(wordFrequency: (String, Int)) = wordFrequency match {
    case (word, freq) if freq > 3 && freq < 25 => word
  }
  def isDefinedAt(wordFrequency: (String, Int)) = wordFrequency match {
    case (word, freq) if freq > 3 && freq < 25 => true
    case _ => false
  }
}

def wordsWithoutOutliers(wordFrequencies: Seq[(String, Int)]): Seq[String] =
  wordFrequencies.collect { case (word, freq) if freq > 3 && freq < 25 => word }

// 偏函数还可以实现函数式的责任链模式  
```

#### 5、判断Seq中检查一个元素是否存在
``` scala
seq.filter(_ == x).headOption != None
seq.find(_ == x) != None
seq.find(_ == x).isDefined
seq.exists(_ == x)
seq.contains(x)
```


#### 6、Sequence 使用
``` scala
// 创建集合

Seq.empty[T]
// 对于数组，优先使用length而不是size

array.length
// 不要对检查empty的属性取反

seq.nonEmpty
seq.isEmpty

seq.lengthCompare(n) > 0
seq.lengthCompare(n) < 0
// 比较数组

array1.sameElements(array2)
// seq 和 set 比较

seq.toSet == set
// 有序集合比较

seq1 == seq2
// 第一个和最后一个元素

seq.head
seq.headOption
seq.last
seq.lastOption
// 下标的range

seq.indices
// 元素是否存在

seq.exists(p)
!seq.exists(p)
// 不要对断言取反

seq.filterNot(p)
// 查找元素

seq.find(p)
// 反序

seq.sorted(Ordering[T].reverse)
seq.sortBy(_.property)(Ordering[T].reverse)
seq.sortWith(!f(_, _))
// 合并多个函数

seq.filter(x => p1(x) && p2(x))
seq.map(f.andThen(g)) // seq.map(f).map(g)


```

#### 7、Option 使用
``` scala
// 不要使用None和Option比较

option.isEmpty
option.isDefined

case class User(id: Int, age: Int, gender: Option[STring])

// Option 模式匹配

val user = User(2, 30, None)
user.gender.getOrElse("not specified")

val gender = user.gender match {
  case Some(gender) => gender
  case None => "not specified"
}

// 不要检查值的存在性再处理值，Option值存在的时候用foreach执行某个副作用
// Some foreach 被调用一次， None不会被调用

user.gender.foreach { v =>
    println(v)
}
// 执行映射

val gender = UserRepository.findById(1).map(_.age) // age is Some(33)
val gender = UserRepository.findById(1).map(_.gender) // gender is Option[Option[String]]
val gender = UserRepository.findById(1).flatMap(_.gender) // gender is Option[String]

option.fold(z)(f) // option.map(f).getOrElse(z)

```

#### 8、Map 使用
``` scala
// filterKeys mapValues

map.filter(p(_._1))  map.map(f(_._2))
// 移除seq中的key

map -- seq
// 赋值

map += x -> y
map1 ++= map2
map -= x
map --= seq
```

#### 9、设置double的位数
``` scala
val formattedVal = "%.6f" format val
formattedVal.toDouble
```

#### 10、类型转化
``` scala
def toInt(s: String): Option[Int] = {
  try {
    Some(s.toInt)
  } catch {
    case e: Exception => None
  }
}

import scala.util.{Try, Success, Failure}
def makeInt(s: String): Try[Int] = Try(s.trim.toInt)
```

#### 11、不可变数组和可变数组
``` scala
val fruits = Array("Apple", "Banana", "Orange")
val fruits = new Array[String](3)
fruits(0) = "Apple"
fruits(1) = "Banana"
fruits(2) = "Orange"

import scala.collection.mutable.ArrayBuffer
import scala.collection.mutable.ArrayBuilder

val fruits = ArrayBuffer[String]()
fruits += "Apple"
fruits += "Banana"
fruits.toArray()

val fruits = ArrayBuilder.make[String]
fruits += "Apple"
fruits += "Banana"
fruits.result()

val fruits = new ListBuffer[String]
fruits.result()
```

#### 12、foreach循环
``` scala
var sum = 0
val x = List(1,2,3)
x.foreach(sum += _)

val names = Vector("Bob", "Fred", "Joe", "Julia", "Kim")
for (name <- names if name.startsWith("J"))
    println(name)

val m1 = Map("fname" -> "Al", "lname" -> "Alexander")
for ((k,v) <- m1) printf("key: %s, value: %s\n", k, v)
m1 foreach (x => println (x._1 + "-->" + x._2))
m1 foreach {case (key, value) => println (key + "-->" + value)}    
```

#### 13、数组分片
``` scala
// def sliding(size: Int, step: Int): Iterator[Iterable][A]]

val nums = (1 to 5).toArray
nums.sliding(2).toList
nums.sliding(2,2).toList
nums.sliding(2,3).toList

// 当size和step相等的时候，可以简化使用grouped方法
nums.grouped(2).toList

val x = List(15, 10, 5, 8, 20, 12)
val (a, b) = x.partition(_ > 10)
val groups = x.groupBy(_ > 10)
```

#### 14、Future
``` scala
import scala.concurrent.{Await, Future}
import scala.util.{Failure, Success}
import scala.concurrent.ExecutionContext.Implicits.global
import scala.concurrent.duration.Duration

def concurrentCount(df1: DataFrame, df2: DataFrame): Future[(Long, Long)] = {
    val count1Future: Future[Long] = Future {
        df1.count()
    }
    val count2Future: Future[Long] = Future {
        df2.count()
    }
    val count: Future[(Long, Long)] = for {
        c1 <- count1Future
        c2 <- count2Future
    } yield (c1, c2)
    count
}

val count: Future[(Long, Long)] = concurrentCount(positiveDF, negativeDF)
val (posCount, negCount) = Await.result(count, Duration.Inf)

val count = concurrentCount(trainDF, validateDF)
count.onComplete {
    case Success((trainCount, validateCount)) => println(s"train sample count: $trainCount validate sample count: $validateCount")
    case Failure(ex) => println(s"train sample count error")
}
```

#### 15、异步事件处理
``` scala
import java.util.Timer
import java.util.TimerTask
import scala.concurrent.{Await, Future}
import scala.util.{Failure, Success}
import scala.concurrent.ExecutionContext.Implicits.global
import scala.concurrent.duration.Duration
 
val timer = new Timer

def delayedSuccess[T](secs: Int, value: T): Future[T] = {
    val result = Promise[T]
    timer.schedule(new TimerTask() {
        def run() = {
            result.success(value)
        }
    }, secs * 1000)
    result.future
}

def delayedFailure(secs: Int, msg: String): Future[Int] = {
    val result = Promise[Int]
    timer.schedule(new TimerTask() {
        def run() = {
            result.failure(new IllegalArgumentException(msg))
        }
    }, secs * 1000)
    result.future
}

def task1(input: Int) = TimedEvent.delayedSuccess(1, input + 1)
def task2(input: Int) = TimedEvent.delayedSuccess(2, input + 2)
def task3(input: Int) = TimedEvent.delayedSuccess(3, input + 3)
def task4(input: Int) = TimedEvent.delayedSuccess(1, input + 4)

// 阻塞等待任务执行

def runBlocking() = {
    val v1 = Await.result(task1(1), Duration.Inf)
    val future2 = task2(v1)
    val future3 = task3(v1)
    val v2 = Await.result(future2, Duration.Inf)
    val v3 = Await.result(future3, Duration.Inf)
    val v4 = Await.result(task4(v2 + v3), Duration.Inf)
    val result = Promise[Int]
    result.success(v4)
    result.future
}

// 使用 onSuccess() 处理事件的完成

def runOnSuccess() = {
    val result = Promise[Int]
    task1(1).onSuccess(v => v match {
        case v1 => {
            val a = task2(v1)
            val b = task3(v1)
            a.onSuccess(v => v match {
                case v2 =>
                    b.onSuccess(v => v match {
                        case v3 => task4(v2 + v3).onSuccess(v4 => v4 match {
                            case x => result.success(x)
                })
                })
            })
        }
    })
    result.future
}

// 使用 flatMap() 处理事件的完成

def runFlatMap() = {
    task1(1) flatMap {v1 =>
        val a = task2(v1)
        val b = task3(v1)
        a flatMap { v2 =>
            b flatMap { 
                v3 => task4(v2 + v3) 
            }
        }
    }
}

```



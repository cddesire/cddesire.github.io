---
layout:     post
title:      "Java 必要代码片段"
date:       2017-05-13 17:30:00
author:     "Daniel"
header-img: "img/post-bg-java.jpg"
tags:
    - Java

---


#### 0、包装类型比较使用equals，而不是==
``` java
Long a = 11L;
Long b = 11L;
a.equals(b);
int result = Long.compare(a, b)
a>b result>0
a=b result=0
a<b result<0
```


####	1、得到当前的方法名
``` java
String methodName = Thread.currentThread().getStackTrace()[1].getMethodName();
```

####  2、打印时间
``` java
/* Given elaspedTime in ms, return a printable string */
public static String time2Str(long elapsedTime) {
    String unit;
    double time = elapsedTime;
    if (elapsedTime < 1000) {
      unit = "milliseconds";
    } else if (elapsedTime < 60 * 1000) {
      unit = "seconds";
      time = time / 1000;
    } else if (elapsedTime < 3600 * 1000) {
      unit = "minutes";
      time = time / (60 * 1000);
    } else {
      unit = "hours";
      time = time / (3600 * 1000);
    }
    return time + " " + unit;
}
```

#### 3、改变数组大小
``` java
private static Object resizeArray(Object oldArray, int newSize) {  
   int oldSize = Array.getLength(oldArray);  
   Class elementType = oldArray.getClass().getComponentType();  
   Object newArray = java.lang.reflect.Array.newInstance(  
         elementType,newSize);  
   int preserveLength = Math.min(oldSize,newSize);  
   if (preserveLength > 0)  
      System.arraycopy(oldArray, 0, newArray, 0, preserveLength);  
   return newArray;  
}  
```

#### 4、产生随机数
``` java
random.nextInt(max) % (max - min + 1) + min

// nextInt is normally exclusive of the top value,
// so add 1 to make it inclusive
import java.util.concurrent.ThreadLocalRandom;
int randomNum = ThreadLocalRandom.current().nextInt(min, max + 1);
// java8
int randomNumber = random.ints(1, min, max + 1).findFirst().getAsInt();

import org.apache.commons.math3.random.RandomDataGenerator
new RandomDataGenerator().nextInt(min, max);

import org.apache.commons.math.random.RandomData;
import org.apache.commons.math.random.RandomDataImpl;
RandomData randomData = new RandomDataImpl();
int number = randomData.nextInt(min, max);
```

#### 5、测试两个List相同
``` java
import static org.junit.Assert.*;
import static org.hamcrest.CoreMatchers.*;
@Test
public void test_array_pass()
{
  List<String> actual = Arrays.asList("fee", "fi", "foe");
  List<String> expected = Arrays.asList("fee", "fi", "foe");
  assertThat(actual, is(expected));
  assertThat(actual, is(not(expected)));
}
```

#### 6、数组类型转换
``` java
// 原生数组转换对象数组
int[] arr = someAPI.getSomeInts();
Integer[] virtualType = ArrayUtils.toObject(arr);
List<Integer> inputAsList = Arrays.asList(virtualType);
List<String> orderIdList = Lists.newArrayList(virtualType);
List<Integer> aList = Ints.asList(arr);

// List 转换为原生数组
int[] arr = list.stream().mapToInt(Integer::intValue).toArray();
int[] arr = ArrayUtils.toPrimitive(list.toArray(new Integer[0]))
int[] values = Ints.toArray(list);

// 字符数组转换long数组
Long[] longArrays = (Long[]) ConvertUtils.convert(stringArrays, Long[].class);
long[] longArrays = (long[]) ConvertUtils.convert(stringArrays, Long.TYPE);

// List转换数组 
String[] orderIdArr = orderIdList.toArray(new String[orderIdList.size()]);
String[] orderIdArr = orderIdList.stream().toArray(String[]::new);

// 或者更通用的方式
public static <T> T[] toArray(Class<T> c, List<T> list) {
  @SuppressWarnings("unchecked")
  T[] ta= (T[])Array.newInstance(c, list.size());

  for (int i= 0; i<list.size(); i++)
    ta[i]= list.get(i);
  return ta;
}

public static <T> T[] toArray(List<T> list) {
  @SuppressWarnings("unchecked")
  Class<T> clazz = (Class<T>)list.get(0).getClass();
  return toArray(clazz, list);
}
``` 

#### 7、BitSet与String类型转换
``` java
private static BitSet fromString(final String s) {		
    return BitSet.valueOf(new long[] { Long.parseLong(s, 2) });
}
private static String toString(BitSet bs) {
   return Long.toString(bs.toLongArray()[0], 2);
}
```

#### 8、String转换为时间
``` java
new SimpleDateFormat("yyyy-MM-dd HH:mm:SS").parse(dateStr);
```

#### 9、时间格式化
``` java
Date today = Calendar.getInstance().getTime(); 
// 时间格式化为字符串
DateFormatUtils.format(today, "yyyy-MM-dd HH:mm:SS");
```

#### 10、打印Map
``` java
org.apache.commons.collections.MapUtils.debugPrint(System.out, "Print this", myMap);
System.out.println(StringUtils.join(resultMap.entrySet().iterator(), "\n"));
```

#### 11、 Spring测试
``` java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(value = { "/spring/applicationContext-dal.xml"})
@FixMethodOrder(MethodSorters.NAME_ASCENDING)
```

#### 12、BeanUtils 
``` java
// Apache的BeanUtils和PropertyUtils
BeanUtils.copyProperties(toBean, frombean);
PropertyUtils.copyProperties(toBean, frombean);
// Spring的BeanUtils
BeanUtils.copyProperties(frombean, toBean);
// Cglib的BeanCopier
BeanCopier bc = BeanCopier.create(FromBean.class, ToBean.class, false);
bc.copy(frombean, toBean, null);
```

#### 13、object toString
``` java
ToStringBuilder.reflectionToString(lquery)
```

#### 14、 设置bean属性的值
``` java
Field field = ReflectionUtils.findField(entity.getClass(), beanName);
ReflectionUtils.makeAccessible(field);
ReflectionUtils.setField(field, entity, values);

// access properties as Map
Map<String, Object> properties = BeanUtils.describe(entity);
properties.set("name","Fred");
BeanUtils.populate(entity, properties);

// access individual properties
String oldname = BeanUtils.getProperty(entity,"name");
BeanUtils.setProperty(entity,"name","Barny"); 

// BeanWrapper
import org.springframework.beans.BeanWrapper;
import org.springframework.beans.PropertyAccessorFactory;
Field[] fields = ItemBean.class.getDeclaredFields();
// 性能比Apache common高4-5倍
BeanWrapper wrapper = PropertyAccessorFactory.forBeanPropertyAccess(item);
for (Field f: fields) {
    FeatureSchema anno = f.getAnnotation(FeatureSchema.class);
    System.out.println(f.getName() + "--" + f.getType() + "---" + anno.name() + "---" + anno.type() + "---" + anno.struct());
    Object value = wrapper.getPropertyValue(fieldName);
}
```

#### 15、不同创建对象的方式
``` java
// 1
PersonObj object = new PersonObj();
// 2
PersonObj object2 = (PersonObj) Class.forName("com.tutorial.PersonObj").newInstance();
// 3
PersonObj secondObject = new PersonObj();
PersonObj object3 = (PersonObj) secondObject.clone();
// 4
Object object4 = PersonObj.class.getClassLoader().loadClass("com.tutorial.PersonObj").newInstance();
// 5
Class clazz = PersonObj.class;
Constructor personCon = clazz.getDeclaredConstructors()[0];
PersonObj obj = (PersonObj) personCon.newInstance();
```

#### 16、 MathUtil
``` java
// check if a String contains only digits
NumberUtils.isDigits("123.123"); // false
// check if a string is a valid number
NumberUtils.isNumber("123.123"); // true

int maxVal = 100;
RandomUtils.nextInt(maxVal);

double[] values = new double[]{2.3, 5.4, 6.2, 7.3, 23.3};
StatUtils.min(values);
StatUtils.max(values);
StatUtils.mean(values);
StatUtils.product(values);
StatUtils.sum(values);
StatUtils.variance(values);
```

#### 17、线程池创建
``` java
public final class CustomThreadPoolExecutor extends ThreadPoolExecutor {
    private static final Logger log = LoggerFactory.getLogger(CustomThreadPoolExecutor.class);

    public CustomThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue);
    }

    public CustomThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory);
    }

    public CustomThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, RejectedExecutionHandler handler) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, handler);
    }

    public CustomThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory, handler);
    }

    @Override
    protected void beforeExecute(Thread t, Runnable r) {
        if (log.isDebugEnabled()) {
            log.debug("beforeExecute in thread: {}, runnable type: {}.", Thread.currentThread().getName(), r.getClass().getName());
        }
    }

    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        super.afterExecute(r, t);
        this.logThrowableAfterExecute(r, t);
    }

    private void logThrowableAfterExecute(Runnable r, Throwable t) {
        if (log.isDebugEnabled()) {
            log.debug("afterExecute in thread: {}, runnable type: {}.", Thread.currentThread().getName(), r.getClass().getName());
        }

        if (t == null && r instanceof Future<?>) {
            try {
                ((Future<?>) r).get();
            } catch (ExecutionException ee) {
                log.warn("Execution exception when running task in {}", Thread.currentThread().getName());
                t = ee.getCause();
            } catch (InterruptedException ie) {
                log.warn("Thread ({}) interrupted: {}", Thread.currentThread(), ie);
                Thread.currentThread().interrupt();
            } catch (Throwable throwable) {
                t = throwable;
            }
        }

        if (t != null) {
            log.warn("Caught exception in thread {} : {}", Thread.currentThread().getName(), t);
        }
    }
}

public final class CustomExecutors {
    private static final Logger log = LoggerFactory.getLogger(CustomExecutors.class);

    private final AtomicInteger discardThreadCount = new AtomicInteger(0);

    private final ThreadFactory threadFactory  = new ThreadFactoryBuilder().setNameFormat("custom-thread-pool-%d").setUncaughtExceptionHandler(new UncaughtExceptionHandlerImpl()).build();

    private final class UncaughtExceptionHandlerImpl implements Thread.UncaughtExceptionHandler {
        @Override
        public void uncaughtException(Thread t, Throwable e) {
            log.error("Thread {} throw UncaughtException: {}", t.getName(), e.getMessage());
        }
    }

    private final class RejectedExecutionHandlerImpl implements RejectedExecutionHandler {
        @Override
        public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
            log.warn("Thread {} is rejected, now discard count: {}", r.getClass().getName(), discardThreadCount.incrementAndGet());
        }
    }

    private final static int  CORE_POOL_SIZE  = 32;
    private final static int  MAX_POOL_SIZE   = 128;
    private final static int  WORK_QUEUE_SIZE = 512;
    private final static int KEEP_ALIVE_TIME = 30;

    private final ExecutorService customThreadPoolExecutor = new CustomThreadPoolExecutor(CORE_POOL_SIZE, MAX_POOL_SIZE,
            KEEP_ALIVE_TIME, TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(WORK_QUEUE_SIZE),
            threadFactory, new RejectedExecutionHandlerImpl());

    class AsynWriteTask implements Runnable {
        private UserEnv userEnv;
        public AsynWriteTask(UserEnv userEnv) {
            this.userEnv = userEnv;
        }
        private String getStatefulThreadName() {
            StringBuilder sb = new StringBuilder();
            sb.append(", AppName: ").append(userEnv.getAppName()).append(", StartTime: ")
                    .append(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
            return sb.toString();
        }

        @Override
        public void run() {
            final Thread currentThread = Thread.currentThread();
            final String oldName = currentThread.getName();
            final String statefulName = getStatefulThreadName();
            currentThread.setName(statefulName + "-" + oldName);
            try {
                putTask(userEnv, appName);
            } finally {
                currentThread.setName(oldName);
            }
        }
    }

    // 使用ListenableFuture添加回调
    private final ListeningExecutorService listeningExecutorService = MoreExecutors.listeningDecorator(
            new CustomThreadPoolExecutor(CORE_POOL_SIZE, MAX_POOL_SIZE, KEEP_ALIVE_TIME, TimeUnit.SECONDS,
                    new ArrayBlockingQueue<Runnable>(WORK_QUEUE_SIZE), threadFactory));

    ListenableFuture<List<InfoTO>> futureTaskResult = listeningExecutorService.submit(new BatchQueryInfoTask(subOrderArr));
    Futures.addCallback(futureTaskResult, new FutureCallback<List<InfoTO>>() {
        @Override
        public void onSuccess(@Nullable List<InfoTO> result) {
            logger.info("Query order info callback success: {}", result);
        }
        @Override
        public void onFailure(Throwable t) {
            logger.error("Query order info callback error: {}", t.getMessage(), t);
        }
    });
}

```

#### 18、复写对象的equals和hashCode方法
``` java
public class User {
    private String name;
    private int age;
    private String passport;
	//getters and setters, constructor
     @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof User)) {
            return false;
        }
        User user = (User) o;
        return new EqualsBuilder()
                .append(age, user.age)
                .append(name, user.name)
                .append(passport, user.passport)
                .isEquals();
    }

    @Override
    public int hashCode() {
        return new HashCodeBuilder(17, 37)
                .append(name)
                .append(age)
                .append(passport)
                .toHashCode();
    }
}

@Override
public int compareTo(@Nonnull User o) {
    return new CompareToBuilder()
            .append(o.age, this.age)
            .append(o.name, this.name)
            .toComparison();
    }
```

#### 19、截取原有集合的部分数据
``` java
Set<Integer> subset = ImmutableSet.copyOf(Iterables.limit(set, 20));
// 获取第一个元素
String first = set.stream().findFirst().get();
String first = set.iterator().next();
// 获取最后一个
String lastElement = Iterables.getLast(set, null);
```

#### 20、数组查找元素索引
``` java
// 对象数组
String[] lilyFlowers = {   
              "Lily of the valley",
              "Lily Elite",
              "Lily Monte Negro",
              "Lily Casa Blanca",
              "Lily of the Nile – Alba",
              "Lily Stargazer"};

int indexOfFlower = Iterators.indexOf(Iterators.forArray(lilyFlowers), new Predicate<String>() {
    public boolean apply(String input) {
        return input.equals("Lily Elite");
    }
});
int indexOfFlower = ArrayUtils.indexOf(lilyFlowers, "Lily Elite");
// 原生数组
int [] twoQuarters = {1, 2, 3, 4, 5, 6};
int lastMonthInFirstQuarter = Ints.indexOf(twoQuarters, 3);
int lastMonthInFirstQuarter = ArrayUtils.indexOf(twoQuarters, 3);
```

#### 21、数组中元素是否存在元素
``` java
Integer[] vikQueensLosingSeasons = {
            1962, 1967, 1984, 2011, 1966,
            1963, 1982, 2001, 1990, 2002,
            2006, 2010, 1965, 1972, 1979,
            1981, 1985};
boolean hadALosingSeason = Arrays
            .asList(vikQueensLosingSeasons)
            .contains(new Integer(1962));
boolean yearExists = Ints.contains(vikQueensLosingSeasons, 1972);
Optional<Integer> contains = Iterators.tryFind(
            Iterators.forArray(vikQueensLosingSeasons),
            new Predicate<Integer>() {
                public boolean apply(Integer input) {
                    if (input == 1962) {
                        return true;
                    } else {
                        return false;
                    }
                }
            });
boolean losingSeason = ArrayUtils.contains(vikQueensLosingSeasons, 1962);
boolean contains = IntStream.of(vikQueensLosingSeasons).anyMatch(x -> x == 1962);
```

#### 22、根据范围产生数据
``` java
Range<Integer> range = Range.closed(20, 30);
Set<Integer> ranges = ContiguousSet.create(range,
            DiscreteDomain.integers());
```

#### 23、合并字节数组
``` java
// 1 Use a ByteBuffer
ByteBuffer target = ByteBuffer.wrap(bigByteArray);
target.put(small1);
target.put(small2);

// 2 System.arraycopy
public static void copySmallArraysToBigArray(final byte[][] smallArrays,
    final byte[] bigArray){
    int currentOffset = 0;
    for(final byte[] currentArray : smallArrays){
        System.arraycopy(currentArray, 0, bigArray, currentOffset, currentArray.length);
        currentOffset += currentArray.length;
    }
}

// 其他类型
public static <T> T[] concat(T[] first, T[] second) {
    T[] result = Arrays.copyOf(first, first.length + second.length);
    System.arraycopy(second, 0, result, first.length, second.length);
    return result;
}

public static <T> T[] concatAll(T[] first, T[]... rest) {
    int totalLength = first.length;
    for (T[] array : rest) {
        totalLength += array.length;
    }
    T[] result = Arrays.copyOf(first, totalLength);
    int offset = first.length;
    for (T[] array : rest) {
        System.arraycopy(array, 0, result, offset, array.length);
        offset += array.length;
    }
    return result;
}

// 3 ByteArrayOutputStream
public byte[] concatenateByteArrays(List<byte[]> blocks) {
    ByteArrayOutputStream os = new ByteArrayOutputStream();
    for (byte[] b : blocks) {
        os.write(b, 0, b.length);
    }
    return os.toByteArray();
}

// 4、ArrayUtils
Byte[] both = (Byte[])ArrayUtils.addAll(first, second);
// java8
String[] both = Stream.concat(Arrays.stream(a), Arrays.stream(b))
                      .toArray(String[]::new);
String[] both = Stream.of(a, b).flatMap(Stream::of)
                      .toArray(String[]::new);
// Guava ObjectArrays
String[] both = ObjectArrays.concat(first, second, String.class);
Bytes.concat(first, second)
Ints.concat(first, second)
```

#### 24、加密
``` java
// MD5 加密
byte[] encrypt(byte[] obj) {
  MessageDigest md5 =  MessageDigest.getInstance("MD5");
  md5.update(obj);
  return  md5.digest();
}
// SHA  加密
byte[] encrypt(byte[] obj) {
  MessageDigest sha =  MessageDigest.getInstance("SHA");
  sha.update(obj);
  return  sha.digest();
}
// guava
Hashing.md5().hashBytes(input.getBytes()).toString();
Hashing.sha256().hashBytes(input.getBytes()).toString();
Hashing.sha512().hashBytes(input.getBytes()).toString();
Hashing.crc32().hashBytes(input.getBytes()).toString();

```

#### 25、时间工具
``` java
// 时间段
Date end = new Date(new Date());
Date start = new DateTime(end).minusDays(90).toDate();
// 年月日时分秒生成
DateTime dt = new DateTime(2016, 10, 20, 13, 14, 0, 0); 
// ISO8601形式生成  
DateTime dt = new DateTime("2012-05-20");  
DateTime dt = new DateTime("2012-05-20T13:14:00");
DateTime.parse("2012-05-20 13:14:00", DateTimeFormat.forPattern("yyyy-MM-dd HH:mm:SS"))
// 星期
dt.getDayOfWeek()
DateTimeConstants.SUNDAY DateTimeConstants.MONDAY ...
// 月末日期    
DateTime lastday = dt.dayOfMonth().withMaximumValue();    
// 90天后那周的周一  
DateTime firstday = dt.plusDays(90).dayOfWeek().withMinimumValue();
DateTime begin = new DateTime("2012-02-01");  
DateTime end = new DateTime("2012-05-01");   
// 计算区间毫秒数  
Duration d = new Duration(begin, end);  
long time = d.getMillis();   
// 计算区间天数  
Period p = new Period(begin, end, PeriodType.days());  
int days = p.getDays();  
// 计算特定日期是否在该区间内  
Interval i = new Interval(begin, end);  
boolean contained = i.contains(new DateTime("2012-03-01")); 
// 日期比较 
begin.isAfterNow();
begin.isAfter(end);
// 格式化输出 
dateTime.toString("yyyy-MM-dd HH:mm:ss");
```

#### 26、获取枚举对象
``` java
public enum CheckType {
    COMMON_TYPE(1),
    UNFOUND(-1);

    private CheckType(Integer type) {
        // for getEmumByType2
        this.type = type;
        // for getEmumByType1
        Holder.MAP.put(type, this);
    }

    private Integer type;

    public Integer getType() {
        return type;
    }

    public void setType() {
        this.type = type;
    }

    private static class Holder {
        static Map<Integer, CheckType> MAP = Maps.newHashMap();
    }

    public static CheckType getEmumByType1(@Nullable final Integer type) {
        CheckType checkType = Holder.MAP.get(type);
        return Optional.fromNullable(checkType).or(UNFOUND);
    }

    public static CheckType getEmumByType2(@Nullable final Integer type) {
        return EnumSet.allOf(CheckType.class)
                .stream()
                .filter(t -> type.equals(t.getType()))
                .findFirst()
                .orElse(UNFOUND);
    }

    public static CheckType getEnumByName(@Nullable final String enumName) {
        CheckType checkType = EnumUtils.getEnum(CheckType.class, enumName);
        return Optional.fromNullable(checkType).or(UNFOUND);
    }
}
```
#### 27、flat嵌套的集合
``` java
// Iterable<? extends Iterable<? extends T>>
Map<String, Set<String>> maps = mock();
Iterable<String> values =  Iterables.concat(maps.values());
```

#### 28、时间截取
``` java
Date date = new Date();
DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
dateFormat.format(date);// 2016-11-30 09:45:49
dateFormat.format(DateUtils.truncate(date, Calendar.DATE)); // 2016-11-30 00:00:00
dateFormat.format(DateUtils.truncate(date, Calendar.HOUR_OF_DAY)); // 2016-11-30 09:00:00
dateFormat.format(DateUtils.truncate(date, Calendar.MINUTE)); // 2016-11-30 09:45:00
dateFormat.format(DateUtils.truncate(date, Calendar.SECOND)); // 2016-11-30 09:45:49
dateFormat.format(DateUtils.truncate(date, Calendar.DAY_OF_MONTH)); //2016-11-30 00:00:00
dateFormat.format(DateUtils.truncate(date, Calendar.MONTH)); // 2016-11-01 00:00:00
```

#### 29、List切分partition
``` java
// 1 Lists.partition
List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
List<List<Integer>> subSets = Lists.partition(intList, 3);
// 2 Iterables.partition
Collection<Integer> intCollection = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
Iterable<List<Integer>> subSets = Iterables.partition(intCollection, 3);
// 3 java8 groupingBy
List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
Map<Boolean, List<Integer>> groups = intList.stream().collect(Collectors.partitioningBy(s -> s > 6));
Map<Integer, List<Integer>> groups = intList.stream().collect(Collectors.groupingBy(s -> (s - 1) / 3));      
List<List<Integer>> subSets = new ArrayList<List<Integer>>(groups.values());
```

#### 30、两个List比较Deletions、Additions
``` java
List<T> oldList = ...
List<T> newList= ...

List<T> removed = new ArrayList<T>(oldList);
removed.removeAll(newList);

List<T> same = new ArrayList<T>(oldList);
same.retainAll(newList);

List<T> added = new ArrayList<T>(newList);
added.removeAll(oldList);
```

#### 31、安全转换范型
``` java
List<? extends Foo> list1 = ...
List<Foo> list2 = Collections.unmodifiableList(list1);

@SupressWarnings("unchecked")
List<Foo> list2 = (List<Foo>)(List<?>)list1; 

List<? super Foo> list3 = list2;
```

#### 32、Get-and-Put principle([ Producer Extends, Consumer Super](http://stackoverflow.com/questions/2723397/what-is-pecs-producer-extends-consumer-super/2723538#2723538))
``` java
class CopyClass {
  <T> void copy(List<? extends T> from, List<? super T> to) {
    for(T item : from) to.add(item);
  }
}
```

#### 33、JSON解析（fastjson）
``` java
// 解析map
final static Type type = new TypeReference<Map<String, Long>>() {}.getType();
Map<String, Long> timeMap = JSON.parseObject(configInfo, type);

// 解析List
final static Type type = new TypeReference<List<CategoryBean>>() {}.getType();
List<CategoryBean> categoryList = JSON.parseObject(categoryListJSON, type);
// 或者
List<CategoryBean> categoryList = JSON.parseArray(categoryListJSON, CategoryBean.class);

// 解析自定义对象
Dict dict = JSON.parseObject(value, Dict.class);
// 解析成JSONObject
JSONObject config = JSON.parseObject(configJsonString);
String username = config.getString("username");

// 关闭循环引用检测
JSON.toJSONString(obj, SerializerFeature.DisableCircularReferenceDetect)
Dict dict = JSON.parseObject("...", Dict.class, Feature.DisableCircularReferenceDetect);

// JSONObject遍历
jObject = new JSONObject(contents.trim());
Iterator<?> keys = jObject.keys();
while( keys.hasNext() ) {
    String key = (String)keys.next();
    if ( jObject.get(key) instanceof JSONObject ) {

    }
}

// 超大JSON处理
https://github.com/alibaba/fastjson/wiki/Stream-api

// 格式化JSON输出
String text = JSON.toJSONString(dict, SerializerFeature.PrettyFormat);

// 将非字符串类型输出为字符串类型，如将Integr输出为String
String text = JSON.toJSONString(dict, SerializerFeature.WriteNonStringValueAsString)

// 写入OutputStream或者Writer
JSON.writeJSONString(os, dict);
JSON.writeJSONString(writer, dict);

// BeanToArray 特性
class Company {
    public int code;

    @JSONField(serialzeFeatures=SerializerFeature.BeanToArray, parseFeatures=Feature.SupportArrayToBean)
    public List<Department> departments = new ArrayList<Department>();
}

```

#### 34、Java Bean 之间转换
[MapStruct](http://www.tianshouzhi.com/api/tutorials/mapstruct)
[MapStruct : Transferring data from one bean to another](https://www.javacodegeeks.com/2016/12/mapstruct-transferring-data-one-bean-another.html)
``` java
// maven引入
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.2.0.Final</version>
</dependency>
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct-processor</artifactId>
    <version>1.2.0.Final</version>
</dependency>

@Mapper(unmappedTargetPolicy = ReportingPolicy.IGNORE)
@DecoratedWith(SampleMapperDecorator.class)
public interface SampleMapper {
    SampleMapper INSTANCE = Mappers.getMapper(SampleMapper.class);
    @Mappings({
        @Mapping(source = "riskTagCode", target = "riskCode"),
        @Mapping(expression = "java(getParam.getObjectType().getCode())", target = "objectType"),
        @Mapping(source = "sampleId", target = "id")
    })
    SampleBean paramToBean(SampleGetParam getParam);

}

public abstract class SampleMapperDecorator implements SampleMapper {
    private final SampleMapper delegate;
    public SampleMapperDecorator(SampleMapper delegate) {
        this.delegate = delegate;
    }
    @Override
    public SampleBean paramToBean(SampleGetParam getParam) {
        SampleBean SampleBean = delegate.paramToBean(getParam);
        Date endTime = Optional.ofNullable(getParam.getEndTime()).orElse(new Date());
        Date startTime = Optional.ofNullable(getParam.getStartTime()).orElse(new DateTime(endTime).minusDays(7).toDate());
        SampleBean.setEndTime(endTime);
        SampleBean.setStartTime(startTime);
        return SampleBean;
    }
}
```

#### 35、创建空数组
``` java 
Long[] children = ArrayUtils.EMPTY_LONG_OBJECT_ARRAY;
Long[] children = {};
Long[] children = ArrayUtils.toArray();
```

#### 36、Shuffle  Array
``` java
void shuffleArray(String[] nodes) {
    Random rnd = new Random()
    for (int i = nodes.length; i > 1; i--) {
      int randomIndex = rnd.nextInt(i);
      String tmp = nodes[randomIndex];
      nodes[randomIndex] = nodes[i - 1];
      nodes[i - 1] = tmp;
    }
}

Collections.shuffle(Lists.newArrayList(1,2,3));
```

#### 37、流之间复制
``` java
/**
 * Copies the specified length of bytes from in to out.
 *
 * @param in InputStream to read from
 * @param out OutputStream to write to
 * @param length number of bytes to copy
 * @param bufferSize the size of the buffer 
 * @param close whether to close the streams
 * @throws IOException if bytes can not be read or written
 */
public static void copyBytes(InputStream in, OutputStream out, final long length, final int bufferSize, final boolean close) throws IOException {
  final byte buf[] = new byte[bufferSize];
  try {
    int n = 0;
    for(long remaining = length; remaining > 0 && n != -1; remaining -= n) {
      final int toRead = remaining < buf.length ? (int)remaining : buf.length;
      n = in.read(buf, 0, toRead);
      if (n > 0) {
        out.write(buf, 0, n);
      }
    }

    if (close) {
      out.close();
      out = null;
      in.close();
      in = null;
    }
  } finally {
    if (close) {
      closeStream(out);
      closeStream(in);
    }
  }
}

private static void closeStream(java.io.Closeable... closeables) {
  for(java.io.Closeable c : closeables) {
    if (c != null) {
      try {
        c.close();
      } catch(IOException e) {
          log.debug("Exception in closing " + c, e);
      }
    }
  }
}
```

#### 38、byte数组和Long数组转换
``` java
public static long[] toLongArray(byte[] bArray) {
  ByteBuffer byteBuffer = ByteBuffer.wrap(bArray);
  LongBuffer longBuffer = byteBuffer.asLongBuffer();
  long l[] = new long[longBuffer.capacity()];
  longBuffer.get(l);
  return l;
}

public static byte[] toByteArray(long[] data) {
  ByteBuffer byteBuffer = ByteBuffer.allocate(data.length * 8);
  LongBuffer longBuffer = byteBuffer.asLongBuffer();
  longBuffer.put(data);
  return byteBuffer.array();
}
```

#### 39、取绝对值
``` java
/**
 * @param x
 * @return the absolute value of x
 * @throws ArithmeticException when a negative value would have been returned by {@link Math#abs(int)}
 */
public static int abs(int x) throws ArithmeticException {
    if (x == Integer.MIN_VALUE) {
        throw new ArithmeticException("Math.abs(Integer.MIN_VALUE)");
    }
    return Math.abs(x);
}
```

#### 40、字节数组与16进制字符串转换
``` java
// 00A0BF  <=>  byte[] {0x00,0xA0,0xBf}
/**
 * byte array to Hex String
 * @param ba
 * @return string with HEX value of the key
 */
public static String toHex(byte[] ba) {
  ByteArrayOutputStream baos = new ByteArrayOutputStream();
  PrintStream ps = new PrintStream(baos);
  for(byte b: ba) {
    ps.printf("%x", b);
  }
  return baos.toString();
}
 
public static byte[] hexToByteArray(String s) {
    int len = s.length();
    byte[] data = new byte[len / 2];
    for (int i = 0; i < len; i += 2) {
        data[i / 2] = (byte) ((Character.digit(s.charAt(i), 16) << 4) + Character.digit(s.charAt(i+1), 16));
    }
    return data;
}

import javax.xml.bind.DatatypeConverter;
public static String toHexString(byte[] array) {
    return DatatypeConverter.printHexBinary(array);
}
public static byte[] toByteArray(String s) {
    return DatatypeConverter.parseHexBinary(s);
}

import com.google.common.io.BaseEncoding;
BaseEncoding.base16().decode(string);
BaseEncoding.base16().encode(bytes);
```

#### 41、最接近的二次幂
``` java
/**
 * Return the exponent of the power of two closest to the given
 * positive value, or zero if value leq 0.
 */
private static int getClosestPowerOf2(int value) {
	if (value <= 0)
		throw new IllegalArgumentException("Undefined for " + value);
	final int hob = Integer.highestOneBit(value);
	return Integer.numberOfTrailingZeros(hob) + (((hob >>> 1) & value) == 0 ? 0 : 1);
	}
// 13: hob = 8 numberOfTrailingZeros = 3  4 & 13 = 4
// 9:  hob = 8 numberOfTrailingZeros = 3  4 & 9 = 0
```

#### 42、创建泛型数组
``` java
// Checked: strong typing. GenSet knows explicitly what type of objects it contains
// methods will throw an exception when they are passed arguments that are not of type E. 
// See Collections.checkedCollection.
public class GenSet<E> {
    private E[] a;
    public GenSet(Class<E> c, int s) {
        // Use Array native method to create array of a type only known at run time
        @SuppressWarnings("unchecked")
        final E[] a = (E[]) Array.newInstance(c, s);
        this.a = a;
    }
    E get(int i) {
        return a[i];
    }
}

// Unchecked: weak typing. No type checking is actually done on any of the objects passed as argument
public class GenSet<E> {
    private Object[] a;
    public GenSet(int s) {
        a = new Object[s];
    }
    E get(int i) {
        @SuppressWarnings("unchecked")
        final E e = (E) a[i];
        return e;
    }
}
```

#### 43、避免ConcurrentModificationException
``` java
Collection<Integer> coll = new ArrayList<Integer>();
//populate
coll.removeIf(i -> i.intValue() == 5);
```

#### 44、对map的value排序
``` java
public static <K, V extends Comparable<? super V>> Map<K, V> sortByValue(Map<K, V> map) {
    List<Map.Entry<K, V>> list = new LinkedList<Map.Entry<K, V>>(map.entrySet());
    Collections.sort(list, new Comparator<Map.Entry<K, V>>() {
        public int compare( Map.Entry<K, V> o1, Map.Entry<K, V> o2 ) {
            return (o1.getValue()).compareTo( o2.getValue() );
        }
    });

    Map<K, V> result = new LinkedHashMap<K, V>();
    for (Map.Entry<K, V> entry : list) {
        result.put( entry.getKey(), entry.getValue() );
    }
    return result;
}


public static <K, V extends Comparable<? super V>> Map<K, V> sortByValue(Map<K, V> map) {
    return map.entrySet()
              .stream()
              .sorted(Map.Entry.comparingByValue(/*Collections.reverseOrder()*/))
              .collect(
                Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new )
               );
}
```

#### 45、格式化double类型
``` java
DecimalFormat df = new DecimalFormat("#.#####");
df.setRoundingMode(RoundingMode.CEILING);
df.format(234.212431324); // 234.2125

BigDecimal bd = new BigDecimal(d).setScale(4, RoundingMode.HALF_EVEN);
d = bd.doubleValue(234.212431324); // 234.2125
```

#### 46、clone对象
``` java
@Override
public CategoryBean clone() {
    CategoryBean clone = null;
    try {
        clone = (CategoryBean) super.clone();
    } catch (CloneNotSupportedException ex) {
        throw new RuntimeException(ex);
    }
    return clone;
}
// 深度克隆
public static CategoryBean deepClone(CategoryBean src) {
  CategoryBean dst = null;
  try {
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            ObjectOutputStream oo = new ObjectOutputStream(out);
            oo.writeObject(src);
            ByteArrayInputStream in = new ByteArrayInputStream(out.toByteArray());
            ObjectInputStream oi = new ObjectInputStream(in);
            dst = oi.readObject();
       } catch (IOException e) {
             throw new RuntimeException(e);
       } catch (ClassNotFoundException e) {
              throw new RuntimeException(e);
     }
   return dst;
  }

```

#### 47、Enum高级用法
``` java
import org.joda.time.*;
import java.util.function.BiFunction;
import java.util.function.Function;

public enum TimePeriod {

    MINUTE((from, to) -> Minutes.minutesBetween(from, to).getMinutes() + 1,
            from -> from.withZone(DateTimeZone.UTC)
                    .withSecondOfMinute(0)
                    .withMillisOfSecond(0)),
		    
    HOUR((from, to) -> Hours.hoursBetween(from, to).getHours() + 1,
            from -> from.withZone(DateTimeZone.UTC)
                    .withMinuteOfHour(0)
                    .withSecondOfMinute(0)
                    .withMillisOfSecond(0)),
		    
    DAY((from, to) -> Days.daysBetween(from, to).getDays() + 1,
            from -> from.withZone(DateTimeZone.UTC)
                    .withTimeAtStartOfDay());

    private BiFunction<DateTime, DateTime, Integer> getTimeIntervalFunc;

    private Function<DateTime, DateTime> getStartTimeFunc;

    private TimePeriod(BiFunction<DateTime, DateTime, Integer> getTimeIntervalFunc,
                       Function<DateTime, DateTime> getStartTimeFunc) {
        this.getTimeIntervalFunc = getTimeIntervalFunc;
        this.getStartTimeFunc = getStartTimeFunc;
    }

    public int getTimeInterval(DateTime from, DateTime to) {
        return getTimeIntervalFunc.apply(from, to);
    }

    public DateTime getStartTime(DateTime from) {
        return getStartTimeFunc.apply(from);
    }
}

// 另一个例子
public enum Operation {
  PLUS("+") {
    @Override
    public int apply(int x, int y) {
      return x + y;
    }
  }, 

  MINUS("-") {
    @Override
    public int apply(int x, int y) {
      return x - y;
    }
  };

  public abstract int apply(int x, int y);

  public String operator() {
    return op;
  }

  private Operation(String op) {
    this.op = op;
  }

  private String op;
}
```

#### 48、ExpectedException异常测试
``` java
class ExceptionsThrower {
    void throwRuntimeException(int i) {
        if (i <= 0) {
            throw new RuntimeException("Illegal argument: i must be <= 0");
        }
        throw new RuntimeException("Runtime exception occurred");
    }
}

public class ExpectedExceptionsTest {
	@Rule
	public ExpectedException thrown = ExpectedException.none();

	@Test
	public void verifiesMessageStartsWith() {
	    thrown.expect(RuntimeException.class);
	    thrown.expectMessage(startsWith("Illegal argument:"));
	    exceptionsThrower.throwRuntimeException(-1);
	}
}
```


### 49、AOP实现方法耗时统计
[Spring AOP Method Profiling](http://www.journaldev.com/7801/spring-aop-method-profiling-beginners-example)
``` java
import org.aspectj.lang.ProceedingJoinPoint;
import org.springframework.util.StopWatch;
import org.springframework.util.StopWatch.TaskInfo;
public class TimeConsumeProfiler {
    public Object timeProfile(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start(proceedingJoinPoint.toShortString());
        boolean isExceptionThrown = false;
        try {
            // execute the profiled method
            return proceedingJoinPoint.proceed();
        } catch (RuntimeException e) {
            isExceptionThrown = true;
            throw e;
        } finally {
            stopWatch.stop();
            TaskInfo taskInfo = stopWatch.getLastTaskInfo();
            String profileMessage = taskInfo.getTaskName() + ": " + taskInfo.getTimeMillis() + " ms" +
                    (isExceptionThrown ? " (thrown Exception)" : "");
            System.out.println(profileMessage);
        }
    }
}

// spring.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
		">
    
    <context:component-scan base-package="com.aaa.bbb.profiler"/>
    <context:annotation-config/>
    <bean id="timeConsumeProfiler" class="com.aaa.bbb.profiler.core.TimeConsumeProfiler"/>
    <aop:config>
        <aop:aspect ref="timeConsumeProfiler">
            <!-- 定义在service包里的任意方法的执行 -->
            <aop:pointcut id="serviceMethod"
                          expression="execution(* com.aaa.bbb.profiler.service.*.*(..))"/>
            <aop:around pointcut-ref="serviceMethod" method="timeProfile"/>
        </aop:aspect>
    </aop:config>
</beans>

```

### 50、lambda异常包装
``` java
org.jooq.lambda.Unchecked

Arrays.stream(dir.listFiles()).forEach(
    Unchecked.consumer(file -> { System.out.println(file.getCanonicalPath()); })
);

Arrays.stream(dir.listFiles())
        .map(Unchecked.function(File::getCanonicalPath))
        .forEach(System.out::println);
```

### 51、回调模式
``` java
public interface Callback {
  void call();
}

public abstract class Task {
  /**
   * Execute with callback
   */
  public final void executeWith(Callback callback) {
    execute();
    if (callback != null) {
      callback.call();
    }
  }

  public abstract void execute();
}

public class SimpleTask extends Task {
  @Override
  public void execute() {
    LOGGER.info("Perform some important activity and then call the callback method.");
  }
}

public static void main(String[] args) {
    Task task = new SimpleTask();
    Callback c = () -> LOGGER.info("I'm done now.");
    task.executeWith(c);
}
```

### 52、数组比较
``` java

int[] arr1 = new int[20]; // Initialized to 0
int[] arr2 = new int[20]; // Initialized to 0
System.out.println(Arrays.equals(arr1, arr2)); // Prints true
System.out.println(arr1 == arr2); // Prints false
```

### 53、检测Integer溢出overflow、截取
``` java
// 使用Java8 Math类提供的addExact或者multiplyExact等方法，这些方法要么返回正确结果，要么抛出ArithmeticException
public static int multAccum(int oldAcc, int newVal, int scale) {
  return Math.addExact(oldAcc, Math.multiplyExact(newVal, scale));
}

// Check whether i is within byte range
if ((i < Byte.MIN_VALUE) || (i > Byte.MAX_VALUE)) {
     throw new ArithmeticException("Value is out of range");
}
byte b = (byte) i;

// int向float转换避免进度损失，float有23位的尾数
int num1 = 1234567890;
float num2 = 1234567890;
int result = num1 - (int)num2; // -46
// 扩大类型
double num2 = 1234567890;
int result = num1 - (int)num2; // 0
// 检查范围
if ((num2 > 0x007fffff) || (num2 < -0x800000)) {
  throw new ArithmeticException("Insufficient precision");
}
```

### 54、整数转为浮点数
``` java
short a = 533;
int b = 6789;
long c = 4664382371590123456L;
 
float d = a / 7;    // d is 76.0 (truncated)
double e = b / 30;  // e is 226.0 (truncated)
double f = c * 2;   // f is -9.1179793305293046E18

float d = a / 7.0f;       // d is 76.14286
double e = b / 30.;       // e is 226.3
double f = (double)c * 2; // f is 9.328764743180247E18
d /= 7;  // d is 76.14286
e /= 30; // e is 226.3
f *= 2;  // f is 9.328764743180247E18

double value = Math.ceil(a/((double) b));
```

### 55、集合元素类型和方法参数类型保持一致
``` java
HashSet<Short> s = new HashSet<Short>();
for (int i = 0; i < 10; i++) {
  s.add((short)i);
  // Remove a Short
  if (s.remove((short)i) == false) {
    System.err.println("Error removing " + i);
  }
}
```

### 56、取余操作
> 5 % 3 = 2
<br>5 % (-3) = 2
<br>(-5) % 3 = -2
<br>(-5) % (-3) = -2

``` java
private int imod(int i, int j) {
  int temp = i % j;
  return (temp < 0) ? -temp : temp;
}
     
public int lookup(int hashKey) {
  return hash[imod(hashKey, SIZE)];
}

```


### 57、ExecutorCompletionService使用
> ExecutorCompletionService<V>内部维护列一个队列BlockingQueue，用于管理已完成的任务；内部还维护列一个Executor, 可以执行任务。任务一旦完成就加入到BlockingQueue中, 如果队列中的数据为空时, 调用take()就会阻塞 (等待任务完成)。

``` java
ExecutorCompletionService<String> completionService = new ExecutorCompletionService<String>(Executors.newFixedThreadPool(10));
for(int i=0; i<50; i++) {
    completionService.submit(new Callable<String>() {
        @Override
        public String call() throws Exception {
            return Thread.currentThread().getName();
        }
    });
}

int completionTask = 0;
while(completionTask < 50) {
    //如果完成队列中没有数据, 则阻塞; 否则返回队列中的数据
    Future<String> resultHolder = completionService.take();
    System.out.println("result: " + resultHolder.get());
    completionTask++;
}
```

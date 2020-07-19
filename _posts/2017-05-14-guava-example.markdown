---
layout:     post
title:      "Google Guava 学习笔记"
date:       2017-05-13 17:00:00
author:     "Daniel"
header-img: "img/post-bg-java.jpg"
tags:
    - Java
    - Guava

---

####  Joiner

``` java
Joiner.on("|").skipNulls().join(stringList);
Joiner.on("|").useForNull("no value").join(stringList);
Joiner.on("#").withKeyValueSeparator("=").join(stringMap);
```

#### Splitter

``` java
Splitter.on(Pattern.compile("[\\r\\n]+")).omitEmptyStrings().trimResults().splitToList(str)
Splitter.MapSplitter mapSplitter = Splitter.on("#").withKeyValueSeparator("=");
Map<String, String> splitMap = mapSplitter.split(stringMap);
```
####  Preconditions

```java
Preconditions.checkNotNull (T object, Object message) // 返回NullPointerException
Preconditions.checkElementIndex (int index, int size, Object message) // 返回IndexOutOfBoundsException
Preconditions.checkArgument (Boolean expression, Object message) //验证传递给函数的参数，expression如果为false则抛出IllegalArgumentException
Preconditions.checkState(Boolean expression, Object message)  //验证对象的状态，expression如果为false则抛出IllegalArgumentException
```

#### Objects

``` java
public String toString() {
    return Objects.toStringHelper(this)
            .omitNullValues()
            .add("title", title)
            .add("author", author)
            .toString();
 }
 
 Objects.firstNonNull(someString,"default value");  // 如果someString为空，则返回"default value"
 
ObjectUtils.toString(o1); // ""
ObjectUtils.toString(o1, "isNull"); // "isNull"
StringUtils.defaultIfEmpty(o1, "");

Objects.hashCode(title, author);  //产生hash值
 
 public int compareTo(Book o) {
    return ComparisonChain
            .start()
            .compare(this.title, o.getTitle())
            .compare(this.author, o.getAuthor())
            .result();
 }
```

####  Optional

``` java
Optional.fromNullable(obj).or(defaultValue);
```

#### Predicate
Function的apply用于转换对象，Predicate用于过滤对象
``` java
Predicate<Integer> isEven = new Predicate<Integer>() {
    @Override public boolean apply(Integer number) {
        return (number % 2) == 0;
    }               
};
Iterable<Integer> evenNumbers = Iterables.filter(numbers, isEven);

List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Collection<String> result = Collections2.filter(names, predicate);
```

####  Predicates
```java
List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Iterable<String> result = Iterables.filter(names, Predicates.containsPattern("a"));
Collection<String> result = Collections2.filter(names, Predicates.containsPattern("a"));
Collection<String> result = Collections2.filter(names, Predicates.notNull());
boolean result = Iterables.all(names, Predicates.containsPattern("n|m"));

// 过滤从List包含的Keys
Map<OccupancyType, BigDecimal> filteredMap
    = Maps.filterKeys(roomPrice, Predicates.in(policyList));

Collection<BigDecimal> col = Maps.filterKeys(roomPrice, Predicates.in(policyList)).values()
```


#### FluentIterable

``` java
List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Iterable<String> result = Iterables.filter(names, Predicates.containsPattern("a"));
Collection<String> result = Collections2.filter(names, 
      Predicates.or(Predicates.containsPattern("J"), 
      Predicates.not(Predicates.containsPattern("a"))));
boolean result = Iterables.all(names, Predicates.containsPattern("n|m"));
// List to Set
Set<To> result = FluentIterable.from(myList)
                               .transform(new Function<From, To>() {
                                   @Override
                                   public To apply(From input) {
                                       return convert(input);
                                   }
                               })
                               .toSet();
// 过滤List
Iterable<String> filtered = Iterables.filter(addressList, new Predicate<String>() {
    @Override
    public boolean apply(@Nullable String addr) {
        return !isValid(addr);
    }
});
List<String> invalidAddressList = Lists.newArrayList(filtered);

// 过滤map的keys
Set<String> invalidSet = Maps.filterEntries(mobilesMap, new Predicate<Map.Entry<String, Date>>() {
    @Override
    public boolean apply(@Nullable Map.Entry<String, Date> input) {
        return !isValid(input.getKey(), input.getValue());
    }
}).keySet();
List<String> invalidMobiles = Lists.newArrayList(invalidMobileSet);

```

#### Function
```java
Function<String, Integer> function = new Function<String, Integer>() {
    @Override
    public Integer apply(String input) {
        return input.length();
    }
};

List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Iterable<Integer> result = Iterables.transform(names, function);
Collection<Integer> result = Collections2.transform(names, function);

Function<String,Integer> f1 = new Function<String,Integer>(){
    @Override
    public Integer apply(String input) {
        return input.length();
    }
};

Function<Integer,Boolean> f2 = new Function<Integer,Boolean>(){
    @Override
    public Boolean apply(Integer input) {
        return input % 2 == 0;
    }
};

List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Collection<Boolean> result = Collections2.transform(names, Functions.compose(f2, f1));

```


#### Functions

``` java
Lists.transform(numberList, Functions.toStringFunction());

Predicate<String> predicate = new Predicate<String>() {
    @Override
    public boolean apply(String input) {
        return input.startsWith("A") || input.startsWith("T");
    }
};
Function<String, Integer> func = new Function<String,Integer>(){
    @Override
    public Integer apply(String input) {
        return input.length();
    }
};
List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
Collection<Integer> result = FluentIterable.from(names)
                                           .filter(predicate)
                                           .transform(func)
                                           .toList();

Function<Integer, Integer> powerOfTwo = new Function<Integer, Integer>() {
    @Override
    public Integer apply(Integer input) {
        return (int) Math.pow(input, 2);
    }
};
Set<Integer> lowNumbers = Sets.newHashSet(2, 3, 4);
Map<Integer, Integer> numberToPowerOfTwoMuttable = Maps.asMap(lowNumbers, powerOfTwo);
Map<Integer, Integer> numberToPowerOfTwoImuttable = Maps.toMap(lowNumbers, powerOfTwo);
```

#### Mutlimaps
一个key，多个value
``` java
Map<String,List<MyClass>> myClassListMap test2 = new HashMap<String,List<MyClass>>();

Multimap<String, String> myMultimap = ArrayListMultimap.create();
myMultimap.put("Fruits", "Bannana");
myMultimap.put("Fruits", "Apple");
myMultimap.put("Fruits", "Pear");
Collection<string> fruits = myMultimap.get("Fruits");
myMultimap.remove("Fruits","Pear");
myMultimap.removeAll("Fruits");
```

#### Table
 
``` java
// Table<R,C,V> == Map<R,Map<C,V>>
Table<String, String, String> employeeTable = HashBasedTable.create();
employeeTable.put("IBM", "101","Mahesh");
employeeTable.put("IBM", "102","Ramesh");
employeeTable.put("IBM", "103","Suresh");
employeeTable.put("Microsoft", "111","Sohan");
employeeTable.put("Microsoft", "112","Mohan");
employeeTable.put("Microsoft", "113","Rohan");
Map<String,String> ibmEmployees =  employeeTable.row("IBM");
Set<String> employers = employeeTable.rowKeySet();
Map<String,String> EmployerMap =  employeeTable.column("102");

Table<String, String, Integer> universityCourseSeatTable
  = ImmutableTable.<String, String, Integer> builder()
  .put("Mumbai", "Chemical", 120).build();
```

#### CharMatcher
```java
// Remove all lowercase letters
String allButLowerCase = CharMatcher.JAVA_LOWER_CASE.negate().retainFrom("B-double E double R U-N beer run");

// Remove all * from string
String stringWithoutAstricks = CharMatcher.is('*').removeFrom("(* This is a comment.  The compiler will ignore it. *)");

// Validate letter and digit
boolean randomPharse = CharMatcher.JAVA_LETTER_OR_DIGIT.matchesAllOf("wefwewef3r343fwdSVD()I#KMFI");

// Obtain digits from telephone number
String telephoneNumber = CharMatcher.inRange('0','9').retainFrom("123-456-7890"); # 1234567890

// Count matching chars
int numberOfDigits = CharMatcher.DIGIT.countIn("123-LevelUpLunch");

// Collapse whitespace to dash
String address = "505 Williams Street";
String addressWithDash = CharMatcher.WHITESPACE.collapseFrom(address, '-');
```

#### stringConverter
```java
Double val = Doubles.stringConverter().convert("1.0");
String valAsString = Doubles.stringConverter().reverse().convert(new Double(1));
Integer val = Ints.stringConverter().convert("3");
String valAsString = Ints.stringConverter().reverse().convert(new Integer(3));

// 判断字符串是否为整数  
NumberUtils.isDigits(str);  
// 判断字符串是否为数字  
NumberUtils.isNumber(str);
```

#### Cache
```java

// cacheLoader方式实现
LoadingCache<String,String> cahceBuilder=CacheBuilder
    .newBuilder()
    .build(new CacheLoader<String, String>(){
        @Override
        public String load(String key) throws Exception {
            String value="hello "+key+"!";
            return value;
        }

    });

// callback方式实现
Cache<String, String> cache = CacheBuilder.newBuilder().maximumSize(1000).build();
    String resultVal = cache.get("jerry", new Callable<String>() {
        public String call() {
            String value="hello "+"jerry"+"!";
            return value;
        }
    });

// 泛型方式实现
public  <K , V> LoadingCache<K , V> cached(CacheLoader<K , V> cacheLoader) {
      LoadingCache<K , V> cache = CacheBuilder
      .newBuilder()
      .maximumSize(2)
      .weakKeys()
      .softValues()
      .refreshAfterWrite(120, TimeUnit.SECONDS)
      .expireAfterWrite(10, TimeUnit.MINUTES)
      .removalListener(new RemovalListener<K, V>(){
        @Override
        public void onRemoval(RemovalNotification<K, V> rn) {
            System.out.println(rn.getKey()+"被移除");

        }})
      .build(cacheLoader);
      return cache;
}

public  LoadingCache<String , String> commonCache(final String key) throws Exception{
    LoadingCache<String , String> commonCache= cached(new CacheLoader<String , String>(){
            @Override
            public String load(String key) throws Exception {
                return "hello "+key+"!";
            }
      });
    return commonCache;
}
// 构建LoadingCache对象
LoadingCache<String, TradeAccount> traLoadingCache =
        CacheBuilder.newBuilder()
          .expireAfterAccess(5L, TimeUnit.MINUTES) //5分钟后缓存失效
          .maximumSize(5000L) //最大缓存5000个对象
          .removalListener(new TradeAccountRemovalListener()) //注册缓存对象移除监听器
          .ticker(Ticker.systemTicker()) //定义缓存对象失效的时间精度为纳秒级
          .build(new CacheLoader<String, TradeAccount>(){ 
            @Override
            public TradeAccount load(String key) throws Exception {
              // load a new TradeAccount not exists in cache
              return null;
            }
          });
// SoftReference对象实现自动回收
LoadingCache<String, TradeAccount> traLoadingCache =
        CacheBuilder.newBuilder()
          .expireAfterAccess(5L, TimeUnit.MINUTES) //5分钟后缓存失效
          .softValues() //使用SoftReference对象封装value, 使得内存不足时，自动回收
          .removalListener(new TradeAccountRemovalListener()) //注册缓存对象移除监听器
          .ticker(Ticker.systemTicker()) //定义缓存对象失效的时间精度为纳秒级
          .build(new CacheLoader<String, TradeAccount>(){ 
            @Override
            public TradeAccount load(String key) throws Exception {
              // load a new TradeAccount not exists in cache
              return null;
            }
          });
// 自动刷新缓存
LoadingCache<String, TradeAccount> traLoadingCache =
        CacheBuilder.newBuilder()
          .concurrencyLevel(10) //允许同时最多10个线程并发修改
          .refreshAfterWrite(5L, TimeUnit.SECONDS) //5秒中后自动刷新
          .removalListener(new TradeAccountRemovalListener()) //注册缓存对象移除监听器
          .ticker(Ticker.systemTicker()) //定义缓存对象失效的时间精度为纳秒级
          .build(new CacheLoader<String, TradeAccount>(){ 
            @Override
            public TradeAccount load(String key) throws Exception {
              // load a new TradeAccount not exists in cache
              return null;
            }
          });          
```


#### List to Map
``` java
//Maps.toMap 返回一个不可变的map 
Map<Book,String> immutableMap=Maps.toMap(Sets.newHashSet(books),new Function<Book,String>(){  
    @Override  
    String apply(Book input) {  
        return input.getIsbn()  
    }  
});


//Maps.asMap books产生map的key,Function产生map的value  
Map<Book,String> asBookMap=Maps.asMap(Sets.newHashSet(books),new Function<Book,String>(){  
    @Override  
    String apply(Book input){  
        return input.getIsbn()  
    }  
}) 

//Maps.uniqueIndex iterator 产生map的value,Function 的结果作为map的key  
Map<String,Book> bookMap= Maps.uniqueIndex(books.iterator(),new Function<Book,String>(){  
    @Override  
    String apply(Book input) {  
        return input.getIsbn()  
    }  
})  
```

#### 根据value大小找出topK的key
```java
public static <K, V extends Comparable<?>> List<K> getTopKeys(Map<K, V> map, int n) {
  Function<K, V> getVal = Functions.forMap(map);
  Ordering<K> ordering = Ordering.natural().onResultOf(getVal);
  return ordering.greatestOf(map.keySet(), n);
}

public static <K, V> List<K> getTopKeys(Map<K, V> map, Comparator<V> comp, int n) {
  Function<K, V> getVal = Functions.forMap(map);
  Ordering<K> ordering = Ordering.from(comp).onResultOf(getVal);
  return ordering.greatestOf(map.keySet(), n);
}
```

#### 集合排序
```java
List<Integer> nums = Arrays.asList(3, 5, 4, null, 1, 2); 
Collections.sort(nums,  Ordering.natural().nullsLast());
// [1, 2, 3, 4, 5, null]
Collections.sort(nums, Ordering.natural().nullsFirst());
// [null, 1, 2, 3, 4, 5]
Collections.sort(nums, Ordering.natural().reverse().nullsLast());
// [5, 4, 3, 2, 1, null]

Ordering<String> byLength = new Ordering<String>() {
   @Override
   public int compare(String s1, String s2) {
      return Ints.compare(s1.length(), s2.length());
   }
}; 
List<String> strings = Arrays.asList("xxx", "Z", null, "22", "A", "33", "11");
Collections.sort(strings,  byLength.nullsFirst());
// [null, Z, A, 22, 33, 11, xxx]
Collections.sort(strings, byLength.compound(Ordering.natural()).nullsFirst());
// [null, A, Z, 11, 22, 33, xxx]
Collections.sort(strings, byLength.compound(Ordering.natural().reverse()).nullsLast());
// [Z, A, 33, 22, 11, xxx, null]
Collections.sort(strings, byLength.reverse().compound(Ordering.natural()).nullsLast());
// [xxx, 11, 22, 33, A, Z, null]

Function<Object, String> toSimpleClassName = new Function<Object, String>() {
   @Override
   public String apply(Object obj) {
      return obj.getClass().getSimpleName();
   }
};
  
Lists.transform(ImmutableList.of("foo", 42, 'x'), toSimpleClassName); 
// [String, Integer, Character]
Ordering<Object> bySimpleClassName =  Ordering.natural().onResultOf(toSimpleClassName);
bySimpleClassName.sortedCopy(ImmutableList.of("foo", 42, 'x', "bar", 666)); 
// [x, 42, 666, foo, bar]
Multimaps.index(ImmutableList.of(3, 'y', true, "foo", 'z', 1, 'x', "bar", 2, false), toSimpleClassName
   ); 
// {Integer=[3, 1, 2], Character=[y, z, x], Boolean=[true, false], String=[foo, bar]}
```

#### 两个集合的操作
```java
MapDifference<Integer, Student> mapDifference = Maps.difference(geometryClass, gymClass);
Map<Integer, Student> commonStudents = mapDifference.entriesInCommon();
mapDifference.entriesDiffering()
Map<Integer, Student> studentsOnLeft = mapDifference.entriesOnlyOnLeft();
Map<Integer, Student> studentsOnTheRight = mapDifference.entriesOnlyOnRight();
```

#### RangeMap
```java
RangeMap<Integer, String> gradeScale = TreeRangeMap.create();
gradeScale.put(Range.closed(0, 60), "F");
gradeScale.put(Range.closed(61, 70), "D");
gradeScale.put(Range.closed(71, 80), "C");
gradeScale.put(Range.closed(81, 90), "B");
gradeScale.put(Range.closed(91, 100), "A");
String grade = gradeScale.get(77);

RangeMap<Integer, BannerDistributionEnum> DISTRIBUTION_MAP =
            ImmutableRangeMap.<Integer, DistributionEnum>builder()
            .put(Range.closedOpen(0, 5), DistributionEnum.ALGO_FLOW)
            .put(Range.closedOpen(5, 10), DistributionEnum.CONTRAST_CONFIG_FLOW)
            .put(Range.closedOpen(10, 100), DistributionEnum.CONFIG_FLOW)
            .build();
```

#### 创建静态的Map并初始化
```java
private static final Map<Level, String> PREFIXES = UnifiedMap.<Level, String>newMap()
    .withKeyValue(Level.CONFIG, "[config]")
    .withKeyValue(Level.INFO, "[info]")
    .withKeyValue(Level.SEVERE, "[error]")
    .withKeyValue(Level.WARNING, "[warning]")
    .asUnmodifiable();
Map<String, String> test = ImmutableMap.of("k1", "v1", "k2", "v2");
Map<String, String> test = ImmutableMap.<String, String>builder()
    .put("k1", "v1")
    .put("k2", "v2")
    .build();
HashMap<String, String> h = new HashMap<String, String>() two brace 
  put("a","b");
two brace;

```

#### 集合的交、并、补
``` java
Set<Integer> setA = Sets.newHashSet(1, 2, 3, 4, 5);
Set<Integer> setB = Sets.newHashSet(4, 5, 6, 7, 8);

SetView<Integer> union = Sets.union(setA, setB);
SetView<Integer> difference = Sets.difference(setA, setB);
SetView<Integer> intersection = Sets.intersection(setA, setB);
```


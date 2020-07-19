---
layout:     post
title:      "Java 8 Lambda 学习笔记"
date:       2017-05-13 17:10:00
author:     "Daniel"
header-img: "img/post-bg-java.jpg"
tags:
    - Java
    - Lambda

---

> 本笔记部分是根据[leveluplunch](http://www.leveluplunch.com/java/examples/#java-arrays)的博客整理的关于Java 8 lambda的知识。

### 1、统计汇总
``` java
DoubleSummaryStatistics stats = companies.stream().mapToDouble((x) -> x.getRevenue()).summaryStatistics();
DoubleSummaryStatistics stats = companies.stream().collect(Collectors.summarizingDouble(Company::getRevenue));
```

### 2、Optional使用
``` java
// ofNullable和orElse使用
Optional<Framework> nullOptional = Optional.ofNullable(null);
Framework orElseFramework = nullOptional.orElse(framework);
// orElseGet/orElseThrow使用
List<String> carsFiltered = Optional.ofNullable(cars)
                .orElseGet(Collections::emptyList)
                .stream()
                .filter(Objects::nonNull)
                .collect(Collectors.toList());
// 级联调用，避免空指针
Optional.of(new Outer())
    .map(Outer::getNested)
    .map(Nested::getInner)
    .map(Inner::getFoo)
    .ifPresent(System.out::println);
// 或者利用Supplier
Outer obj = new Outer();
resolve(() -> obj.getNested().getInner().getFoo());
    .ifPresent(System.out::println);
public static <T> Optional<T> resolve(Supplier<T> resolver) {
    try {
        T result = resolver.get();
        return Optional.ofNullable(result);
    }
    catch (NullPointerException e) {
        return Optional.empty();
    }
}
```

### 3、Function
``` java
Function<Person, Job> mapPersonToJob = new Function<Person, Job>() {
    public Job apply(Person person) {
        Job job = new Job(person.getPersonId(), person.getJobDescription());
        return job;
    }
};

List<Person> persons = Lists.newArrayList(new Person(1, "Husband"), new Person(2, "Dad"), new Person(3, "Software engineer"), new Person(4, "Adjunct instructor"), new Person(5, "Pepperoni hanger"));
// Map a list of objects
List<Job> jobs = persons.stream().map(mapPersonToJob).collect(Collectors.toList());
// Map object to object
Person person = new Person(1, "Description");
Job job = mapPersonToJob.apply(person);
```

### 4、Predicate
``` java
Predicate<String> hasLengthOf10 = new Predicate<String>() {
    @Override
    public boolean test(String t) {
        return t.length() > 10;
    }
};
boolean outcome = hasLengthOf10.test(randomString);
Predicate<String> nonNullPredicate = Objects::nonNull;
Predicate<String> containsLetterA = p -> p.contains("A");
boolean outcome = hasLengthOf10.or(containsLetterA).test(containsA);

public static <T> Predicate<T> or (Predicate<T> ... predicates){
    return Stream.of(predicates).reduce(Predicate::or).get();
}

public static <T> Predicate<T> and (Predicate<T> ... predicates){
    return Stream.of(predicates).reduce(Predicate::and).get();
}
```

### 5、构建stream
``` java
Stream<String> stream = Stream.of("java 8 ", "leveluplunch.com", "examples", "exercises");
// stream to String            
String joined = stream.map(String::trim).collect(Collectors.joining(","));

int[] numbers = { 1, 2, 3, 4, 5, 6, 7 };
// array to stream
int sum = Arrays.stream(numbers).sum();
// 统计单词个数
long uniqueWords = java.nio.file.Files
            .lines(Paths.get("word-occurrences-in-file.txt"), Charset.defaultCharset())
            .flatMap(line -> Arrays.stream(line.split(" ."))).distinct()
            .count();
// iterate
Stream.iterate(0, n -> n + 3).limit(10).forEach(System.out::println);
// generate
Stream.generate(Math::random).limit(10).forEach(System.out::println);
// 产生随机字符串列表
Stream.generate(()-> RandomStringUtils.randomAlphabetic(25)).limit(size).collect(Collectors.toList());
// 拼接stream
Stream<String> stream1 = Stream.of("one", "two");
Stream<String> stream2 = Stream.of("three", "four");
Stream.concat(stream1, stream2).forEach(e -> System.out.println(e));

IntStream intStream1 = IntStream.of(1, 2);
IntStream intStream2 = IntStream.of(3, 4);
IntStream.concat(intStream1, intStream2).forEach(e -> System.out.println(e));
int val = IntStream.concat(intStream1, intStream2).distinct().sum();
OptionalInt sum2 = IntStream.concat(intStream1, intStream2).distinct().reduce((a, b) -> a + b);
```

### 6、stream 转换为其他结构
``` java
// stream to List
List<String> abc = Stream.of("a", "b", "c").collect(Collectors.toList());
// stream to Map
Map<String, Item> map = items.stream().collect(Collectors.toMap(Item::getKey, item -> item));
// stream to Set
Set<Integer> numbers = Stream.of(1, 2, 3).collect(Collectors.toSet());
// stream to String
String streamToString = Stream.of("a", "b", "c").collect(Collectors.joining());
// stream to array
String[] stringArray = Stream.of("One", "Two", "Three").toArray(String[]::new);
Integer[] stringArray = Stream.of(1, 2, 3).toArray(Integer[]::new);
int[] intArray = IntStream.of(1, 2, 3).toArray();
// flatten List<List<Object>>
List<List<Object>> list = ...;
List<Object> flat = list.stream().flatMap(List::stream).collect(Collectors.toList());
// zip
final int size = bookIdList.size();
List<Integer> scoreList = IntStream.range(0, size).boxed().sorted(Comparator.reverseOrder()).collect(Collectors.toList());
List<BookRankBean> hotFavorBookList = Streams.zip(bookIdList.stream(), scoreList.stream(), BookRankBean::new)
                .collect(Collectors.toList());
```

### 7、IntStream
``` java
int sum = IntStream.builder().add(10).add(10).build().sum();

IntStream first = IntStream.builder().add(10).build();
IntStream second = IntStream.builder().add(10).build();
// concat
IntStream third = IntStream.concat(first, second);
// generate
OptionalInt one = IntStream.generate(() -> 1).limit(10).distinct().findFirst();
// iterate
List<Integer> numbers = IntStream.iterate(0, n -> n + 3).limit(3).boxed().collect(Collectors.toList());
// range
List<Integer> numbers = IntStream.range(1, 3).boxed().collect(Collectors.toList());
List<Integer> numbers = IntStream.rangeClosed(1, 3).boxed().collect(Collectors.toList());

List<String> integers = new ArrayList<String>();
integers.add("1");
integers.add("2");
integers.add("3");
OptionalInt intStream = integers.stream().mapToInt(Integer::parseInt).max();


int[] numbers = { 1, 2, 3, 4, 5, 6 };
List<Integer> listOfInteger = Arrays.stream(numbers).boxed().collect(Collectors.toList());
```


### 8、Stream find/match
``` java
List<HiddenObjectGame> games;
boolean containVowel = games.stream().allMatch(game -> game.getName().contains("a"));
boolean playsGt1000 = games.stream().anyMatch(game -> game.getTotalPlays() > 1000);

Predicate<HiddenObjectGame> playsGt1000 = p -> p.getTotalPlays() > 1000;
Predicate<HiddenObjectGame> ratingGt5 = p -> p.getRating() > 5;
boolean noneMatch = games.stream().noneMatch(ratingGt5.and(playsGt1000));

Optional<HiddenObjectGame> firstHiddenGame = games.stream().findFirst();

Predicate<HiddenObjectGame> releaseDateInApril = p -> Month.APRIL == p.getReleaseDate().getMonth();
Optional<HiddenObjectGame> hiddenGameReleaseInApril = 	games.stream().filter(releaseDateInApril).findAny();
```


### 9、Stream filter
``` java
List<Post> postWithLessThan500 = posts.stream()
            .filter(p -> p.wordlength < 500).collect(Collectors.toList());

List<String> tags = posts
            .stream()
            .map(Post::getTags)
            .flatMap(tag -> Arrays.stream(tag.split(",")).map(String::trim).map(String::toLowerCase))
            .map(Object::toString)
            .distinct()
            .collect(Collectors.toList());

 List<Post> firstTwoPosts = posts.stream().limit(2).collect(Collectors.toList());
```


### 10、Stream reduce
``` java
List<Precipitation> precip;

double totalPrecipYear = precip.stream().mapToDouble(Precipitation::getAmount).sum();
OptionalDouble totalPrecipYear2 = precip.stream()
            .mapToDouble(Precipitation::getAmount).reduce(Double::sum);
double totalPrecipYear3 = precip.stream()
            .mapToDouble(Precipitation::getAmount).reduce(0, (a, b) -> a + b);

Optional<String> optionalJava = Stream.of("a", "b", "c").reduce((a, b) -> b);
String lastValue = Stream.of("a", "b", "c").reduce((a, b) -> b).orElse("false");
```


### 11、List 转换为原生数组
``` java
List<Double> searchEngineMarketShare = Lists.newArrayList();
double [] searchEngineMarketShareArray = searchEngineMarketShare
        .stream()
        .mapToDouble(Double::doubleValue)
        .toArray();
```


### 12、过滤空值
``` java
Stream<String> language = Stream.of("java", "python", "node", null, "ruby", null, "php");
List<String> result = language.filter(x -> x!=null).collect(Collectors.toList());
result.forEach(System.out::println);
List<String> result = language.filter(Objects::nonNull).collect(Collectors.toList());
```

### 13、对Map value排序
``` java
unsortMap.entrySet().stream()
                .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
                .forEachOrdered(x -> result.put(x.getKey(), x.getValue()));
public static <K, V extends Comparable<? super V>> Map<K, V> compareByValue(Map<K, V> map) {
	  Map<K, V> result = new LinkedHashMap<>();
	  Stream<Map.Entry<K, V>> mapInStream = map.entrySet().stream();
	  mapInStream.sorted(Map.Entry.comparingByValue())
	      .forEachOrdered(x -> result.put(x.getKey(), x.getValue()));
	  return result;
}
```

### 14、groupBy/count
``` java
List<String> items = Arrays.asList("apple", "apple", "banana", "apple", "orange", "banana", "papaya");
Map<String, Long> result = items.stream().collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));

Map<String, Long> counting = items.stream().collect(Collectors.groupingBy(Item::getName, Collectors.counting()));
Map<String, Integer> sum = items.stream().collect(Collectors.groupingBy(Item::getName, Collectors.summingInt(Item::getQty)));  
Map<String,List<Person>> personByCity =  people.stream().collect(Collectors.groupingBy(Person::getCity));             
```

### 15、对集合排序Comparator
``` java
Collections.sort(listDevs, new Comparator<Developer>() {
    @Override
    public int compare(Developer o1, Developer o2) {
        return o1.getName().compareTo(o2.getName());
    }
});
listDevs.sort((Developer o1, Developer o2)->o1.getName().compareTo(o2.getName()));
listDevs.sort((o1, o2)->o1.getName().compareTo(o2.getName()));
listDevs.sorted(Comparator.comparing(Developer::getName))
listDevs.sort(Comparator.comparing(Developer::getName)
                      .reversed()
                      .thenComparing(Comparator.comparing(Movie::getRating)
                      .reversed())
```

### 16、自定义Function
``` java
/**
 * Represents a function that accepts four arguments and produces a result.  This is the four-arity
 * specialization of {@link Function}.
 *
 * <p> This is a functional interface whose functional method is
 * {@link #apply(Object, Object, Object, Object)}.
 *
 * @param <A> the type of the first argument to the function
 * @param <B> the type of the second argument to the function
 * @param <C> the type of the third argument to the function
 * @param <D> the type of the fourth argument to the function
 * @param <R> the type of the result of the function
 * @see Function
 * @since 0.1.0
 */
@FunctionalInterface
public interface Function4<A, B, C, D, R> {

  /**
   * Applies this function to the given arguments.
   *
   * @param a the first function argument
   * @param b the second function argument
   * @param c the third function argument
   * @param d the fourth function argument
   * @return the function result
   * @since 0.1.0
   */
  R apply(A a, B b, C c, D d);

  /**
   * Returns a composed function that first applies this function to
   * its input, and then applies the {@code after} function to the result.
   * If evaluation of either function throws an exception, it is relayed to
   * the caller of the composed function.
   *
   * @param <V>   the type of output of the {@code after} function, and of the
   *              composed function
   * @param after the function to apply after this function is applied
   * @return a composed function that first applies this function and then
   * applies the {@code after} function
   * @throws NullPointerException if after is null
   * @since 0.1.0
   */
  default <V> Function4<A, B, C, D, V> andThen(Function<? super R, ? extends V> after) {
    Objects.requireNonNull(after);
    return (A a, B b, C c, D d) -> after.apply(apply(a, b, c, d));
  }
}

@FunctionalInterface
public interface Function3<A, B, C, R> {
  R apply(A a, B b, C c);

  default <V> Function3<A, B, C, V> andThen(Function<? super R, ? extends V> after) {
    Objects.requireNonNull(after);
    return (A a, B b, C c) -> after.apply(apply(a, b, c));
  }
}
```


### 17、Try
``` java
/**
 * @see [https://github.com/jasongoodwin/better-java-monads]
 * @param <T>
 * @author changdong
 */
public abstract class Try<T> {

    protected Try() {
    }

    public static <U> Try<U> ofFailable(TrySupplier<U> f) {
        Objects.requireNonNull(f);
        try {
            return Try.successful(f.get());
        } catch (Throwable t) {
            return Try.failure(t);
        }
    }

    /**
     * Transform success or pass on failure.
     * Takes an optional type parameter of the new type.
     * You need to be specific about the new type if changing type
     * <p>
     * Try.ofFailable(() -&gt; "1").&lt;Integer&gt;map((x) -&gt; Integer.valueOf(x))
     *
     * @param f   function to apply to successful value.
     * @param <U> new type (optional)
     * @return Success&lt;U&gt; or Failure&lt;U&gt;
     */

    public abstract <U> Try<U> map(TryMapFunction<? super T, ? extends U> f);

    /**
     * Transform success or pass on failure, taking a Try&lt;U&gt; as the result.
     * Takes an optional type parameter of the new type.
     * You need to be specific about the new type if changing type.
     * <p>
     * Try.ofFailable(() -&gt; "1").&lt;Integer&gt;flatMap((x) -&gt; Try.ofFailable(() -&gt; Integer.valueOf(x)))
     * returns Integer(1)
     *
     * @param f   function to apply to successful value.
     * @param <U> new type (optional)
     * @return new composed Try
     */
    public abstract <U> Try<U> flatMap(TryMapFunction<? super T, Try<U>> f);

    /**
     * Specifies a result to use in case of failure.
     * Gives access to the exception which can be pattern matched on.
     * <p>
     * Try.ofFailable(() -&gt; "not a number")
     * .&lt;Integer&gt;flatMap((x) -&gt; Try.ofFailable(() -&gt;Integer.valueOf(x)))
     * .recover((t) -&gt; 1)
     * returns Integer(1)
     *
     * @param f function to execute on successful result.
     * @return new composed Try
     */

    public abstract T recover(Function<? super Throwable, T> f);

    /**
     * Try applying f(t) on the case of failure.
     *
     * @param f function that takes throwable and returns result
     * @return a new Try in the case of failure, or the current Success.
     */
    public abstract Try<T> recoverWith(TryMapFunction<? super Throwable, Try<T>> f);

    /**
     * Return a value in the case of a failure.
     * This is similar to recover but does not expose the exception type.
     *
     * @param value return the try's value or else the value specified.
     * @return new composed Try
     */
    public abstract T orElse(T value);

    /**
     * Return another try in the case of failure.
     * Like recoverWith but without exposing the exception.
     *
     * @param f return the value or the value from the new try.
     * @return new composed Try
     */
    public abstract Try<T> orElseTry(TrySupplier<T> f);

    /**
     * Gets the value T on Success or throws the cause of the failure.
     *
     * @return T
     * @throws Throwable produced by the supplier function argument
     */

    public abstract <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X;

    /**
     * Gets the value T on Success or throws the cause of the failure.
     *
     * @return T
     * @throws Throwable
     */
    public abstract T get() throws Throwable;

    /**
     * Gets the value T on Success or throws the cause of the failure wrapped into a RuntimeException
     *
     * @return T
     * @throws RuntimeException
     */
    public abstract T getUnchecked();

    public abstract boolean isSuccess();

    /**
     * Performs the provided action, when successful
     *
     * @param action action to run
     * @return new composed Try
     * @throws E if the action throws an exception
     */
    public abstract <E extends Throwable> Try<T> onSuccess(TryConsumer<T, E> action) throws E;

    /**
     * Performs the provided action, when failed
     *
     * @param action action to run
     * @return new composed Try
     * @throws E if the action throws an exception
     */
    public abstract <E extends Throwable> Try<T> onFailure(TryConsumer<Throwable, E> action) throws E;

    /**
     * If a Try is a Success and the predicate holds true, the Success is passed further.
     * Otherwise (Failure or predicate doesn't hold), pass Failure.
     *
     * @param pred predicate applied to the value held by Try
     * @return For Success, the same success if predicate holds true, otherwise Failure
     */
    public abstract Try<T> filter(Predicate<T> pred);

    /**
     * Try contents wrapped in Optional.
     *
     * @return Optional of T, if Success, Empty if Failure or null value
     */
    public abstract Optional<T> toOptional();

    /**
     * Factory method for failure.
     *
     * @param e   throwable to create the failed Try with
     * @param <U> Type
     * @return a new Failure
     */

    public static <U> Try<U> failure(Throwable e) {
        return new Failure<>(e);
    }

    /**
     * Factory method for success.
     *
     * @param x   value to create the successful Try with
     * @param <U> Type
     * @return a new Success
     */
    public static <U> Try<U> successful(U x) {
        return new Success<>(x);
    }
}

class Success<T> extends Try<T> {

    private final T value;

    public Success(T value) {
        this.value = value;
    }

    @Override
    public <U> Try<U> flatMap(TryMapFunction<? super T, Try<U>> f) {
        Objects.requireNonNull(f);
        try {
            return f.apply(value);
        } catch (Throwable t) {
            return Try.failure(t);
        }
    }

    @Override
    public T recover(Function<? super Throwable, T> f) {
        Objects.requireNonNull(f);
        return value;
    }

    @Override
    public Try<T> recoverWith(TryMapFunction<? super Throwable, Try<T>> f) {
        Objects.requireNonNull(f);
        return this;
    }

    @Override
    public T orElse(T value) {
        return this.value;
    }

    @Override
    public Try<T> orElseTry(TrySupplier<T> f) {
        Objects.requireNonNull(f);
        return this;
    }

    @Override
    public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {
        return value;
    }

    @Override
    public T get() throws Throwable {
        return value;
    }

    @Override
    public T getUnchecked() {
        return value;
    }

    @Override
    public <U> Try<U> map(TryMapFunction<? super T, ? extends U> f) {
        Objects.requireNonNull(f);
        try {
            return new Success<>(f.apply(value));
        } catch (Throwable t) {
            return Try.failure(t);
        }
    }

    @Override
    public boolean isSuccess() {
        return true;
    }

    @Override
    public <E extends Throwable> Try<T> onSuccess(TryConsumer<T, E> action) throws E {
        action.accept(value);
        return this;
    }

    @Override
    public Try<T> filter(Predicate<T> p) {
        Objects.requireNonNull(p);

        if (p.test(value)) {
            return this;
        } else {
            return Try.failure(new NoSuchElementException("Predicate does not match for " + value));
        }
    }

    @Override
    public Optional<T> toOptional() {
        return Optional.ofNullable(value);
    }

    @Override
    public <E extends Throwable> Try<T> onFailure(TryConsumer<Throwable, E> action) {
        return this;
    }
}


class Failure<T> extends Try<T> {

    private final Throwable e;

    Failure(Throwable e) {
        this.e = e;
    }

    @Override
    public <U> Try<U> map(TryMapFunction<? super T, ? extends U> f) {
        Objects.requireNonNull(f);
        return Try.failure(e);
    }

    @Override
    public <U> Try<U> flatMap(TryMapFunction<? super T, Try<U>> f) {
        Objects.requireNonNull(f);
        return Try.failure(e);
    }

    @Override
    public T recover(Function<? super Throwable, T> f) {
        Objects.requireNonNull(f);
        return f.apply(e);
    }

    @Override
    public Try<T> recoverWith(TryMapFunction<? super Throwable, Try<T>> f) {
        Objects.requireNonNull(f);
        try {
            return f.apply(e);
        } catch (Throwable t) {
            return Try.failure(t);
        }
    }

    @Override
    public T orElse(T value) {
        return value;
    }

    @Override
    public Try<T> orElseTry(TrySupplier<T> f) {
        Objects.requireNonNull(f);
        return Try.ofFailable(f);
    }

    @Override
    public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {
        throw exceptionSupplier.get();
    }

    @Override
    public T get() throws Throwable {
        throw e;
    }

    @Override
    public T getUnchecked() {
        throw new RuntimeException(e);
    }

    @Override
    public boolean isSuccess() {
        return false;
    }

    @Override
    public <E extends Throwable> Try<T> onSuccess(TryConsumer<T, E> action) {
        return this;
    }

    @Override
    public Try<T> filter(Predicate<T> pred) {
        return this;
    }

    @Override
    public Optional<T> toOptional() {
        return Optional.empty();
    }

    @Override
    public <E extends Throwable> Try<T> onFailure(TryConsumer<Throwable, E> action) throws E {
        action.accept(e);
        return this;
    }
}

public interface TryConsumer<T, E extends Throwable> {
    void accept(T t) throws E;
}

public interface TryMapFunction<T, R> {
    /**
     *
     * @param t
     * @return
     * @throws Throwable
     */
    R apply(T t) throws Throwable;
}

public interface TrySupplier<T>{
    /**
     * 
     * @return
     * @throws Throwable
     */
    T get() throws Throwable;
}

public class TryTest {

    @Test
    public void itShouldBeSuccessOnSuccess() throws Throwable{
        Try<String> t = Try.ofFailable(() -> "hey");
        assertTrue(t.isSuccess());
    }

    @Test
    public void itShouldHoldValueOnSuccess() throws Throwable{
        Try<String> t = Try.ofFailable(() -> "hey");
        assertEquals("hey", t.get());
    }

    @Test
    public void itShouldMapOnSuccess() throws Throwable{
        Try<String> t = Try.ofFailable(() -> "hey");
        Try<Integer> intT = t.map((x) -> 5);
        intT.get();
        assertEquals(5, intT.get().intValue());
    }

    @Test
    public void itShouldFlatMapOnSuccess() throws Throwable {
        Try<String> t = Try.ofFailable(() -> "hey");
        Try<Integer> intT = t.flatMap((x) -> Try.ofFailable(() -> 5));
        intT.get();
        assertEquals(5, intT.get().intValue());
    }

    @Test
    public void itShouldOrElseOnSuccess() {
        String t = Try.ofFailable(() -> "hey").orElse("jude");
        assertEquals("hey", t);
    }

    @Test
    public void itShouldReturnValueWhenRecoveringOnSuccess() {
        String t = Try.ofFailable(() -> "hey").recover((e) -> "jude");
        assertEquals("hey", t);
    }


    @Test
    public void itShouldReturnValueWhenRecoveringWithOnSuccess() throws Throwable {
        String t = Try.ofFailable(() -> "hey")
                .recoverWith((x) ->
                        Try.ofFailable(() -> "Jude")
                ).get();
        assertEquals("hey", t);
    }

    @Test
    public void itShouldOrElseTryOnSuccess() throws Throwable {
        Try<String> t = Try.ofFailable(() -> "hey").orElseTry(() -> "jude");

        assertEquals("hey", t.get());
    }

    @Test
    public void itShouldBeFailureOnFailure(){
        Try<String> t = Try.ofFailable(() -> {
            throw new Exception("e");
        });
        assertFalse(t.isSuccess());
    }

    @Test(expected = IllegalArgumentException.class)
    public void itShouldThrowExceptionOnGetOfFailure() throws Throwable{
        Try<String> t = Try.ofFailable(() -> {
            throw new IllegalArgumentException("e");
        });
        t.get();
    }

    @Test
    public void itShouldMapOnFailure(){
        Try<String> t = Try.ofFailable(() -> {
            throw new Exception("e");
        }).map((x) -> "hey" + x);

        assertFalse(t.isSuccess());
    }

    @Test
    public void itShouldFlatMapOnFailure(){
        Try<String> t = Try.ofFailable(() -> {
            throw new Exception("e");
        }).flatMap((x) -> Try.ofFailable(() -> "hey"));

        assertFalse(t.isSuccess());
    }

    @Test
    public void itShouldOrElseOnFailure() {
        String t = Try.<String>ofFailable(() -> {
            throw new IllegalArgumentException("e");
        }).orElse("jude");

        assertEquals("jude", t);
    }

    @Test
    public void itShouldOrElseTryOnFailure() throws Throwable {
        Try<String> t = Try.<String>ofFailable(() -> {
            throw new IllegalArgumentException("e");
        }).orElseTry(() -> "jude");

        assertEquals("jude", t.get());
    }

    @Test(expected = RuntimeException.class)
    public void itShouldGetAndThrowUncheckedException() throws Throwable {
        Try.<String>ofFailable(() -> {
            throw new Exception();
        }).getUnchecked();

    }

    @Test
    public void itShouldGetValue() throws Throwable {
        final String result = Try.<String>ofFailable(() -> "test").getUnchecked();

        assertEquals("test", result);
    }

    @Test
    public void itShouldReturnRecoverValueWhenRecoveringOnFailure() {
        String t = Try.ofFailable(() -> "hey")
                .<String>map((x) -> {
                    throw new Exception("fail");
                })
                .recover((e) -> "jude");
        assertEquals("jude", t);
    }


    @Test
    public void itShouldReturnValueWhenRecoveringWithOnFailure() throws Throwable {
        String t = Try.<String>ofFailable(() -> {
            throw new Exception("oops");
        })
                .recoverWith((x) ->
                        Try.ofFailable(() -> "Jude")
                ).get();
        assertEquals("Jude", t);
    }

    @Test
    public void itShouldHandleComplexChaining() throws Throwable {
        Try.ofFailable(() -> "1").<Integer>flatMap((x) -> Try.ofFailable(() -> Integer.valueOf(x))).recoverWith((t) -> Try.successful(1));
    }

    @Test
    public void itShouldPassFailureIfPredicateIsFalse() throws Throwable {
        Try t1 = Try.ofFailable(() -> {
            throw new RuntimeException();
        }).filter(o -> false);

        Try t2 = Try.ofFailable(() -> {
            throw new RuntimeException();
        }).filter(o -> true);

        assertEquals(t1.isSuccess(), false);
        assertEquals(t2.isSuccess(), false);
    }

    @Test
    public void isShouldPassSuccessOnlyIfPredicateIsTrue() throws Throwable {
        Try t1 = Try.<String>ofFailable(() -> "yo mama").filter(s -> s.length() > 0);
        Try t2 = Try.<String>ofFailable(() -> "yo mama").filter(s -> s.length() < 0);

        assertEquals(t1.isSuccess(), true);
        assertEquals(t2.isSuccess(), false);
    }

    @Test
    public void itShouldReturnEmptyOptionalIfFailureOrNullSuccess() throws Throwable {
        Optional<String> opt1 = Try.<String>ofFailable(() -> {
            throw new IllegalArgumentException("Expected exception");
        }).toOptional();
        Optional<String> opt2 = Try.<String>ofFailable(() -> null).toOptional();

        assertFalse(opt1.isPresent());
        assertFalse(opt2.isPresent());
    }

    @Test
    public void isShouldReturnTryValueWrappedInOptionalIfNonNullSuccess() throws Throwable {
        Optional<String> opt1 = Try.<String>ofFailable(() -> "yo mama").toOptional();

        assertTrue(opt1.isPresent());
    }

    @Test(expected = IllegalArgumentException.class)
    public void itShouldThrowExceptionFromTryConsumerOnSuccessIfSuccess() throws Throwable {
        Try<String> t = Try.ofFailable(() -> "hey");

        t.onSuccess(s -> {
            throw new IllegalArgumentException("Should be thrown.");
        });
    }

    @Test
    public void itShouldNotThrowExceptionFromTryConsumerOnSuccessIfFailure() throws Throwable {
        Try<String> t = Try.ofFailable(() -> {
            throw new IllegalArgumentException("Expected exception");
        });

        t.onSuccess(s -> {
            throw new IllegalArgumentException("Should NOT be thrown.");
        });
    }

    @Test
    public void itShouldNotThrowExceptionFromTryConsumerOnFailureIfSuccess() throws Throwable {
        Try<String> t = Try.ofFailable(() -> "hey");

        t.onFailure(s -> {
            throw new IllegalArgumentException("Should NOT be thrown.");
        });
    }

    @Test(expected = IllegalArgumentException.class)
    public void itShouldThrowExceptionFromTryConsumerOnFailureIfFailure() throws Throwable {
        Try<String> t = Try.ofFailable(() -> {
            throw new IllegalArgumentException("Expected exception");
        });

        t.onFailure(s -> {
            throw new IllegalArgumentException("Should be thrown.");
        });
    }

    @Test(expected = IllegalArgumentException.class)
    public void itShouldThrowNewExceptionWhenInvokingOrElseThrowOnFailure() throws Throwable {
        Try<String> t = Try.ofFailable(() -> {
            throw new Exception("Oops");
        });

        t.<IllegalArgumentException>orElseThrow(() -> {
            throw new IllegalArgumentException("Should be thrown.");
        });
    }

    public void itShouldNotThrowNewExceptionWhenInvokingOrElseThrowOnSuccess() throws Throwable {
        Try<String> t = Try.ofFailable(() -> "Ok");

        String result = t.<IllegalArgumentException>orElseThrow(() -> {
            throw new IllegalArgumentException("Should be thrown.");
        });

        assertEquals(result, "Ok");
    }

}

```

### 18、对Map优雅操作
``` java
map.forEach((id, val) -> System.out.println(val));
map.getOrDefault(42, "not found"); 

// Map<String, List<Article>>
// 1.修改value
L1: map.put("Java", sortValue(map.get("Java")));
L2: map.compute("Java", (key, value) -> sortValue(value));  

// 2.key存在的情况下修改
L1:
if (map.containsKey("Java")) {  
  map.put("Java", sortValue(map.get("Java")));
}
L2: map.computeIfPresent("Java", (key, value) -> sortValue(value));  

L1:
List javaArticleList = map.get("java");
javaArticleList.removeIf(article -> article.getName().equals("web"));
if (javaArticleList.size() == 0) {
  map.remove("java");
}

L2:
map.computeIfPresent("java", (key, javaArticleList) -> javaArticleList.removeIf(article -> article.getName().equals("web")) && javaArticleList.size() == 0 ? null : javaArticleList);




// 3.key不存在的情况下添加数据
L1:
if (!map.containsKey("Java")) {  
  map.put("Java", javaArticle);
}
L2:
map.putIfAbsent("Java", javaArticle);
map.computeIfAbsent("Java", this::fetchArticle);
map.computeIfAbsent("java", javaArticleList -> new ArrayList<>()).add(new Article("java"));

// 4.与已有数据合并
L1:
if (map.containsKey("Java")) {  
  map.get("Java").addAll(javaArticles);
} else {
  map.put("Java", javaArticles);
}
L2:
map.merge("Java", javaArticles, (list1, list2) ->  
  Stream.of(list1, list2)
    .flatMap(Collection::stream)
    .collect(Collectors.toList()));
    
Map<Long, Double> bookScoreMap;
bookScoreMap.merge(bookId, score, Math::max);
bookCorreMap.merge(bp.getBookId(), bookCorBean, (oldBookCor, newBookCor) ->
  oldBookCor.getCorrelation() - newBookCor.getCorrelation() < 0 ? newBookCor : oldBookCor);
  
Map<CategoryEnum, List<Long>> screenBookListMap =
                bookDetailRepository.batchQueryBookDetailList(Lists.newArrayList(bookIdSet))
                        .stream().collect(Collectors.toMap(
                        key -> CategoryEnum.getCategoryById(key.getCate1Id()),
                        value -> Lists.newArrayList(value.getBookId()),
                        (List<Long> list1, List<Long> list2) ->
                                Stream.of(list1, list2).flatMap(Collection::stream).collect(Collectors.toList())));  

// 5.替换map所有的value
L1:
for (String key : map.keys()) {  
  map.put(key, getUpdatedListFor(key));
}
L2:
map.replaceAll((key, val) -> getUpdatedListFor(key));  

// 6. 对map value求和
int sum = map.values().stream().mapToInt(Integer::intValue).sum();
```

### 19、获取最大日期的对象
``` java
Contact lastContact = Collections.max(contacts, Comparator.comparing(c -> c.lastUpdated));
```

### 20、ThrowingFunction
``` java
/**
 * Represents a function that accepts one argument and returns a value;
 * Function might throw a checked exception instance.
 *
 * @param <T> the type of the input to the function
 * @param <R> the type of the result of the function
 * @param <E> the type of the thrown checked exception
 */
@FunctionalInterface
public interface ThrowingFunction<T, R, E extends Throwable> {

    R apply(T arg) throws E;

    /**
     * @param <T> type
     * @param <E> checked exception
     * @return a function that accepts one argument and returns it as a value.
     */
    static <T, E extends Exception> ThrowingFunction<T, T, E> identity() {
        return t -> t;
    }

    /**
     * @return a Function that returns the result of the given function as an Optional instance.
     * In case of a failure, empty Optional is returned
     */
    static <T, R, E extends Exception> Function<T, Optional<R>> lifted(ThrowingFunction<T, R, E> f) {
        Objects.requireNonNull(f);
        return f.lift();
    }

    static <T, R, E extends Exception> Function<T, R> unchecked(ThrowingFunction<T, R, E> f) {
        Objects.requireNonNull(f);
        return f.uncheck();
    }

    default <V> ThrowingFunction<V, R, E> compose(final ThrowingFunction<? super V, ? extends T, E> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    default <V> ThrowingFunction<T, V, E> andThen(final ThrowingFunction<? super R, ? extends V, E> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

    /**
     * @return a Function that returns the result as an Optional instance. In case of a failure, empty Optional is
     * returned
     */
    default Function<T, Optional<R>> lift() {
        return t -> {
            try {
                return Optional.of(apply(t));
            } catch (Throwable e) {
                return Optional.empty();
            }
        };
    }

    /**
     * @return a new Function instance which wraps thrown checked exception instance into a RuntimeException
     */
    default Function<T, R> uncheck() {
        return t -> {
            try {
                return apply(t);
            } catch (final Throwable e) {
                throw new WrappedException(e);
            }
        };
    }
}

// test
final ThrowingFunction<Integer, Integer, Exception> f1 = i -> 2 * i;
final ThrowingFunction<Integer, Integer, Exception> f2 = i -> i + 10;
final Integer result = f1.compose(f2).apply(3); // 26
final Integer result = f1.andThen(f2).apply(3); // 16
final Optional<Integer> result = f1.lift().apply(42);  
f1 = givenThrowingFunction("custom exception message");
f1.apply(42); // ThrowEx
f1.uncheck().apply(42); // WrapInRuntimeEx
// WrapInRuntimeExWhenUsingUnchecked
Stream.of(". .").map(unchecked(URI::new)).collect(toList());
// WrapInOptionalWhenUsingStandardUtilsFunctions
final Long result = Stream.of(". .")
                .map(lifted(URI::new))
                .filter(Optional::isPresent)
                .count();

```

### 21、ThrowingBiFunction
``` java
/**
 * Represents a function that accepts two arguments and produces a result.
 * This is the two-arity specialization of {@link ThrowingFunction}.
 * Function may throw a checked exception.
 *
 * @param <T1> the type of the first argument to the function
 * @param <T2> the type of the second argument to the function
 * @param <R> the type of the result of the function
 * @param <E> the type of the thrown checked exception
 *
 * @see ThrowingFunction
 */
@FunctionalInterface
public interface ThrowingBiFunction<T1, T2, R, E extends Throwable> {

    R apply(T1 arg1, T2 arg2) throws E;

    static <T1, T2, R, E extends Throwable> BiFunction<T1, T2, R> unchecked(ThrowingBiFunction<T1, T2, R, E> function) {
        Objects.requireNonNull(function);
        return function.unchecked();
    }

    static <T1, T2, R, E extends Exception> BiFunction<T1, T2, Optional<R>> lifted(ThrowingBiFunction<T1, T2, R, E> f) {
        Objects.requireNonNull(f);
        return f.lift();
    }

    /**
     * Performs provided action on the result of this ThrowingBiFunction instance
     * @param after action that is supposed to be made on the result of apply()
     * @param <V> after function's result type
     * @return combined function
     */
    default <V> ThrowingBiFunction<T1, T2, V, E> andThen(final ThrowingFunction<? super R, ? extends V, E> after) {
        Objects.requireNonNull(after);
        return (arg1, arg2) -> after.apply(apply(arg1, arg2));
    }

    default BiFunction<T1, T2, R> unchecked() {
        return (arg1, arg2) -> {
            try {
                return apply(arg1, arg2);
            } catch (final Throwable e) {
                throw new WrappedException(e);
            }
        };
    }

    default BiFunction<T1, T2, Optional<R>> lift() {
        return (arg1, arg2) -> {
            try {
                return Optional.of(apply(arg1, arg2));
            } catch (Throwable e) {
                return Optional.empty();
            }
        };
    }
}
// test
final ThrowingBiFunction<Integer, Integer, Integer, Exception> f1 = (i, j) -> i * j;
final ThrowingFunction<Integer, Integer, Exception> f2 = i -> i + 10;
final Integer result = f1.andThen(f2).apply(2, 2);  // 14
final Optional<Integer> result = f1.lift().apply(2, 2);
final ThrowingBiFunction<Integer, Integer, Integer, Exception> f1 = (i, j) -> { throw new Exception("some message"); };
f1.unchecked().apply(42, 42);

```

### 22、ifPresentOrElse的替代
``` java
public class OptionalConsumer<T> {

    private Optional<T> optional;

    private OptionalConsumer(Optional<T> optional) {
        this.optional = optional;
    }

    public static <T> OptionalConsumer<T> of(Optional<T> optional) {
        return new OptionalConsumer<>(optional);
    }

    public OptionalConsumer<T> ifPresent(Consumer<T> c) {
        optional.ifPresent(c);
        return this;
    }

    public OptionalConsumer<T> ifNotPresent(Runnable r) {
        if (!optional.isPresent())
            r.run();
        return this;
    }

}

Optional<String> optional = Optional.ofNullable("dfssdf");
OptionalConsumer.of(optional).ifPresent(s ->System.out.println("isPresent "+s))
                .ifNotPresent(() -> System.out.println("! isPresent"));

```

### 23、本地缓存
``` java
static Map<Integer, Integer> cache = new ConcurrentHashMap<>();
static int fibonacci(int i) {
    if (i == 0) return 0;
    if (i == 1) return 1;
    // 通过Map.computeIfAbsent()方法，可以在key所对应的value值不存在的情况下，计算一个新的value值
    return cache.computeIfAbsent(i, (key) -> {
        System.out.println("Slow calculation of " + key);
        return fibonacci(i - 2) + fibonacci(i - 1);
    });
}
```

### 24、Base64
``` java
// 编码
String asB64 = Base64.getEncoder().encodeToString("hello world".getBytes("utf-8"));
System.out.println(asB64);
// 解码
byte[] asBytes = Base64.getDecoder().decode("aGVsbG8gd29ybGQ=");
System.out.println(new String(asBytes, "utf-8"));

// URL编码
String urlEncoded = Base64.getUrlEncoder()
                          .encodeToString("https://www.baidu.com/s?wd=v&rsv_spt=1".getBytes("utf-8"));
System.out.println("Using URL Alphabet: " + urlEncoded);
```

### 25、Java日期时间

|类名|描述|
|:--|:--|
|Instant|代表的是时间戳|
|LocalDate|不包含具体时间的日期，比如2017-01-14|
|LocalTime|代表的是不含日期的时间|
|LocalDateTime|包含了日期及时间，不过没有偏移信息或者时区|
|ZonedDateTime|包含时区的完整的日期时间，偏移量是以UTC/格林威治时间为基准的|
|Period|时间段|
|Duration|持续时间，时间差|

``` java
// 获取当天的日期，只包含日期，没有时间 2017-02-05
LocalDate today = LocalDate.now();
// 获取当前的年月日
int year = today.getYear(); 
int month = today.getMonthValue(); 
int day = today.getDayOfMonth();
// 获取某个特定的日期
LocalDate dateOfBirth = LocalDate.of(2010, 01, 14); 
// 检查两个日期是否相等
LocalDate date1 = LocalDate.of(2017, 01, 14); 
if (date1.equals(today)){ 
    System.out.printf("Today %s and date1 %s are same date %n", today, date1); 
} 
// 一周后的时间
LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);
LocalDate previousYear = today.minus(1, ChronoUnit.YEARS);
// isBefore isAfter
LocalDate yesterday = today.minus(1, ChronoUnit.DAYS);
yesterday.isBefore(today)
// 获取当前时间
LocalTime time = LocalTime.now();
// 两个日期之间包含多少天，多少个月
LocalDate java8Release = LocalDate.of(2017, Month.MARCH, 14); 
Period release = Period.between(today, java8Release); 
// 获取当前时间戳 Date.from(Instant)  Date.toInstant()
Instant timestamp = Instant.now(); 
Instant specificTime = Instant.ofEpochMilli(timestamp.toEpochMilli());
// 日期进行解析/格式化
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd HH:mm:ss.SSSSSS Z");
String s = ZonedDateTime.now().format(formatter);
String str = "2017-04-08 12:30:34";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime dateTime = LocalDateTime.parse(str, formatter);
// LocalDateTime
LocalDateTime today = LocalDateTime.of(LocalDate.now(), LocalTime.now());
LocalDateTime specificDate = LocalDateTime.of(2017, Month.JANUARY, 1, 10, 10, 30);
// Duration
// A duration of 3 seconds and 5 nanoseconds
Duration duration = Duration.ofSeconds(3, 5);
Duration oneDay = Duration.between(today, yesterday);

```

### 26、Collectors.toMap使用
``` java

Map<Long, Detail> detailMap = load();
ConcurrentHashMap<Long, ItemBean> itemBeanMap = detailMap.entrySet().stream()
                .map(entry -> new AbstractMap.SimpleImmutableEntry<>(entry.getKey(), this.buildItem(entry.getValue())))
                .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (t1, t2) -> t2, ConcurrentHashMap::new));

Map<Integer, String> map = tasks.stream()
        .collect(Collectors.toMap(Task::getId, Task::getName, (x, y) -> x+", "+ y));
// 避免重复的键时抛出异常
Map<Integer, String> map =  tasks.stream().collect(Collectors.toMap(Task::getTitle, identity(), (t1, t2) -> t2));
    
```

### 27、Collectors
``` java
CopyOnWriteArrayList<Foo> conList = itemList.stream()
                .collect(Collectors.toCollection(CopyOnWriteArrayList::new));
		
Map<Boolean, List<Employee>> employeeMap = employeeList.stream()
          .collect(Collectors.partitioningBy((Employee emp) -> emp.getAge() > 30));

Map<Boolean, Long> employeeMapCount =employeeList.stream()
         .collect(Collectors.partitioningBy((Employee emp) -> (emp.getAge() > 30), Collectors.counting()));

Map<Department,Set<Employee>> employeeMap= employeeList.stream()
        .collect(Collectors.groupingBy(Employee::getDepartment, TreeMap::new, Collectors.toSet()));

Optional<Employee> maxSalaryEmp = employeeList.stream()
            .collect(Collectors.maxBy(Comparator.comparing(Employee::getSalary)));
Optional<Employee> minAgeEmp = employeeList.stream()
            .collect(Collectors.minBy(Comparator.comparing(Employee::getAge)));

String joinedStr = Stream.iterate(new Integer(0), (Integer integer) -> integer + 1)
              .limit(5)
              .map(integer -> integer.toString())
              .collect(Collectors.joining(",","{","}"));

Double avgAge = employeeList.stream().collect(Collectors.averagingInt(Employee::getAge));
Double avgSalary = employeeList.stream().collect(Collectors.averagingDouble(Employee::getSalary));

List<String> employeeNames = employeeList.stream().collect(Collectors.mapping(Employee::getName, Collectors.toList()));

String avgSalary = employeeList.stream()
		.collect(Collectors.collectingAndThen(Collectors.averagingDouble(Employee::getSalary), averageSalary -> new DecimalFormat("'$'0.00").format(averageSalary)));
		

```

### 28、list最大值
``` java
List<Integer> CENTERS_ROOKIE_YEAR = Lists.newArrayList(1946, 1988, 1970, 1931, 1940, 1920, 1980, 1953, 1960, 1974, 1987);
Integer maxElement = Collections.max(CENTERS_ROOKIE_YEAR);
Integer maxElement = CENTERS_ROOKIE_YEAR.stream().mapToInt(p -> p).max().getAsInt();
Integer maxElement = CENTERS_ROOKIE_YEAR.stream().reduce(Integer::max).get();
Integer maxElement = Ordering.natural().max(CENTERS_ROOKIE_YEAR);
```

### 29、map最大值
``` java
Comparator<? super Entry<Integer, Integer>> maxValueComparator = (entry1, entry2) -> entry1.getValue().compareTo(entry2.getValue());
Entry<Integer, Integer> maxValue = mapValues.entrySet().stream().max(maxValueComparator).get();
```



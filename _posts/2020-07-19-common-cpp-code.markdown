---
layout:     post
title:      "C++ 必要代码片段"
date:       2020-07-19 14:10:00
author:     "Daniel"
header-img: "img/post-bg-cpp.jpg"
tags:
    - C++

---


#### 1、数组长度
``` c++
#if !defined(ARRAY_SIZE)
#define ARRAY_SIZE(x) (sizeof((x)) / sizeof((x)[0]))
#endif

uint32_t percents[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
ARRAY_SIZE(percents)
```

#### 2、二分查找
``` c++
// std::lower_bound 在first和last中的前闭后开区间进行二分查找，返回大于或等于val的第一个元素的iterator位置。如果所有元素都小于val，则返回last的iterator位置。
#include <algorithm>
float percentLineNorm(uint32_t *percents, size_t size, uint32_t value)
{
    auto it = std::lower_bound(percents, percents + size, value);
    auto diff = std::distance(percents, it);
    if (diff == 0)
    {
        return 0;
    }
    else if ((diff + 1) >= static_cast<long>(size))
    {
        return 1;
    }
    else
    {
        auto v = 1.0 * diff / size - (1.0 * percents[diff] - value) / (size * (1.0 * percents[diff] - percents[diff - 1]));
        return v;
    }
}

auto pct = percentLineNorm(percents, ARRAY_SIZE(percents), 7);
std::cout << pct << std::endl;

```

#### 3、日期和时间库
``` c++
#include <chrono>
// 时间间隔Duration
std::chrono::milliseconds ms{3}; 
std::chrono::microseconds us = 2 * ms; 
// 获取时间间隔的时钟周期个数
std::cout <<  "3 ms duration has " << ms.count() << " ticks\n"<<  "6000 us duration has " << us.count() << " ticks\n";

// std::ratio代表的是一个分子除以分母的分数值，比如ratio<2>代表一个时钟周期是两秒，ratio<60>代表了一分钟，ratio<60*60>代表一个小时，ratio<60*60*24>代表一天。
// 30Hz clock using fractional ticks
std::chrono::duration<double, std::ratio<1, 30>> hz30(3.5);
std::chrono::minutes t1(10);
std::chrono::seconds t2(60);
std::chrono::seconds t3 = t1 - t2;
std::cout << t3.count() << " second" << std::endl;
std::cout << chrono::duration_cast<chrono::minutes>(t3).count() <<” minutes”<< endl;

// 时间点Time point
// time_point表示一个时间点，用来获取1970.1.1以来的秒数和当前的时间
typedef std::chrono::duration<int, std::ratio<60 * 60 * 24>> days_type;
std::chrono::time_point<std::chrono::system_clock, days_type> today = std::chrono::time_point_cast<days_type>(std::chrono::system_clock::now());
std::cout << today.time_since_epoch().count() << " days since epoch" << std::endl;

uint32_t timePoint2Seconds(const std::chrono::system_clock::time_point &time)
{
    auto microSeconds = std::chrono::duration_cast<std::chrono::microseconds>(time.time_since_epoch()).count();
    return static_cast<uint32_t>(microSeconds / 1000 / 1000);
}

// 时钟Clocks
// 表示当前的系统时钟，内部有time_point, duration, Rep, Period等信息，它主要用来获取当前时间，以及实现time_t和time_point的相互转换。
std::chrono::steady_clock::time_point t1 = std::chrono::system_clock::now();
std::cout << "Hello World\n";
std::chrono::steady_clock::time_point t2 = std::chrono:: system_clock::now();
std::cout << (t2-t1).count()<<" tick count " << std::endl;
cout << std::chrono::duration_cast<std::chrono::microseconds>(t2 - t1).count() <<"microseconds";

// 计算耗时
using clk = std::chrono::system_clock;
auto toMs = [](clk::duration duration) -> uint64_t {
    return std::chrono::duration_cast<std::chrono::microseconds>(duration).count() / 1000;
};
auto start = clk::now();
// run code XXXX
auto end = clk::now();
uint64_t elpased = toMs(end - start);

// time_point转换为ctime：
std::time_t now_c = std::chrono::system_clock::to_time_t(time_point);
// ctime 转 time_point
std::chrono::system_clock::time_point time_point = std::chrono::system_clock::from_time_t(now_c);

// 时间的格式化输出
#include <chrono>
#include <ctime>
#include <iomanip>
auto t = std::chrono::system_clock::to_time_t(std::chrono::system_clock::now());
std::cout << std::put_time(std::localtime(&t), "%Y-%m-%d %X") << std::endl;
std::cout << std::put_time(std::localtime(&t), "%Y-%m-%d %H.%M.%S") << std::endl;

```

#### 4、unordered_set
``` c++
// set和unordered_set底层分别是用红黑树和哈希表实现的
#include <unordered_set>

std::unordered_set<int> s1;  // 不带任何参数
std::unordered_set<int> s2 {1, 3, 5, 7};  // 初始集合元素
std::set<string> s3 {"abcc", "123", "978"};
std::unordered_set<string> s4(s3.begin(), s3.end());  // 复制
std::set<string, greater<>> s5;  // 默认是从小到大排序，这里变成从大到小排序

// 遍历
std::unordered_set<int> us {1, 2, 3, 4};
for (auto it = std::begin(us); it != std::end(us); it++) {
    std::cout << *it << " ";
}
auto it = std::begin(us), end = std::end(us);
while (std::distance(it, end) != 0) {
    std::cout << *it << " ";
    it = std::next(it);   
}

std::unordered_set<int> c;
// 普通插入,返回pair<迭代器, 插入是否成功>
pair<unordered_set<int>::iterator, bool> insert_ret = c.insert(1);
std::cout << "指向key的迭代器" << *insert_ret.first << "是否成功" << insert_ret.second << std::endl;

//按指定区域插入
std::unordered_set<int> c2;
c2.insert(c.begin(), c.end());

//构造插入
std::unordered_set<int> c3;
c3.emplace(1);
c3.emplace(2);
c3.emplace(3);

//迭代器插入
std::unordered_set<int> c4;
c4.emplace_hint(c4.begin(), 1);
c4.emplace_hint(c4.begin(), 2);
c4.emplace_hint(c4.begin(), 3);

//删除
//指定位置删除
c4.erase(c4.begin());
//指定key删除
c4.erase(3);
//指定区域删除
c4.erase(c4.begin(), c4.end());

//交换
c.swap(c2);

// 查找
std::unordered_set<int>::iterator find_iter = c4.find(2);
if (find_iter != c4.end()){
    std::cout << "找到元素 : " << *find_iter << std::endl;
} else {
    std::cout << "没找到 !" << std::endl;
}
std::cout << "value出现次数 :" << c4.count(2) << std::endl; //set key不可重复


std::unordered_set<int>::hasher func = c.hash_function();
std::unordered_set<int>::key_equal equal = c.key_eq();
std::cout << "hash_func: " << func(11111) << std::endl;
std::cout << "key_eq: " << equal(11111, 11111) << std::endl;

```

#### 5、map
``` c++
// std::map 通常由二叉搜索树实现。
// 默认构造，构造一个空的map
std::map<char, int> m1;
m1['a'] = 10;
m1['b'] = 30;
m1['c'] = 50;
m1['d'] = 70;
// 由一对范围迭代器指定输入
std::map<char, int> m2(m1.begin(), m1.end());
// 复制构造
std::map<char, int> m3(m2);
// 指定比较器：使用类
std::map<char, int, classcomp> m4; 
// 指定比较器：使用函数指针
bool (*fn_pt)(char, char) = fncomp;
std::map<char, int, bool (*)(char, char)> m5(fn_pt); 

// 插入
std::map<char, int> mymap;
// 插入单个元素
mymap.insert(std::pair<char, int>('a', 100));
mymap.insert(std::pair<char, int>('z', 200));
std::pair<std::map<char, int>::iterator, bool> ret;
ret = mymap.insert(std::pair<char, int>('z', 500));
if (ret.second == false) {
    std::cout << "element 'z' already existed";
    std::cout << " with a value of " << ret.first->second << '\n';
}
// 带插入位置提示，只是提示，并不强制插入此位置
std::map<char, int>::iterator it = mymap.begin();
mymap.insert(it, std::pair<char, int>('b', 300));
mymap.insert(it, std::pair<char, int>('c', 400));
// 由一对范围迭代器指定输入
std::map<char, int> anothermap;
anothermap.insert(mymap.begin(), mymap.find('c'));

// 遍历
for (std::map<char,int>::iterator it = mymap.begin(); it != mymap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

// 删除
std::map<char,int>::iterator it;
std::map<char, int>::iterator it = mymap.find('b');
mymap.erase(it);  // 删除迭代器指向的元素
mymap.erase('c');  // 删除相应键值的元素
it = mymap.find('e');
mymap.erase(it, mymap.end());  // 删除一个范围内的所有元素

// 查找
std::map<char, int>::iterator it = mymap.find('b');
if (it != mymap.end())
    mymap.erase (it);
// print content:
std::cout << "a => " << mymap.find('a')->second << '\n';

// 范围查询
std::map<char,int>::iterator itLow = mymap.lower_bound ('b'); // 等于或大于'b'
std::map<char,int>::iterator itUp = mymap.upper_bound ('d');   // 大于'd'
mymap.erase(itLow, itUp);        // erases [itLow, itUp)

```

#### 6、unordered_map 
``` c++
#include <string>
#include <unordered_map>

// 初始化
typedef std::unordered_map<std::string, std::string> stringmap;
std::unordered_map<std::string, std::string> m1; // empty
std::unordered_map<std::string, std::string> m2({{"apple", "red"}, {"lemon", "yellow"}});      // init list
std::unordered_map<std::string, std::string> m3({{"orange", "orange"}, {"strawberry", "red"}}); // init list
std::unordered_map<std::string, std::string> m4(m2);                                       // copy
std::unordered_map<std::string, std::string> m5(m4.begin(), m4.end());                  // range
for (auto &x : m5)
    std::cout << " " << x.first << ":" << x.second;

// 迭代器操作
std::unordered_map<std::string, std::string> mymap;
mymap = {{"Australia", "Canberra"}, {"U.S.", "Washington"}, {"France", "Paris"}};
for (auto it = mymap.cbegin(); it != mymap.cend(); ++it)
    std::cout << " " << it->first << ":" << it->second; // cannot modify *it


for (unsigned i = 0; i < mymap.bucket_count(); ++i)
{
    std::cout << "bucket #" << i << " contains:";
    for (auto local_it = mymap.cbegin(i); local_it != mymap.cend(i); ++local_it)
        std::cout << " " << local_it->first << ":" << local_it->second;
    std::cout << std::endl;
}

// 访问
std::unordered_map<std::string, int> mymap = {
        {"Mars", 3000}, {"Saturn", 60000},  {"Jupiter", 70000}};
// 使用方括号（[]）：如果 k 匹配容器中某个元素的键，则该函数返回该映射值的引用。如果 k 与容器中任何元素的键都不匹配，则该函数将使用该键插入一个新元素，并返回该映射值的引用。
// 使用at：如果 k 匹配容器中某个元素的键，则该函数返回该映射值的引用。如果 k 与容器中任何元素的键都不匹配，则该函数将抛出 out_of_range 异常。
mymap["Mars1"] = 3396;
mymap.at("Saturn") += 272;
mymap.at("Jupiter") = mymap.at("Saturn") + 9638;

// 插入
std::unordered_map<std::string,double> myrecipe, mypantry = {{"milk",2.0},{"flour",1.5}};
std::pair<std::string,double> myshopping ("baking powder",0.3);
myrecipe.insert(myshopping);                        // copy insertion
myrecipe.insert(std::make_pair<std::string,double>("eggs",6.0)); // move insertion
myrecipe.insert(mypantry.begin(), mypantry.end());  // range insertion
myrecipe.insert({{"sugar",0.8},{"salt",0.1}});    // initializer list insertion

// 删除
mymap.erase(mymap.begin());                    // erasing by iterator
mymap.erase("France");                         // erasing by key
mymap.erase(mymap.find("China"), mymap.end()); // erasing by range


// 查找
std::unordered_map<std::string, std::string>::const_iterator got = mymap.find("France");
if (got == mymap.end())
  std::cout << "not found";
else
  std::cout << got->first << " is " << got->second;

```

#### 7、vector
``` c++
// 大小可动态改变的顺序容器
#include<vector>
// 定义初始化
std::vector<int> v1{1,2,3};
std::vector<std::vector<int>> v2{{1,2,3},{4,5,6}};
std::vector<int> v3(10);     //初始化了10个默认值为0的元素
std::vector<int> v4(10,1);   //初始化了10个值为1的元素
int a[5] = { 1, 2, 3, 4, 5 };
std::vector<int> v5(a, a + 5); //通过数组a的地址下标初始化
std::vector<int> v6(v5); // 通过c1初始化
std::vector<int> c7(c1.begin(), c1.begin() + 3);

// 写入删除
v1.push_back(1);     
v1.pop_back(); 
v1.insert(v1.end(), 4);
v1.erase(v1.begin() + 1);
v1.erase(v1.begin(), v1.begin() + 2);
v1.assign(2, 10);
// 第二个位置后边插入5
v.insert(std::next(std::begin(v), 2), 5);
v.insert(std::begin(v) + 2, 5);

// 访问      
v1.at(1)
std::vector<int>::iterator it = v1.begin();
std::cout << *it << *(it + 1) << std::endl;
std::vector<int>::iterator it1 = v1.end();
std::cout << *(it1 - 1) << std::endl;
for(auto it = v1.begin(); it != v1.end(); it++)
{
    std::cout << (*it) << ' ';
}
std::cout << v1.front() << v1.back() << std::endl;

// 排序
#include<algorithm>
int a[10] = { 9,6,3,8,5,2,7,4,1,0 };
sort(a, a + 10, std::less<int>()); // 升序
sort(a, a + 10, std::greater<int>()); // 降序

bool comp(const int &a, const int &b){
    return a < b;
}
sort(v1.begin(), v1.end(), comp);
// 对象排序
std::vector<std::pair<size_t, float>> scores;
std::sort(std::begin(scores), std::end(scores), [](const auto &x, const auto &y) {
        return x.second > y.second;
    });


// 去重
std::vector<int> v{3, 4, 5, 1, 2, 5, 3};
sort(v.begin(), v.end());
//pos是去重以后vector中没有重复元素的下一个位置的迭代器。
std::vector<int>::iterator pos = unique(v.begin(), v.end());
v.erase(pos, v.end());

// reserve capacity=10 size=0
std::vector<int> v1;
v1.reserve(10);
// resize capacity=10 size=10 默认用0填充
std::vector<int> v2;
v2.resize(10);
std::mt19937 rand;
std::shuffle(std::begin(v1), std::end(v1), rand);

// map转pair后排序 
using PairVector = std::vector<std::pair<uint64_t, uint64_t>>;
std::unordered_map<uint64_t, uint64_t> tagMap{{11, 22}, {1111, 2222}, {111, 222}};
PairVector tagsVec(std::make_move_iterator(std::begin(tagMap)), std::make_move_iterator(std::end(tagMap)));
auto compare = [](const PairVector::value_type &prev, const PairVector::value_type &next) {
    return prev.second > next.second;
};
std::sort(tagsVec.begin(), tagsVec.end(), compare);

// inserter
std::vector<int> d {100, 200, 300};
std::vector<int> v {1, 2, 3, 4, 5};
std::copy(d.begin(), d.end(), std::inserter(v, std::next(v.begin())));
// v: 1 100 200 300 2 3 4 5
```

#### 8、类型转换
``` c++
#include <string>
// 字符串转数字
std::string str_hex = "40c3";
std::string str_bin = "-10010110001";
std::string str_auto = "0x7f";
int i_hex = std::stoi(str_hex, nullptr, 16);
int i_bin = std::stoi(str_bin, nullptr, 2);
int i_auto = std::stoi(str_auto, nullptr, 0);

long stol (const string&  str, size_t* idx = 0, int base = 10);
unsigned long stoul (const string&  str, size_t* idx = 0, int base = 10);
long long stoll (const string&  str, size_t* idx = 0, int base = 10);
unsigned long long stoull (const string&  str, size_t* idx = 0, int base = 10);
float stof (const string&  str, size_t* idx = 0);
double stod (const string&  str, size_t* idx = 0);
long double stold (const string&  str, size_t* idx = 0);

std::string orbits("686.97 365.24");
std::string::size_type sz; // alias of size_t
float mars = std::stof(orbits, &sz);
float earth = std::stof(orbits.substr(sz));

// 字符串转char*
std::string str = "hello world";
const char *p = str.data();
const char *p = str.c_str();
const char* p = "Hello world";
std::string str = p;

const char* pBuf = "hello world";
const unsigned char * pTmp = (const unsigned char *)pBuf;

```

#### 8、智能指针
``` c++
#include <memeory>

std::unique_pt<Peron> test()
{
    return std::unique_ptr<Person>(new Person());
}
// 只能有一个对象拥有这个所有权
std::unique_ptr<Person> p1 = test();
p1 -> str = "hello";
// 所有权转移到p2
std::unique_ptr<Person> p2 = std::move(p1);

p2.reset(new Person);
std::unique_ptr<Person> p1 = std::make_unique<Person>();

template<typename T>
stuct Node {
    T data;
    // Node* next;
    std::unique_ptr<Node<T>> next;
}
template<typename T>
class LinkedList {
public:
    void front(const T& data) 
    {
        auto node = std::make_unique<Node<T>>();
        node -> data = data;
        node -> next = std::move(head.next);
        head.next = std::move(node); 
    }
    void print() 
    {
        Node<T>* node = head.next.get();
        while (node) {
            std::cout << node -> data << " ";
            node = node -> next.get();
        }
    }
private:
    Node<T> head;
};

std::shared_ptr<int> p1 = std::make_shared<int>(); // 使用 make_shared 创建空对象
*p1 = 78;
std::cout << "p1 = " << *p1 << std::endl; // 输出78
std::shared_ptr<int> p2(p1); // 指向同一个指针
p1.reset(); // 无参数调用reset，无关联指针，引用个数为0
std::cout << "p1 Reference Count = " << p1.use_count() << std::endl;
p1.reset(new int(11)); // 带参数调用reset，引用个数为1
p1 = nullptr; // 把对象重置为NULL，引用计数为0
if (!p1)
{
    std::cout << "p1 is NULL" << std::endl; // 输出
}


```

#### 9、匿名函数
``` c++
// [捕获列表](参数列表) -> 返回类型 {函数体}
int c = 12;
auto add = [c](int a, int b) -> int {
    return c;
};
auto add = [&c](int a, int b) -> int {
    c = a;
    return c;
};

```

#### 10、future
``` c++
#include <iostream>
#include <future>
#include <thread>

auto get_value = []() { return 10; };
std::future<int> foo; // default-constructed
std::future<int> bar = std::async(get_value); // move-constructed
int x = bar.get();
int x2 = bar.get(); // 错误, 对于每个future的共享状态，get函数最多仅被调用一次

// shared_future 可被多次访问
std::future<int> fut = std::async([]() { return 10; });
std::shared_future<int> shfut = fut.share();
std::cout << "fut valid: " << fut.valid() << '\n';// 0
std::cout << "value: " << shfut.get() << shfut.get() * 2 << '\n'; // 10

// wait
auto is_prime = [](int x) {
    for (int i = 2; i < x; ++i) if (x%i == 0) return false;
    return true;
};
std::future<bool> fut = std::async(is_prime, 194232491);
// wait (1)等待共享状态就绪；(2)如果共享状态尚未就绪，则该函数将阻塞调用的线程直到就绪；(3)当共享状态就绪后，则该函数将取消阻塞并void返回。
fut.wait();
if (fut.get()) std::cout << "is prime.\n";
else std::cout << "is not prime.\n";

std::chrono::milliseconds span(100);
// 可能多次调用std::future::wait_for函数
while (fut.wait_for(span) == std::future_status::timeout) 
    std::cout << '.';
fut.get();


```

#### 11、结构体对象创建
``` c++
struct Student { 
    std::string name;
    char sex;
    int age;
};
Student stu = {"aa" ,'m', 21};
std::cout << stu.name << " " << stu.sex << " " << stu.age << "\n";
auto stu2 = Student{.name = "aa", .sex = 'm'};
std::cout << stu2.name << " " << stu2.sex << " " << stu2.age << "\n";
```

#### 12、 transform
``` c++
std::string s("hello");
std::transform(s.begin(), s.end(), s.begin(),
               [](unsigned char c) -> unsigned char { return std::toupper(c); });
// s -> HELLO
std::vector<std::size_t> ordinals;
std::transform(s.begin(), s.end(), std::back_inserter(ordinals),                [](unsigned char c) -> std::size_t { return c; });
std::transform(s.begin(), s.end(), std::inserter(ordinals, ordinals.begin()),
               [](unsigned char c) -> std::size_t { return c; });
// s -> {72 69 76 76 79}
std::transform(ordinals.cbegin(), ordinals.cend(), ordinals.cbegin(),
                   ordinals.begin(), std::plus<>{});
// s -> {144 138 152 152 158}
```

#### 13、random
``` c++
#include <random>
#include <algorithm>
#include <iterator>
#include <iostream>
std::vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
std::random_device rd;
std::mt19937 g(rd());
std::shuffle(v.begin(), v.end(), g);
std::copy(v.begin(), v.end(), std::ostream_iterator<int>(std::cout, " "));

std::random_device rd;  
std::mt19937 gen(rd());
// [0-9]之间的随机数
int rnd = std::uniform_int_distribution<> dis(0, 9)(gen);
```

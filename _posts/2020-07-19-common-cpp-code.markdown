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
``` cpp
#if !defined(ARRAY_SIZE)
#define ARRAY_SIZE(x) (sizeof((x)) / sizeof((x)[0]))
#endif

uint32_t percents[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
ARRAY_SIZE(percents)
```

#### 2、二分查找
``` cpp
// std::lower_bound 在first和last中的前闭后开区间进行二分查找，返回大于或等于val的第一个元素的iterator位置。如果所有元素都小于val，则返回last的iterator位置。
#include <algorithm>

float percentLineNorm(uint32_t *percents, size_t size, uint32_t value) {
    auto it = std::lower_bound(percents, percents + size, value);
    auto diff = std::distance(percents, it);
    if (diff == 0) {
        return 0;
    } else if ((diff + 1) >= static_cast<long>(size)) {
        return 1;
    } else {
        auto v = 1.0 * diff / size - (1.0 * percents[diff] - value) / (size * (1.0 * percents[diff] - percents[diff - 1]));
        return v;
    }
}

auto pct = percentLineNorm(percents, ARRAY_SIZE(percents), 7);
std::cout << pct << std::endl;

```

#### 3、日期和时间库
``` cpp
#include <chrono>

// 时间间隔Duration
std::chrono::milliseconds ms{3}; 
std::chrono::microseconds us = 2 * ms; 
// 获取时间间隔的时钟周期个数
std::cout <<  "3 ms duration has " << ms.count() << " ticks\n"<<  "6000 us duration has " << us.count() << " ticks\n";
std::this_thread::sleep_for(std::chrono::milliseconds(100));

// std::ratio代表的是一个分子除以分母的分数值，比如ratio<2>代表一个时钟周期是两秒，ratio<60>代表了一分钟，ratio<60*60>代表一个小时，ratio<60*60*24>代表一天。
// 30Hz clock using fractional ticks
std::chrono::duration<double, std::ratio<1, 30>> hz30(3.5);
std::chrono::minutes t1(10);
std::chrono::seconds t2(60);
std::chrono::seconds t3 = t1 - t2;
std::cout << t3.count() << " second" << std::endl;
std::cout << chrono::duration_cast<chrono::minutes>(t3).count() <<"minutes" << endl;

// 时间点Time point
// time_point表示一个时间点，用来获取1970.1.1以来的秒数和当前的时间
typedef std::chrono::duration<int, std::ratio<60 * 60 * 24>> days_type;
std::chrono::time_point<std::chrono::system_clock, days_type> today = std::chrono::time_point_cast<days_type>(std::chrono::system_clock::now());
std::cout << today.time_since_epoch().count() << " days since epoch" << std::endl;


// 计算耗时
using clk = std::chrono::system_clock;
auto toMs = [](clk::duration duration) -> uint64_t {
    return std::chrono::duration_cast<std::chrono::microseconds>(duration).count() / 1000;
};
std::chrono::steady_clock::time_point start = std::chrono::system_clock::now();
// run code XXXX
std::chrono::steady_clock::time_point end = std::chrono::system_clock::now();
uint64_t elpased = toMs(end - start);

// time_point转换为ctime：
std::time_t now_c = std::chrono::system_clock::to_time_t(time_point);
// ctime 转 time_point
std::chrono::system_clock::time_point time_point = std::chrono::system_clock::from_time_t(now_c);
// unixtime标识 1588886970 2020/5/8 5:29:30
std::chrono::system_clock::time_point time1(std::chrono::seconds(1578466970));


// 时间的格式化输出
#include <chrono>

#include <ctime>

#include <iomanip>

std::time_t t = std::chrono::system_clock::to_time_t(std::chrono::system_clock::now());
std::cout << std::put_time(std::localtime(&t), "%Y-%m-%d %X") << std::endl;
std::cout << std::put_time(std::localtime(&t), "%Y-%m-%d %H.%M.%S") << std::endl;

const std::chrono::system_clock::duration ttl = std::chrono::hours(24 * 7);
const std::chrono::system_clock::time_point now = std::chrono::system_clock::now();
const std::chrono::system_clock::time_point sevenDaysAgo = now - ttl;
long start = std::chrono::duration_cast<std::chrono::seconds>(sevenDaysAgo.time_since_epoch()).count();

```

#### 4、unordered_set
``` cpp
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
if (find_iter != c4.end()) {
    std::cout << "找到元素 : " << *find_iter << std::endl;
} else {
    std::cout << "没找到 !" << std::endl;
}
std::cout << "value出现次数 :" << c4.count(2) << std::endl; //set key不可重复

c4.find(2);  // O(1)
c4.count(2);  // O(1)
std::find(c4.begin(), c4.end(), 2); // O(n)


std::unordered_set<int>::hasher func = c.hash_function();
std::unordered_set<int>::key_equal equal = c.key_eq();
std::cout << "hash_func: " << func(11111) << std::endl;
std::cout << "key_eq: " << equal(11111, 11111) << std::endl;

```

#### 5、map
``` cpp
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
// 指定比较器：使用类 std::greater 降序的方式
std::map<char, int, std::greater<char>> m4; 
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
std::map<char,int>::iterator it_low = mymap.lower_bound('b'); // 等于或大于'b'
std::map<char,int>::iterator it_up = mymap.upper_bound('d');   // 大于'd'
mymap.erase(it_low, it_up);        // erases [it_low, it_up)

```

#### 6、unordered_map 
``` cpp
#include <string>

#include <unordered_map>

// 初始化
typedef std::unordered_map<std::string, std::string> stringmap;
std::unordered_map<std::string, std::string> m1; // empty
std::unordered_map<std::string, std::string> m4(m2); // copy
std::unordered_map<std::string, std::string> m5(m4.begin(), m4.end()); // range
for (auto &x : m5)
    std::cout << " " << x.first << ":" << x.second;

// 迭代器操作
std::unordered_map<std::string, std::string> mymap;
for (auto it = mymap.cbegin(); it != mymap.cend(); ++it)
    std::cout << " " << it->first << ":" << it->second; // cannot modify *it

for (unsigned i = 0; i < mymap.bucket_count(); ++i){
    std::cout << "bucket #" << i << " contains:";
    for (auto local_it = mymap.cbegin(i); local_it != mymap.cend(i); ++local_it)
        std::cout << " " << local_it->first << ":" << local_it->second;
    std::cout << std::endl;
}

// 访问
std::unordered_map<std::string, int> mymap;
// 使用方括号（[]）：如果 k 匹配容器中某个元素的键，则该函数返回该映射值的引用。如果 k 与容器中任何元素的键都不匹配，则该函数将使用该键插入一个新元素，并返回该映射值的引用。
// 使用at：如果 k 匹配容器中某个元素的键，则该函数返回该映射值的引用。如果 k 与容器中任何元素的键都不匹配，则该函数将抛出 out_of_range 异常。
mymap["Mars1"] = 3396;
mymap.at("Saturn") += 272;
mymap.at("Jupiter") = mymap.at("Saturn") + 9638;

// 值的读取和find配合使用
auto it = mymap.find(id);
int value = it == mymap.cend() ? 0 : it->second;

// 插入
std::unordered_map <std::string, double> myrecipe, mypantry;
std::pair <std::string, double> myshopping ("baking powder", 0.3);
myrecipe.insert(myshopping);                        // copy insertion
myrecipe.insert(std::make_pair<std::string, double>("eggs",6.0)); // move insertion
myrecipe.insert(mypantry.begin(), mypantry.end());  // range insertion

// 删除
mymap.erase(mymap.begin());                    // erasing by iterator
mymap.erase("France");                         // erasing by key
mymap.erase(mymap.find("China"), mymap.end()); // erasing by range

// 是否存在 contains
std::unordered_map<std::string, std::string>::const_iterator got = mymap.find("France");
mymap.find("France") != mymap.end();
mymap.count("France") == 1;

// 直接操作value
std::unordered_map<uint64_t, uint64_t> mymap;
++unclickRegionMap[0];

```

#### 7、vector
``` cpp
// 大小可动态改变的顺序容器
#include<vector>
// 定义初始化
std::vector<int> v1{1, 2, 3};
std::vector<int> v3(10);     //初始化了10个默认值为0的元素
std::vector<int> v4(10, 1);   //初始化了10个值为1的元素
int a[5] = {1, 2, 3, 4, 5};
std::vector<int> v5(a, a + 5); //通过数组a的地址下标初始化
std::vector<int> v6(v5); // 通过c1初始化
std::vector<int> v7(c1.begin(), c1.begin() + 3);

std::vector<int> v(10);
std::iota(v.begin(), v.end(), 19); // iota 对容器内的元素按序递增生成序列 19为初始值


// 写入删除
v1.push_back(1);     
v1.pop_back(); 
v1.insert(v1.end(), 4);
v1.erase(v1.begin() + 1);
v1.erase(v1.begin(), v1.begin() + 2);
int x = 5;
// 所有小于5的元素移除
v1.erase(std::remove_if(v1.begin(), v1.end(), [x](int n) { return n < x; } ), v1.end());

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
for(auto it = v1.begin(); it != v1.end(); it++) {
    std::cout << (*it) << ' ';
}
std::cout << v1.front() << v1.back() << std::endl;

// 排序
#include<algorithm>
int a[10] = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};
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
template <typename K, typename V>
std::vector<K> top_keys_by_value(const std::unordered_map<K, V> &map, uint32_t size = 0) {
    std::vector<std::pair<K, V>> pair_vec{std::begin(map), std::end(map)};
    // std::vector<std::pair<K, V>> pair_vec{std::make_move_iterator(std::begin(map)), std::make_move_iterator(std::end(map))};
    std::sort(std::begin(pair_vec), std::end(pair_vec), [](std::pair<K, V> &p1, std::pair<K, V> &p2) {
        return p1.second > p2.second;
    });
    if (size != 0 && pair_vec.size() > size) {
        pair_vec.resize(size);
    }
    std::vector<K> top_keys;
    std::transform(std::begin(pair_vec), std::end(pair_vec),
            std::inserter(top_keys, std::begin(top_keys)), [](const std::pair<K, V> &p) {
        return p.first;
    });
    return top_keys;
}

// inserter
std::vector<int> d {100, 200, 300};
std::vector<int> v {1, 2, 3, 4, 5};
std::copy(d.begin(), d.end(), std::inserter(v, std::next(v.begin())));
std::copy(d.begin(), d.end(), std::back_inserter(v));
// v: 1 100 200 300 2 3 4 5

// 过滤
#include <vector>
#include <unordered_set>
#include <iostream>
#include <iterator>
#include <algorithm> // std::copy_if std::transform
const std::vector<uint64_t> candidates{1, 2, 3, 4, 5, 6};
const std::vector<uint64_t> filter_list{2, 3};
const std::unordered_set<uint64_t> filter_set(filter_list.cbegin(), filter_list.cend());
std::vector<uint64_t> filter_res_list;
filter_res_list.reserve(filter_list.size());
std::copy_if(candidates.cbegin(), candidates.cend(), std::inserter(filter_res_list, filter_res_list.begin()),[&filter_set](const uint64_t item) {
                 return filter_set.count(item) == 0;
             });
std::vector<std::string> res;
res.reserve(filter_res_list.size());
std::transform(filter_res_list.cbegin(), filter_res_list.cend(), std::inserter(res, res.begin()), static_cast<std::string (*)(uint64_t)>(std::to_string));
std::copy(res.begin(), res.end(), std::ostream_iterator<std::string>(std::cout, ","));


// 也可以使用erase + remove_if 过滤
std::vector<uint64_t> vec{1, 2, 2, 4, 3, 4};
std::unordered_set<uint64_t> filter_set = {2, 3};
auto it = std::remove_if(std::begin(vec), std::end(vec), [&filter_set](const uint64_t &item) {
    return filter_set.count(item) != 0;
});
vec.erase(it, vec.end());
std::copy(vec.cbegin(), vec.cend(), std::ostream_iterator<uint64_t>(std::cout, "\t"));
// 移除重复元素
std::sort(vec.begin(), vec.end());
vec.erase(std::unique(vec.begin(), vec.end()), vec.end());

// 元素是否存在
std::find(vec.begin(), vec.end(), str) != vec.end();

// find_if
auto it = std::find_if(std::begin(regions), std::end(regions), 
    [region](const std::pair<RegionID, size_t> &x) -> bool {
        if (x.first != region) {
            return false;
        }
        return true;
    }
);
int showRegion = 2;
if (std::distance(std::begin(regions), it) < 3) {
    showRegion = 1;
}

```

#### 8、类型转换
``` cpp
#include <string>
// 字符串转数字  
std::string str_hex = "40c3";
std::string str_bin = "-10010110001";
std::string str_auto = "0x7f";
int i_hex = std::stoi(str_hex, nullptr, 16);
int i_bin = std::stoi(str_bin, nullptr, 2);
int i_auto = std::stoi(str_auto, nullptr, 0);

std::string str = "";
uint32_t ts = static_cast<uint32_t>(std::strtoul(str, nullptr, 10));

// 字符串开始寻找数字或者正负号或者小数点，然后遇到非法字符终止，不会报异常
long stol (const string&  str, size_t* idx = 0, int base = 10);
unsigned long stoul (const string&  str, size_t* idx = 0, int base = 10);
long long stoll (const string&  str, size_t* idx = 0, int base = 10);
unsigned long long stoull (const string&  str, size_t* idx = 0, int base = 10);
float stof (const string&  str, size_t* idx = 0);
double stod (const string&  str, size_t* idx = 0);
long double stold (const string&  str, size_t* idx = 0);

std::string str = "123.456";
double d = std::stod(str);

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

const char *pBuf = "hello world";
const unsigned char *pTmp = (const unsigned char *)pBuf;


#include <sstream>

template <typename T>
std::string tostring(const T& t) {
    std::ostringstream ss;
    ss << t;
    return ss.str();
}
uint64_t data = 123;
std::string mystring = tostring(data);
```

#### 8、智能指针
``` cpp
#include <memeory>

std::unique_pt<Peron> test() {
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
    void front(const T& data) {
        auto node = std::make_unique<Node<T>>();
        node -> data = data;
        node -> next = std::move(head.next);
        head.next = std::move(node); 
    }
    void print() {
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
if (!p1) {
    std::cout << "p1 is NULL" << std::endl; // 输出
}

```

#### 9、匿名函数
``` cpp
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
``` cpp
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
``` cpp
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
``` cpp
std::string s("hello");
std::transform(s.begin(), s.end(), s.begin(),
               [](unsigned char c) -> unsigned char { return std::toupper(c); });
// s -> HELLO
std::vector<std::size_t> ordinals;
std::transform(s.begin(), s.end(), std::back_inserter(ordinals), 
    [](unsigned char c) -> std::size_t { return c; });
std::transform(s.begin(), s.end(), std::inserter(ordinals, ordinals.begin()),
               [](unsigned char c) -> std::size_t { return c; });
// s -> {72 69 76 76 79}
std::transform(ordinals.cbegin(), ordinals.cend(), ordinals.cbegin(),
                   ordinals.begin(), std::plus<>{});
// s -> {144 138 152 152 158}
```

#### 13、random
``` cpp
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

#### 14、类型转换cast
``` cpp
// 1、static_cast 
// 1.1 基本数据类型之间的转换 
float pi = 3.1415;
int a = static_cast<int>(pi);
// 1.2 void*与目标类型的指针间的转换
int n = 10;
void *pv = &n;
int *p = static_cast<int*>(pv);

int n = 10;
int *pn = &n;
void *pv = static_cast<void*>(pn);
int *p = static_cast<int*>(pv);
// 1.3 基类与派生类指针或引用类型之间的转换
Base *pb2 = new Derived;
Derived *pd2 = static_cast<Derived*>(pb2);
pd2->func(); 

Derived *pd3 = new Derived;
Base *pb3 = static_cast<Base*>(pd3);
pb3->print(); 

// const_cast
const Student *pa = new Student;
Student *pb = const_cast<Student*>(pa);
pb->v = 100;
// pa、pb指向同一个对象
std::cout << pa->v << "\n" << pb->v << std::endl;

const Student &refa = *pa;
Student &refb = const_cast<Student&>(refa);
refb.v = 200;
// refa、refb引用同一个对象
std::cout << refa.v << "\n" << refb.v << std::endl;

// dynamic_cast
// class RRContext : public BaseContext {
BaseContext* ctx;
RRContext* rctx = dynamic_cast<RRContext*>(ctx);
```

#### 15、基本类型的最大最小值
``` cpp
#include <limits>

#include <iostream>

std::cout << "short: " << std::dec << std::numeric_limits<short>::max()
          << " or " << std::hex << std::showbase << std::numeric_limits<short>::max() << '\n'
          << "int: " << std::dec << std::numeric_limits<int>::max()
          << " or " << std::hex << std::numeric_limits<int>::max() << '\n' << std::dec
          << "size_t: " << std::dec << std::numeric_limits<std::size_t>::max()
          << " or " << std::hex << std::numeric_limits<std::size_t>::max() << '\n'
          << "float: " << std::numeric_limits<float>::max()
          << " or " << std::hexfloat << std::numeric_limits<float>::max() << '\n'
          << "double: " << std::defaultfloat << std::numeric_limits<double>::max()
          << " or " << std::hexfloat << std::numeric_limits<double>::max() << '\n';

short: 32767 or 0x7fff
int: 2147483647 or 0x7fffffff
size_t: 18446744073709551615 or 0xffffffffffffffff
float: 3.40282e+38 or 0x1.fffffep+127
double: 1.79769e+308 or 0x1.fffffffffffffp+1023

```

#### 16、push vs emplace
``` cpp
std::vector<std::string> v;
v.reserve(1000);
// 1 push_back(const string&)，参数是左值引用
for (int i = 0; i < count; i++) {
  std::string temp("ceshi");
  v.push_back(temp);  
}
// 2 push_back(string &&), 参数是右值引用
// std::move把左值引用强制转换为右值引用
for (int i = 0; i < count; i++) {
  std::string temp("ceshi");
  v.push_back(std::move(temp));
}
// 3 push_back(string &&), 参数是右值引用
for (int i = 0; i < count; i++) {
  v.push_back("ceshi");
}
// 4 只有一次构造函数，不调用拷贝构造函数，速度最快
// emplace_back把参数"ceshi"完美转发给string的构造函数，直接构造了一个元素，而这个元素是直接存放在vector容器中的
for (int i = 0; i < count; i++) {
  v.emplace_back("ceshi");
}
// 1 耗时最长，将调用左值引用的push_back，且将会调用一次string的【拷贝构造函数】
// 2 3 调用string的【移动构造函数】，移动构造函数耗时比拷贝构造函数少，因为不需要重新分配内存空间
// 4 emplace_back只调用构造函数，没有移动构造函数，也没有拷贝构造函数

// 传入的参数temp不是右值引用，需要先调用temp的复制构造函数生成一个副本，然后把副本的右值引用传递给emplace_back，最终造成v.emplace_back(temp)等效与v.push_back(temp)。
for (int i = 0; i < count; i++) {
    std::string temp("ceshi");
    v.emplace_back(temp);
}

// emplace 最大的作用是避免产生不必要的临时变量，因为它可以完成 in place 的构造
struct Foo {
    Foo(int n, double x);
};

std::vector<Foo> v;
v.emplace(it, 42, 3.1416);        // 没有临时变量产生
v.insert(it, Foo(42, 3.1416));    // 需要产生一个临时变量，临时值是右值引用
v.insert(it, {42, 3.1416});       // 需要产生一个临时变量，临时值是右值引用
```

#### 17、for_each
``` cpp
std::vector<int> nums{3, 4, 2, 8, 15, 267};
auto print = [](const int& n) { std::cout << " " << n; };
std::for_each(nums.cbegin(), nums.cend(), print);
std::for_each(nums.begin(), nums.end(), [](int &n){ n++; });
```

#### 18、别名模板
``` cpp
/*
 * Composition is now a tuple with an int as the fixed first type
 * see also the default function templates example
 */
template <class ... Types>
using Composition = std::tuple<int, Types ...>;
Composition<float, string> composition(1, 2.3f, "name");
std::cout << std::get<0>(composition) << std::endl;
std::cout << std::get<1>(composition) << std::endl;
std::cout << std::get<2>(composition) << std::endl;


#include <iostream>

#include <tuple>
/*
 * Default first type of the tuple is int; opt. specify other type
 * see also the alias templates example
 */
template<class Key = int, class ... Types>
std::tuple<Key, Types ...> makeComposition(Key key, Types ... types) {
    return std::make_tuple(key, types ...);
}
auto composition = makeComposition(1, 2.3f, "name");
std::cout << std::get<0>(composition) ;
std::cout << std::get<1>(composition) ;
std::cout << std::get<2>(composition) ;


/*
 * If there's more than one item we split the first one (head) off
 * and continue with the rest (tail) recursively until we are done
 */
template <class Head>
void processItem(Head item) {
  std::cout << item << std::endl;
}
template <class Head, class ... Tail>
void processItem(Head item, Tail ... tail) {
  processItem(item);
  processItem(tail ...);
}
int main() {
  processItem("Hello", "my friend", "how are you", "today?");
  processItem("Look at all those fancy types", 1, 2.3f);
}

```

#### 19、字符串操作
``` cpp
// 包含
std::string str1 = "hello world";
std::string str2 = "world";
bool contains = str1.find(str2) != std::string::npos;

// 大小写转换
std::string str1 = "Hello World";
std::transform(str1.begin(), str1.end(), str1.begin(), ::tolower);
std::transform(str1.begin(), str1.end(), str1.begin(), ::toupper);

// 字符串替换
std::string replaceAll(std::string str, const std::string& from, const std::string& to) {
    size_t start_pos = 0;
    while ((start_pos = str.find(from, start_pos)) != std::string::npos) {
        str.replace(start_pos, from.length(), to);
        start_pos += to.length();
    }
    return str;
}
std::string str1 = "Hello World";
std::string str2 = replaceAll(str1, "l", "--");

// 字符串分割
std::vector<std::string> split(const std::string &s, char delim) {
    std::stringstream ss(s);
    std::string item;
    std::vector<std::string> elems;
    while (std::getline(ss, item, delim)) {
        elems.push_back(item);
    }
    return elems;
}
std::string str1 = "Hello World";
std::vector<std::string> words = split(str1, ' ');

#include <boost/algorithm/string.hpp>

std::vector<std::string> vec;
boost::trim(str);
boost::split(vec, str, boost::is_any_of(sep), boost::token_compress_on);
std::string res = boost::join(vec, "-");


template <typename T>
std::vector<T> split(const std::string &str, const std::string &sep) {
    std::vector<std::string> ids;
    boost::split(ids, str, boost::is_any_of(sep));
    std::vector<T> s;
    for (auto id : ids) {
        s.push_back(T(strtoul(id.c_str(), nullptr, 10)));
    }
    return s;
}

std::string foo = "abcdef";
if (foo.find("cde") != std::string::npos) {
   std::cout << "find";
} else {
   std::cout << "not find";
}
```

#### 20、vector元素最大值最小值
``` cpp
int max(const std::vector<int> &vec) {
    if (vec.size() == 0) {
        return 0;
    }
    return *std::max_element(vec.begin(), vec.end());
}
int maxIndex(const std::vector<int> &vec, int* index) {
    if (vec.size() == 0) {
        *index = 0;
        return 0;
    }
    auto largest = std::max_element(vec.begin(), vec.end());
    *index = std::distance(std::begin(vec), largest);
    return *largest;
}
int min(const std::vector<int> &vec) {
    if (vec.size() == 0) {
        return 0;
    }
    return *std::min_element(vec.begin(), vec.end());
}
int minIndex(const std::vector<int> &vec, int* index) {
    if (vec.size() == 0) {
        *index = 0;
        return 0;
    }
    auto smallest = std::min_element(vec.begin(), vec.end());
    *index = std::distance(std::begin(vec), smallest);
    return *smallest;
}
```

#### 21、std::function
``` cpp
bool parseRequest(std::function<std::string(const std::string &)> g);
bool parseRPCRequest(BRPCContext *brpc_context) {
    auto &q = brpc_context->req->queries();
    auto getter = [&q](const std::string &key) -> std::string {
        auto it = q.find(key);
        if (it == std::end(q)) {
            return "";
        }
        return it->second;
    };
    return parseRequest(getter);
}
```

#### 22、集合join
``` cpp
const std::string delimiter = "$", sep = ":";
std::unordered_map<std::string, int> map{};
std::string ret = std::accumulate(map.begin(), map.end(), std::string(), [delimiter, sep](const std::string& s, const std::pair<const std::string, int>& p) {
    return s + (s.empty() ? std::string() : delimiter) + p.first + sep + std::to_string(p.second);
});

std::vector<int> v{1,2,3};
std::string ret = std::accumulate(v.begin()+1, v.end(), std::to_string(v[0]),
    [](const std::string& a, int b) {
        return a + ',' + std::to_string(b);
});


```

#### 23、多个vector合并
``` cpp
data.insert(data.end(), left.begin(), left.end());
data.push_back(mid);
data.insert(data.end(), right.begin(), right.end());

#include <algorithm>

#include <iterator>

std::copy(left.begin(), left.end(), std::back_inserter(data));
data.push_back(mid);
std::copy(right.begin(), right.end(), std::back_inserter(data));

data.insert(data.end(), std::make_move_iterator(left.begin()), std::make_move_iterator(left.end()));
data.push_back(mid);
data.insert(data.end(), std::make_move_iterator(right.begin()), std::make_move_iterator(right.end()));

```





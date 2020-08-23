---
layout:     post
title:      "SQL 必要代码片段"
date:       2017-05-20 17:10:00
author:     "Daniel"
header-img: "img/post-bg-sql.jpg"
tags:
    - SQL
    - Hive

---


#### 1、full outer join 替换方式

``` sql
select * from t1
left join t2 on t1.id = t2.id
union
select * from t1
right join t2 on t1.id = t2.id
```

#### 2、每组取最大的topK
> group_concat & find_in_set

``` sql
select
  t1.*
from books_toufang t1
join 
(
  select
    app_id
    , group_concat(id order by chapter_count desc) as group_id_set
  from books_toufang
  group by app_id
)t2
on t1.app_id=t2.app_id and find_in_set(t1.id, group_id_set) between 1 and 5
order by t1.app_id, t1.chapter_count desc
;

set [session | global] group_concat_max_len = 10240;
```

#### 3、获取每组的最大的topK
[SQL: Getting TOP N rows for a grouped query](https://rickosborne.org/blog/2008/01/sql-getting-top-n-rows-for-a-grouped-query/)
``` sql
select
  t1.*, t2.rn
from books_toufang t1
join 
(
  select
      t1.id
      , count(*) as rn
  from books_toufang t1
  join books_toufang t2
  on t1.app_id=t2.app_id and t1.chapter_count<=t2.chapter_count
  group by t1.id
  having count(*)<=5
) t2
on t1.id=t2.id
order by app_id, rn
;
```

#### 4、获取某列最大值对应的哪一行
``` sql
select 
  user_id,
    my_date,
from
(
  select 
    user_id,
      my_date,
      max(my_date) over (partition by user_id) max_my_date
  from  users
)
where my_date = max_my_date


select
    a.*
from
    users a
inner join 
(
  select user_id, max(my_date) as max_my_date from users group by user_id
) as b 
on a.my_date = b.max_my_date

```


####  5、 select into和 insert into select 
``` sql
-- table2必须存在
insert into table2(a, c, d) select a,c,5 from table1;
-- table2不存在，在插入时会自动创建表
select a,c into table2 from table1;
```

####  6、非数字判断
``` sql 
select x from table where cast(field as double) is not null;
```

#### 7、mysql表基本信息
``` sql
-- 查询列名和注释
select column_name,
       column_comment
from information_schema.columns
where table_schema ='db'
  and table_name = 'tablename' ;

-- 查看表的注释
select table_name,
       table_comment
from information_schema.tables
where table_schema = 'db'
  and table_name ='tablename';

-- 查看表生成的ddl 
show create table table_name;

-- 查询所有的数据库信息
select distinct information_schema.schemata from tables ;
```

#### 8、中文字符
``` sql
regexp '[\u0391-\uFFE5]'
length(name) <> char_length(name)
-- 大小写不敏感
select 'a' regexp 'a', 'a' regexp binary 'a';
```

#### 9、hive日期提取星期几
``` sql
-- 返回值为0-6（0-6分别表示星期日-星期六）2017-01-01是星期日
select pmod(datediff('2017-07-27', '2017-01-01'), 7) from dual;
```

#### 10、hive 多张小表mapjoin
``` sql
select /*+ mapjoin(b, c) */  a.val, b.val, c.val from a 
 join b on (a.key = b.key1)
 join c on (c.key = b.key1)
```

#### 11、显示表的结构和位置信息
``` sql
describe formatted table1;
describe formatted dbname.table1 partition(ds='20180808');
describe extended table1;
describe extended dbname.table1 partition(ds='20180808');

-- 获得表的建表语句
show create table table1;
-- 显示owner、location、format、# files、size等
show table extended like table1;
-- 显示# files、# rows、raw data size、total size
show tblproperties table1;

desc function extended datediff;
```

#### 12、添加列、修改字段的顺序
``` sql
alter table table1 change col_old_name col_new_name column_type after column_name;
alter table table1 change ts ts bigint;
alter table table1 change ts new_col bigint;
-- 修改列名的顺序
alter table table1 change column c c string after a;

-- 添加一列  cascade:同步修改元数据，restrict:不修改元数据
-- cascade重新刷新数据时添加的字段才会有值，不然刷新数据新添加的字段以前的数据都为null
alter table table1 add columns (added_column string comment '新添加的列') cascade;
alter table table1 change column added_column added_column string after original_column1 cascade;

alter table table1 drop if exists partition(ds='${ds}');
```

#### 13、选取某一时间段的分区
``` sql
select * from 
where ds >= date_add(to_date('${bizdate}'), -30)
and ds < '${bizdate}'

datediff(to_date('${bizdate}', 'yyyymmdd'), gmt_create, 'dd') <= 30
```

#### 14、mysql 批量更新
``` sql
update my_table t1
join (
    select 1 as id, 10 as _c1, 20 as _c2
) t2 on t1.id = t2.id
set c1 = c2, c1 = _c2;
```

#### 15、mysql添加列
``` sql
alter table report_all add column registered bigint(20) not null comment '实时激活数' default 0 after id;
```

#### 16、行转列
``` sql
user_id, rec_result
73008500	[43143,104248,43470,135783,112564,129673,26992,42237,34775,46552]
62331224	[67101,43477,85409,85397,121266,39521,36232,104000,58363,129365]
104767742	[39655,41067,42119,111065,36115,36187,67580,33333,33576,53355]
165109731	[100470,29320,25469,42522,135783,39897,26485,26992,46552,26817]
164701169	[143044,41470,46407,93705,46380,67070,33576,117227,41067,29879]
128165435	[43143,121255,95172,121256,117605,106697,106686,96297,43470,138398]
148097232	[121304,121256,117617,39797,75608,40534,19953,36232,121306,52857]


select 
  user_id, rec_book_id
from noveldw_dev.tdl_lsr_rec_free_banner_fdt
lateral view explode(split(substr(rec_result, 2, length(rec_result)-2), ',')) t as rec_book_id
where ds='2019-01-27' 
limit 10;
```

#### 17、hive shell带参数执行
``` sql 
hive -database "${db}" -hivevar ds='${ds}' -hivevar hh='${mi}' -hivevar mi='${mi}' \
     -f adl_xxx_smt.sql  \
     >> ${LOG_BASE}/output.${ds}.${hh}.log 2>&1 &
-- 在adl_xxx_smt.sql中的sql参数替换采用ds='${ds}' hh='${hh}'
```

#### 18、设置任务的优先级
``` sql
Hive： 
SET mapreduce.job.queuename=root.etl.distcp; 
SET mapreduce.job.priority=HIGH; 
MapReduce： 
hadoop jar app.jar -D mapreduce.job.queuename=root.etl.distcp -D mapreduce.job.priority=HIGH

```

#### 19、hive窗口函数
``` sql
select 
  uid,
  create_time,
  order_id,
  row_number() over(partition by uid order by create_time) as rn,
  last_value(order_id) over(partition by uid order by create_time) as last,
first_value(order_id) over(partition by uid order by create_time desc) as first 
from tmp_his_order 
order by uid, create_time;

-- ntile(n)，用于将分组数据按照顺序切分成n片，返回当前切片值
select 
  order_id,
  create_time,
  pv,
  ntile(3) over(partition by order_id order by pv desc) as rn 
from tmp_his_order;

-- rank() 排名相等会在名次中留下空位
-- dense_rank() 排名相等会在名次中不会留下空位
select 
  order_id,
  create_time,
  pv,
  rank() over(partition by order_id order by pv desc) as rn1,
  dense_rank() over(partition by order_id order by pv desc) as rn2,
  row_number() over(partition by order_id order by pv desc) as rn3 
from tmp_his_order 
where order_id = 'order1';

-- cume_dist 小于等于当前值的行数/分组内总行数
select 
  dept,
  userid,
  price,
  cume_dist() over(order by price) as rn1,
  cume_dist() over(partition by dept order by price) as rn2 
from tmp_his_order;

-- grouping__id
select 
    group_id
    , phone_brand
    , network_type
    , provider_name
    , count(1) user_num
from 
(
  select 
    cast(grouping__id as bigint)&7 as group_id,-- 一定要先将grouping__id转换为数值类型 
    nvl(phone_brand, '剔重汇总') phone_brand, 
    nvl(network_type, '剔重汇总') network_type, 
    nvl(provider_name, '剔重汇总') provider_name, 
    user_id
  from temp.temp_active_user_info t
  group by phone_brand,     --1
            network_type,    --2
            provider_name,   --4
            user_id          --8
  grouping sets (
        (phone_brand, user_id),      --9&7=1
        (network_type, provider_name, user_id)   --14&7=6
  )
) t
group by group_id, phone_brand, network_type, provider_name
;
-- 1、先将grouping__id转换为数值类型
-- 2、&前后不能有空格
-- 3、&后的数字为去重字段的位置数减去1，例如上面的SQL语句种user_id的位置数为8，那&后紧跟7


-- cube 根据group by的维度的所有组合进行聚合
select 
    cate1_name
    , cate2_name
    , count(distinct book_id) as book_count
    , sum(week_click_count) as total_week_click_count
    , grouping_id()
from tdl_book_info_dim
group by cate1_name, cate2_name
with cube
order by grouping_id()

-- rollup 以最左侧的维度为主，从该维度进行层级聚合。
-- 上钻过程月天的UV->月的UV->总UV
select 
    cate1_name
    , cate2_name
    , count(distinct book_id) as book_count
    , sum(week_click_count) as total_week_click_count
    , grouping_id()
from tdl_book_info_dim
group by cate1_name, cate2_name
with rollup
order by grouping_id()
;

-- lag(col,n,DEFAULT) 用于统计窗口内往上第n行值
-- lead(col,n,DEFAULT) 用于统计窗口内往下第n行值
select 
    cate1_name
    , cate2_name
    , book_name
    , row_number() over(partition by cate1_name order by week_click_count) as rn
    , lag(week_click_count, 1, '0') over(partition by cate1_name order by week_click_count desc) as lag_1
    , lead(week_click_count, 1, '0') over(partition by cate1_name order by week_click_count desc) as lead_1
from tdl_book_info_dim
limit 100;

-- partition by doesn't actually roll up the data. 
-- It allows you to reset something on a per group basis. 
-- 如果不指定rows between,默认为从起点到当前行
-- 如果不指定order by，则将分组内所有值累加
-- preceding：往前
-- following：往后
-- current row：当前行
-- unbounded：起点
-- unbounded preceding 表示从前面的起点
-- unbounded following：表示到后面的终点

select 
  order_id,
  create_time,
  pv,
  -- 默认为从起点到当前行
  sum(pv) over(partition by order_id order by create_time) as pv1, 
  --分组内所有行
  sum(pv) over(partition by order_id) as pv3,
  --当前行+往前3行
  sum(pv) over(partition by order_id order by create_time rows between 3 preceding and current row) as pv4, 
  --当前行+往前3行+往后1行  
  sum(pv) over(partition by order_id order by create_time rows between 3 preceding and 1 following) as pv5,
  --当前行+往后所有行    
  sum(pv) over(partition by order_id order by create_time rows between current row and unbounded following) as pv6     
from tmp_his_order;

```

#### 20、hive日期函数
``` sql
select date_sub('2017-11-20', 1) from dual;  -— 2017-11-19
select date_add('2017-11-20', 1) from dual;  -- 2017-11-21
select datediff('2017-11-21', '2017-11-01 14:00:00') from dual; —- 20

select unix_timestamp() from dual; -- 1511597988
select unix_timestamp('2017-11-25 13:01:03') from dual; -- 1511586063
select unix_timestamp('20171125 13:01:03','yyyyMMdd HH:mm:ss') from dual; -- 1511586063

select from_unixtime(1511597988, 'yyyyMMdd') from dual; -- 20171125

select to_date('2017-11-25 13:01:03') from dual; -- 2017-11-25

-- 转成标准的yyyy-MM-dd格式
select to_date(from_unixtime(unix_timestamp('20200808', 'yyyyMMdd')));    -- 2020-08-08
select to_date(from_unixtime(unix_timestamp('2020 08 08', 'yyyy MM dd')));   -- 2020-08-08

```

#### 21、避免用科学计数法表示浮点数
``` sql
-- a表示总的位数，b表示保留几位小数，a要>=实际的位数，否则为NULL
cast(column as decimal(a, b))  
-- 保留4为数字，转成字符串
printf("%.4f", column)
format_number(column, 4)
```

#### 22、将多列按列名和值转换为两列
``` sql
select 
  operation_name
  , network_type
  , type
  , time
from temp.temp_page_detail_load_time
lateral view explode(map('shell_time', shell_time, 'shell_time2', shell_time2, 'load_time', load_time)) t as type, time
;
```

#### 23、快速的复制一张分区表
``` sql
create table odl_lsr_rec_user_book_30d_fht like ods_storage.odl_lsr_rec_user_book_30d_fht;

hadoop fs -cp /user/hive/warehouse/ods_storage.db/odl_lsr_rec_user_book_30d_fht /user/hive/warehouse/test.db/odl_lsr_rec_user_book_30d_fht/

msck repair table odl_lsr_rec_user_book_30d_fht;
```

#### 24、分析表的统计信息
``` sql
analyze table als_30d_fht partition(ds='2017-11-21', hh='23') compute statistics;

-- 所有分区
analyze table als_30d_fht partition(ds, hh) compute statistics;

-- 对列进行分析
analyze table als_30d_fht partition(ds='2020-05-11') compute statistics for columns book_co_cnt;

```

#### 25、带分隔符字符串连接函数concat_ws
``` sql
select concat_ws(',', 'abc', 'def', 'gh') from dual; -- abc,def,gh
```

#### 26、正则表达式替换
``` sql
select regexp_replace('foobar', 'oo|ar', '') from dual;
select regexp_extract('foothebar', 'foo(.*?)(bar)', 1) from dual; -- the
select regexp_extract('foothebar', 'foo(.*?)(bar)', 2) from dual; -- bar
select regexp_extract('foothebar', 'foo(.*?)(bar)', 0) from dual; -- foothebar
```

#### 27、json解析
``` sql
{"store":
   {"fruit":[{"weight":8,"type":"apple"},{"weight":9,"type":"pear"}],
    "bicycle":{"price":19.95,"color":"red"}
   },
  "email":"amy@only_for_json_udf_test.net",
  "owner":"amy"
}
select get_json_object(src_json.json, '$.owner') from src_json; -- amy
select get_json_object(src_json.json, '$.store.fruit[0]') from src_json; -- {"weight":8,"type":"apple"}
select get_json_object(src_json.json, '$.non_exist_key') from src_json; -- NULL
-- 转json格式
select named_struct('app', app, 'app_cnt', app_cnt);

-- hive json array 解析打平
-- {"code":200,"data":[{"item_id":129938,"cpack":{"rec_id":"8321299389"}},{"item_id":46433,"cpack":{"rec_id":"832464339"}},{"item_id":159492,"cpack":{"rec_id":"8321594923"}},{"item_id":81595,"cpack":{"rec_id":"832815953"}},{"item_id":160330,"cpack":{"rec_id":"8321603305"}},{"item_id":113865,"cpack":{"rec_id":"8321138653"}},{"item_id":159429,"cpack":{"rec_id":"8321594291"}},{"item_id":137069,"cpack":{"rec_id":"8321370699"}},{"item_id":153300,"cpack":{"rec_id":"8321533000"}},{"item_id":156020,"cpack":{"rec_id":"8321560201"}}],"flow":"88fb13f0754d92","success":true,"upack":{"req_id":"344471"}}

select 
   get_json_object(bc, '$.item_id') as item_id
  , get_json_object(bc, '$.cpack.rec_id') as rec_id
  , flow
  , get_json_object(upack, '$.req_id') as upack
from resp_log_fdt
lateral view json_tuple(resp, 'data', 'flow', 'upack') t as data, flow, upack
lateral view explode(split(regexp_replace(regexp_extract(t.data,'^\\[(.+)\\]$',1),'\\}\\,\\{', '\\}\\|\\|\\{'), '\\|\\|')) bb as bc
limit 100
;

```

#### 28、分位数
``` sql
explode(percentile_approx(cast(rating as double),array(0.05,0.5,0.95),9999)) as percentile
-- x是直方图的柱子的中心
select explode(histogram_numeric(rating, 100)) from table1; 
select inline(histogram_numeric(rating, 100)) from table1;
```

#### 29、同一行最大最小值
``` sql
select greatest(1,2,3) from dual;
3
select least(1,2,3) from dual;
1
```

#### 30、group 为 array
``` sql
select 
  a, b, concat_ws(',' , collect_set(cast(c as string)))
from table1 
group by a, b;
```

#### 31、hive数据上传下载
``` sql
load data local inpath '/tmp/test.txt' overwrite into table test; 
load data local inpath "/tmp/test.txt" overwrite into table test_p partition (date=20140722)
insert overwrite local directory '/tmp/test' row format delimited fields terminated by ',' select * from test;
```

#### 32、mysql移除字段中的所有空白字符
``` sql
replace(replace(replace(replace(filed ,char(13),''),char(10),''),char(9),''),' ','')
```

#### 33、hive 锁表处理
``` sql
-- 查看锁表状态
show locks <table_name>;
show locks <table_name> partition (<partition_desc>);

load data local inpath '/tmp/test.txt' into table test_p partition(pt=20140722); 
-- 会触发锁操作。先调用alter table add partition来创建分区，然后hadoop dfs -put向其中导入数据。
```


#### 34、mysql 时间处理
``` sql
--今天
select * from t1 where to_days(create)=to_days(now());
select * from t1 where date(create)=curdate();
--昨天
select * from t1 where to_days(now())–to_days(create)<=1;
--7天
select * from t1 where date_sub(curdate(), interval 7 day)<=date(create);
--本月
select * from t1 where date_format(create, '%y%m')=date_format(curdate(), '%y%m');
--上个月
select * from t1 where period_diff(date_format(now() , '%y%m'), date_format(create, '%y%m'))=1;
-- 时间戳和日期
select from_unixtime(1532544000)
select unix_timestamp(date('2018-07-26'))

日期类型
datetime   8 bytes   YYYY-MM-DD HH:MM:SS   1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 
timestamp  4 bytes   YYYY-MM-DD HH:MM:SS   1970-01-01 00:00:01 ~ 2038 
date       3 bytes   YYYY-MM-DD            1000-01-01          ~ 9999-12-31
datetime 的日期范围比较大；timestamp 所占存储空间比较小，只是 datetime 的一半。在 insert, update数据时，timestamp 列会自动以当前时间（CURRENT_TIMESTAMP）填充/更新。timestamp比较受时区timezone的影响以及MYSQL版本和服务器的SQL MODE的影响.

-- 日期字符串转换
select date_format('2018-08-08 22:23:01', '%Y%m%d%H%i%s');
select str_to_date('08.08.2108 08:09:30', '%m.%d.%Y %h:%i:%s');
```

#### 35、hive 正则匹配
``` sql
select '123456aa' rlike '^\\d+';
select 'footbar' regrep '^f.*r$';
select regexp_extract('foothebar', 'foo(.*?)(bar)', 1); # the
```

#### 36、mysql Duplicate entry for key group_key
``` sql
set session max_heap_table_size=536870912;
set session tmp_table_size=536870912;
```

#### 37、hive order by,sort by, distribute by, cluster by
``` sql
-- order by会对查询的结果做一次全局排序，所有的数据都会到同一个reducer进行处理
-- sort by在每个reducer端做排序，保证了局部有序
-- ditribute by控制map的输出在reducer是如何划分
select mid, money, name from store distribute by mid sort by mid asc, money asc
-- cluster by的功能就是distribute by和sort by相结合
select mid, money, name from store cluster by mid
```

#### 38、mysql中in和exists区别
``` sql
-- mysql中的in是把外表和内表作hash连接，而exists是对外表作loop循环，每次loop循环再对内表进行查询。
-- 如果查询的两个表大小相当，那么用in和exists差别不大。 
-- 如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。

-- 小表A，大表B
select * from A where exists(select cc from B where cc=A.cc);
select * from B where cc in (select cc from A);

```

#### 39、hive MD5
``` sql
select reflect('org.apache.commons.codec.digest.DigestUtils', 'md5Hex', 'your_string') ;
select reflect('org.apache.commons.codec.digest.DigestUtils', 'sha256Hex', 'your_string');
```

#### 40、用户留存
``` sql
-- 近30天每个用户的留存
insert overwrite table tdl_user_retention_info
select
    user_id
    , active_date
    , reg_date
    , retention_n
from
(
    select
        app_id
        , user_id
        , pt as active_date
        , substr(from_unixtime(cast(registertime/1000 as bigint)), 1, 10) as reg_date
        , datediff(pt, substr(from_unixtime(cast(registertime/1000 as bigint)), 1, 10)) as retention_n
    from dw_active_users
    where pt>=date_sub('${ds}', 30) and pt<='${ds}'
        and substr(from_unixtime(cast(registertime/1000 as bigint)), 1, 10)>=date_sub('${ds}', 30)
        and substr(from_unixtime(cast(registertime/1000 as bigint)), 1, 10)<='${ds}'
)t
group by user_id, active_date, reg_date, retention_n
;

-- 聚合，次留、3留、7次留
select
    t2.reg_date
    , count(distinct if(retention_n=1, t2.user_id, null)) as active_uv1
    , count(distinct if(retention_n=2, t2.user_id, null)) as active_uv2
    , count(distinct if(retention_n=6, t2.user_id, null)) as active_uv7
    , count(distinct if(retention_n=0, t1.user_id, null)) as reg_uv
from
(
  select
    user_id
  from tdl_new_user_fdt
  where ds>='2019-07-01'
)t1
join tdl_user_retention_info t2
on t1.user_id=cast(t2.user_id as string)
group by t2.reg_date
order by t2.reg_date
;
```

#### 41、explode
``` sql
-- explode
select tf.* from (select 0) t lateral view explode(array('A','B','C')) tf as col;
select tf.* from (select 0) t lateral view explode(map('A',10,'B',20,'C',30)) tf as key,value;
select tf.* from (select 0) t lateral view posexplode(array('A','B','C')) tf as pos,val;
-- inline (array of structs)
select tf.* from (select 0) t lateral view inline(array(struct('A', 10, date '2015-01-01'), struct('B', 20, date '2016-02-02'))) tf as col1, col2, col3;
-- stack (values)
select tf.* from (select 0) t lateral view stack(2, 'A', 10, date '2015-01-01', 'B', 20, date '2016-01-01') tf as col0, col1, col2;
```

#### 41、数组联动排序
``` sql
with array_table as (
  select array(1, 2, 3, 4, 5) as col1, array(0.43, 0.01, 0.45, 0.22, 0.001) as col2
)
select
  col1
  , col2
  , collect_list(c1_val) as new_col1
  , collect_list(c2_val) as new_col2
from
(
  select
    t.col1 as col1
    , t.col2 as col2
    , val as c1_val
    , t.col2[pos] as c2_val
    , pos as c1_pos
  from array_table t
  lateral view posexplode(col1) c1 as pos, val
  distribute by col1, col2
  sort by c2_val
)s
group by col1, col2;
```

#### 42、hive数据倾斜
##### Map倾斜
- 原因

  1、上游表文件大小不均匀，并且小文件特别多，导致当前map任务读取数据分布不均匀，引起长尾；

  2、Map任务做`count distinct`时，某些文件的某个值特别多引起长尾；

- 方案

  1、调节map任务的个数和每个map任务读取小文件的个数；

  2、通过`distirbute by rand()`随机分布函数将数据重新随机分发，将聚合、join操作在其他阶段完成；

##### Join倾斜

  1、join的某路输入比较小，采用mapjoin避免分发引起的长尾；

  2、join的每路输入都比较大，且长尾是空值导致的，将空值处理成随机值，`on coalesce(t1.key, rand()*9999) = t2.key`；

##### Reduce倾斜

  1、对同一个表的不同列`count distinct`，造成map端数据膨胀，使得下游的join和reduce出现链路上的长尾；
  
  2、map端直接聚合出现key分布不均匀，造成reduce长尾。通过对热点key单独处理；

  3、动态分区过多造成小文件过多，引起reduce长尾；

  4、多个distinct同时出现在一段sql，数据会被分发多次，不经会造成数据膨胀N倍，还会把长尾现象放大N倍。

#### 43、hive常用设置
``` sql
-- mapjoin
set hive.auto.convert.join=true;
set hive.mapjoin.smalltable.filesize=64000000;
set hive.auto.convert.join.noconditionaltask=true;
set hive.auto.convert.join.noconditionaltask.size=256000000;
-- map输入合并小文件
set mapred.max.split.size=256000000;
set mapred.min.split.size.per.node=256000000;
set mapred.min.split.size.per.rack=256000000;
set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;
-- map输入文件合并
set hive.merge.mapfiles=true;
set hive.merge.mapredfiles=true;
set hive.merge.size.per.task=256000000;
set hive.merge.smallfiles.avgsize=12800000;
-- jvm重用
set mapred.job.reuse.jvm.num.tasks=10;
--子查询并发执行
set hive.support.concurrency=true;
set mapreduce.job.reduce.slowstart.completedmaps=0.9;
-- 矢量查询，1024行数据组成一个batch进行处理
set hive.vectorized.execution.enabled=true;
set hive.vectorized.execution.reduce.enabled=true;
```

#### 44、AUC计算
参考[深入理解AUC](https://tracholar.github.io/machine-learning/2018/01/26/auc.html)

将测试集的正负样本按照**模型预测得分从小到大排序**，对于第 $$j$$ 个正样本，假设它的排序为 $$r_j$$，排在第j个正样本前面的负样本个数为 $$r_j - j$$ 个，对于第 $$j$$ 个正样本来说，它的得分比随机取的一个负样本得分大的概率是 $$(r_j − j)/N_−$$,其中$$N_−$$是总的负样本数目。平均下来，随机取的正样本得分比负样本大的概率为

\\[ AUC = \frac{1}{N_{+}} \sum_{j=1}^{N_{+}}\left(r_{j}-j\right) / N_{-}=\frac{\sum_{j=1}^{N_{+}} r_{j}-N_{+}\left(N_{+}+1\right) / 2}{N_{+} N_{-}} \\]

``` sql
select
  (ry - 0.5*n1*(n1+1))/n0/n1 as auc
from
(
  select
    sum(if(y=0, 1, 0)) as n0,
    sum(if(y=1, 1, 0)) as n1,
    sum(if(y=1, r, 0)) as ry
  from
  (
    select 
      y, row_number() over(order by score asc) as r
    from
    (
      select y, score from some.table
    )t1
  )t2
)t3
```

#### 45、计算连续登陆天数
``` sql
-- 每个人连续工作时间段及对应的工资和

select
  name
  , start_date
  , end_date
  , salary * (datediff(end_date, start_date) + 1) as salary
from
(
  select
    name
    , date_sub(active_date, rn - 1) as flag_date
    , salary
    , min(active_date) as start_date
    , max(active_date) as end_date
  from
  (
    select 
      name
      , active_date
      , salary
      , row_number() over(partition by name order by active_date) as rn
    from test
    group by name, active_date, salary
  )t
  group by name, date_sub(active_date, rn - 1), salary
)t
;
-- 计算连续登陆天数

select
  user_id
  , login_time
  , date_sub(login_date, rn - 1) as login_group
  , min(login_date) as start_date
  , max(login_date) as end_date
  , count(1) as cont_days
from
(
  select
    user_id
    , login_date
    , row_number() over(partition by user_id order by login_date) as rn
  from test
)t
group by user_id, date_sub(login_time, rn - 1)
;
```

#### 46、with as
``` sql
with q1 as ( select key from src where key = '5')
select * from q1;
 
-- from style
with q1 as (select * from src where key= '5')
from q1
select *;
  
-- chaining CTEs
with q1 as ( select key from q2 where key = '5'),
q2 as ( select key from src where key = '5')
select * from (select key from q1) a;

with q1 as ( select key from src where key = '5'),
q2 as ( select key from q1 where key = '5')
select * from (select key from q2) a;

-- union example
with q1 as (select * from src where key= '5'),
q2 as (select * from src s2 where key = '4')
select * from q1 union all select * from q2;

-- insert example
create table s1 like src;
with q1 as ( select key, value from src where key = '5')
from q1
insert overwrite table s1
select *;
 
-- ctas example
create table s2 as
with q1 as ( select key from src where key = '4')
select * from q1;

```

#### 47、presto获取表路径
``` sql
select "$path" from dws_table;
```



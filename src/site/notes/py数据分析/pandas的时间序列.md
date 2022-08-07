---
{"dg-publish":true,"permalink":"/py/pandas/","tags":"gardenEntry","dgHomeLink":true,"dgPassFrontmatter":false}
---

# 1.时间序列的介绍
在使用Python进行数据分析时，经常会遇到时间日期格式处理和转换，特别是分析和挖掘与时间相关的数据，比如量化交易就是从历史数据中寻找股价的变化规律。Python中自带的处理时间的模块有datetime，NumPy库也提供了相应的方法，Pandas作为Python环境下的数据分析库，更是提供了强大的日期数据处理的功能，是处理时间序列的利器

什么是时间序列？

时间序列数据有很多种定义，它们以不同的方式表示相同的含义。一个简单的定义是，时间序列数据是包含序列时间戳的数据点。

- 股票价格随时间变化
- 日、周、月销售额
- 过程中的周期性测量
- 一段时间内的电力或天然气消耗率

时间序列是指多个时间点上形成的数值序列它既可以是定期出现的，也可以是不定期出现的。

对于时间序列的数据而言，必然少不了时间戳这一关键元素。Pandas中，时间戳的使用Timesamp对象表示，该对象与datatime有高度兼容性，可以直接通过datetime()函数将datetime转换成TimeStamp对象，具体示例如下。


```python
import pandas as pd
import numpy as np
from datetime import datetime
```


```python
#三种时间表达方式
dt=pd.to_datetime("2022-11-12")
dt=pd.to_datetime("20220112")
dt=pd.to_datetime("23/12/2021")
dt
```

    /var/folders/ns/x8d70sy17cd0lbs0r26bxv840000gn/T/ipykernel_46144/1395829052.py:3: UserWarning: Parsing '23/12/2021' in DD/MM/YYYY format. Provide format or specify infer_datetime_format=True for consistent parsing.
      dt=pd.to_datetime("23/12/2021")





    Timestamp('2021-12-23 00:00:00')




```python
#创建series时间对象
dt=pd.to_datetime(["2022-11-12","2022-09-13","2022-08-23"])
dt
```




    DatetimeIndex(['2022-11-12', '2022-09-13', '2022-08-23'], dtype='datetime64[ns]', freq=None)




```python
#将时间作为索引
data=pd.Series((1,3,5),index=dt)
data
```




    2022-11-12    1
    2022-09-13    3
    2022-08-23    5
    dtype: int64




```python
#也可以直接用datatime进行转化
dates=[
datetime(2020,1,2),datetime(2020,1,5),datetime(2020,1,7),
datetime(2020,1,8),datetime(2020,1,10),datetime(2020,1,12)
]

```


```python
ts=pd.Series(np.random.randn(6),index=dates)
ts
```




    2020-01-02   -0.639841
    2020-01-05    0.939297
    2020-01-07   -0.592684
    2020-01-08   -0.408900
    2020-01-10    0.327843
    2020-01-12    0.656634
    dtype: float64




```python
ts.index
```




    DatetimeIndex(['2020-01-02', '2020-01-05', '2020-01-07', '2020-01-08',
                   '2020-01-10', '2020-01-12'],
                  dtype='datetime64[ns]', freq=None)




```python
ts.index.dtype
```




    dtype('<M8[ns]')



# 2.时间序列的选取


```python
# 指定索引为多个日期字符串的列表
date_list = ['2015/05/30', '2017/02/01',
             '2015.6.1', '2016.4.1',
             '2017.6.1', '2018.1.23']
```


```python
# 将日期字符串转换为DatetimeIndex 
date_index = pd.to_datetime(date_list)
```


```python
# 创建以DatetimeIndex 为索引的Series对象
date_se = pd.Series(np.arange(6), index=date_index)
date_se
```




    2015-05-30    0
    2017-02-01    1
    2015-06-01    2
    2016-04-01    3
    2017-06-01    4
    2018-01-23    5
    dtype: int64




```python
# 根据位置索引获取数据
date_se[3]
```




    3




```python
date_time = datetime(2015, 6, 1)
date_se[date_time]
```




    2




```python
date_se['20150530']
```




    0




```python
date_se['2018/01/23']
```




    5




```python
date_se['2015']  # 获取2015年的数据
```




    2015-05-30    0
    2015-06-01    2
    dtype: int64



除了使用索引的方式以外，还可以通过truncate()方法截取Series或DataFrame对象，该语法格式如下：

**DataFrame.truncate(before=None, after=None, axis=None, copy=True)[source]**

在某个索引值之前和之后Truncate Series或DataFrame。
- before --表示截断此索引值之前的所有行。
- after -- 表示截断此索引值之后的所有行。
- axis --表示截断的轴，默认为行索引方向。


```python
# 扔掉2016-1-1之前的数据
sorted_se = date_se.sort_index()
sorted_se
```




    2015-05-30    0
    2015-06-01    2
    2016-04-01    3
    2017-02-01    1
    2017-06-01    4
    2018-01-23    5
    dtype: int64




```python
sorted_se.truncate(before='2016-1-1')
```




    2016-04-01    3
    2017-02-01    1
    2017-06-01    4
    2018-01-23    5
    dtype: int64




```python
# 扔掉2016-7-31之后的数据
sorted_se.truncate(after='2016-7-31')
```




    2015-05-30    0
    2015-06-01    2
    2016-04-01    3
    dtype: int64



# 3、时间序列的生成

pandas提供了data_range()函数，主要生成一个具有固定的频率的DatetimeIndex对象，该函数的语法格式如下：

**pandas.date_range(start=None, end=None, periods=None, freq='D', tz=None, normalize=False, name=None, closed=None, **kwargs)**

该函数主要用于生成一个固定频率的时间索引，在调用构造方法时，必须指定start、end、periods中的两个参数值，否则报错。

- periods：固定时期，取值为整数或None产生多少个值
- freq：日期偏移量，取值为string或DateOffset，默认为'D'
- normalize：若参数为True表示将start、end参数值正则化到午夜时间戳
- name：生成时间索引对象的名称，取值为string或None
- closed：可以理解成在closed=None情况下返回的结果中，若closed=‘left’表示在返回的结果基础上，再取左开右闭的结果，若closed='right'表示在返回的结果基础上，再取做闭右开的结果

需要注意的是start，end，periods，freq这四个参数至少需要三个指定的参数，否则会出现错误。

当调用date_range()函数创建Datetimelndex对象时，如果只是传入了开始日期(start参数)与结束日期(end参数)，则默认生成的时间戳是按天计算的，即freq参数为D。


```python
# 创建DatetimeIndex对象时，只传入开始日期与结束日期
pd.date_range('2018/08/10', '2018/08/20')
```




    DatetimeIndex(['2018-08-10', '2018-08-11', '2018-08-12', '2018-08-13',
                   '2018-08-14', '2018-08-15', '2018-08-16', '2018-08-17',
                   '2018-08-18', '2018-08-19', '2018-08-20'],
                  dtype='datetime64[ns]', freq='D')



如果只传入开始的日期或者结束的日期，则需要用periods参数指定产生多少个时间戳，示例代码如下：


```python
#创建DatetimeIndex对象时，传入start与periods参数
pd.date_range(start='2018/08/10', periods=5)
```




    DatetimeIndex(['2018-08-10', '2018-08-11', '2018-08-12', '2018-08-13',
                   '2018-08-14'],
                  dtype='datetime64[ns]', freq='D')




```python
# 创建DatetimeIndex对象时，传入end与periods参数
pd.date_range(end='2018/08/10', periods=5)
```




    DatetimeIndex(['2018-08-06', '2018-08-07', '2018-08-08', '2018-08-09',
                   '2018-08-10'],
                  dtype='datetime64[ns]', freq='D')



如果日期中带有与时间相关的信息，且想产生一组被规范化到当天午夜的时间戳，可以将normalize参数的值设为True 。

如果希望时间序列中时间戳都是每周固定的星期日，则可以创建DatetimeIndex时将fre参数设成为“W-SUN”。列如，创建一个DatetimeIndex对象，它是从2018年01月01日开始每隔一周连续生成五个星期日，代码如下：


```python
dates_index = pd.date_range('2018-01-01',         # 起始日期
                            periods=5,            # 周期
                            freq='W-SUN')         # 频率
dates_index
```




    DatetimeIndex(['2018-01-07', '2018-01-14', '2018-01-21', '2018-01-28',
                   '2018-02-04'],
                  dtype='datetime64[ns]', freq='W-SUN')




```python
ser_data = [12, 56, 89, 99, 31]
pd.Series(ser_data, dates_index)
```




    2018-01-07    12
    2018-01-14    56
    2018-01-21    89
    2018-01-28    99
    2018-02-04    31
    Freq: W-SUN, dtype: int64




```python
# 创建DatetimeIndex，并指定开始日期、产生日期个数、默认的频率，以及时区
pd.date_range(start='2018/8/1 12:13:30', periods=5, 
              tz='Asia/Hong_Kong')           
```




    DatetimeIndex(['2018-08-01 12:13:30+08:00', '2018-08-02 12:13:30+08:00',
                   '2018-08-03 12:13:30+08:00', '2018-08-04 12:13:30+08:00',
                   '2018-08-05 12:13:30+08:00'],
                  dtype='datetime64[ns, Asia/Hong_Kong]', freq='D')




```python
#规范化时间戳
pd.date_range(start='2018/8/1 12:13:30', periods=5, 
              normalize=True, tz='Asia/Hong_Kong') 
```




    DatetimeIndex(['2018-08-01 00:00:00+08:00', '2018-08-02 00:00:00+08:00',
                   '2018-08-03 00:00:00+08:00', '2018-08-04 00:00:00+08:00',
                   '2018-08-05 00:00:00+08:00'],
                  dtype='datetime64[ns, Asia/Hong_Kong]', freq='D')



# 4、时间序列的偏移量

| 名称                | 偏移量类型          | 说明                                                         |
| ------------------- | ------------------- | ------------------------------------------------------------ |
| D                   | Day                 | 每日                                                         |
| B                   | BusinessDay         | 每工作日                                                     |
| H                   | Hour                | 每小时                                                       |
| T或min              | Minute              | 每分                                                         |
| S                   | Second              | 每秒                                                         |
| L或ms               | Milli               | 每毫秒                                                       |
| U                   | Micro               | 每微秒                                                       |
| M                   | MounthEnd           | 每月最后一个日历日                                           |
| BM                  | BusinessMonthEnd    | 每月最后一个工作日                                           |
| MS                  | MonthBegin          | 每月每一个工作日                                             |
| BMS                 | BusinessMonthBegin  | 每月第一个工作日                                             |
| W-MON、W-TUE...     | Week                | 指定星期几（MON、TUE、WED、THU、FRI、SAT、SUM）              |
| WOM-1MON、WMON-2MON | WeekOfMonth         | 产生每月第一、第二、第三或第四周的星期几                     |
| Q-JAN、Q-FEB...     | QuaterEnd           | 对于以指定月份（JAN、FEB、MAR、APR、MAY、JUN、JUL、AUG、SEP、OCT、NOV、DEC）结束的年度，每季度最后一月的最后一个日历日 |
| BQ-JAN、BQ-FEB...   | BusinessQuaterEnd   | 对于以指定月份结束的年度，每季度最后一月的最后一个工作日     |
| QS-JAN、QS-FEB...   | QuaterBegin         | 对于以指定月份结束的年度，每季度最后一月的第一个日历日       |
| QS-JAN、QS-FEB...   | BusinessQuaterBegin | 对于指定月份结束的年度，每季度最后一月的第一个工作日         |
| A-JAN、A-FEB...     | YearEnd             | 每年指定月份的最后一个日历日                                 |
| BA-JAN、BA-FEB      | BusinessYearEnd     | 每年指定月份的最后一个日历日                                 |
| AS-JAN、AS-FEB      | YearBegin           | 每年指定月份的第一个日历日                                   |
| BAS-JAN、BAS-FEB    | BusinessYearBegin   | 每年指定月份的第一个工作日                                   |


```python
#例如，每月第3个星期五
pd.date_range('1/1/2020','9/1/2020',freq='WOM-3FRI')
```




    DatetimeIndex(['2020-01-17', '2020-02-21', '2020-03-20', '2020-04-17',
                   '2020-05-15', '2020-06-19', '2020-07-17', '2020-08-21'],
                  dtype='datetime64[ns]', freq='WOM-3FRI')



# 5.时间前移或后移
Pandas对象中提供了一个**shift()方法**，用来前移或后移数据，但索引保持不变。
shift()方法语法格式如下：

**shift(periods=1，freq=None，axis=0)**部分参数含义如下：

-  periods：表示移动的幅度，可以为正数，也可以为负数，默认值是1，代表移动一次。
- freq：如果这个参数存在，那么会按照参数值移动时间戳索引，而数据值没有发生变化

接下来，通过一个示例来演示如何使用时间序列的数据发生了前移或后移效果。

首先我们创建一个使用DatetimeIndex作为索引的Series对象，示例代码如下。

shift方法用于执行单纯的前移或后移操作


```python
ts=pd.Series(np.random.randn(4),
index=pd.date_range('1/1/2020',periods=4,freq='M'))
ts
```




    2020-01-31    0.871835
    2020-02-29   -1.544202
    2020-03-31    0.835669
    2020-04-30   -0.152571
    Freq: M, dtype: float64




```python
#向后移动一个月
ts.shift(1,freq='M')
```




    2020-02-29    0.871835
    2020-03-31   -1.544202
    2020-04-30    0.835669
    2020-05-31   -0.152571
    Freq: M, dtype: float64




```python
#向前移动3天
ts.shift(-3,freq='D')
```




    2020-01-28    0.871835
    2020-02-26   -1.544202
    2020-03-28    0.835669
    2020-04-27   -0.152571
    dtype: float64




```python
#通过Day或MonthEnd移动
from pandas.tseries.offsets import Day,MonthEnd
now=datetime(2020,1,27)
now
```




    datetime.datetime(2020, 1, 27, 0, 0)




```python
now+3*Day()
```




    Timestamp('2020-01-30 00:00:00')




```python
now+MonthEnd()
```




    Timestamp('2020-01-31 00:00:00')



# 6.时区处理

python的时区信息来自第三方库pytz，pandas包装了pytz的功能
查看所有时区


```python
import pytz
pytz.common_timezones
```




    ['Africa/Abidjan', 'Africa/Accra', 'Africa/Addis_Ababa', 'Africa/Algiers', 'Africa/Asmara', 'Africa/Bamako', 'Africa/Bangui', 'Africa/Banjul', 'Africa/Bissau', 'Africa/Blantyre', 'Africa/Brazzaville', 'Africa/Bujumbura', 'Africa/Cairo', 'Africa/Casablanca', 'Africa/Ceuta', 'Africa/Conakry', 'Africa/Dakar', 'Africa/Dar_es_Salaam', 'Africa/Djibouti', 'Africa/Douala', 'Africa/El_Aaiun', 'Africa/Freetown', 'Africa/Gaborone', 'Africa/Harare', 'Africa/Johannesburg', 'Africa/Juba', 'Africa/Kampala', 'Africa/Khartoum', 'Africa/Kigali', 'Africa/Kinshasa', 'Africa/Lagos', 'Africa/Libreville', 'Africa/Lome', 'Africa/Luanda', 'Africa/Lubumbashi', 'Africa/Lusaka', 'Africa/Malabo', 'Africa/Maputo', 'Africa/Maseru', 'Africa/Mbabane', 'Africa/Mogadishu', 'Africa/Monrovia', 'Africa/Nairobi', 'Africa/Ndjamena', 'Africa/Niamey', 'Africa/Nouakchott', 'Africa/Ouagadougou', 'Africa/Porto-Novo', 'Africa/Sao_Tome', 'Africa/Tripoli', 'Africa/Tunis', 'Africa/Windhoek', 'America/Adak', 'America/Anchorage', 'America/Anguilla', 'America/Antigua', 'America/Araguaina', 'America/Argentina/Buenos_Aires', 'America/Argentina/Catamarca', 'America/Argentina/Cordoba', 'America/Argentina/Jujuy', 'America/Argentina/La_Rioja', 'America/Argentina/Mendoza', 'America/Argentina/Rio_Gallegos', 'America/Argentina/Salta', 'America/Argentina/San_Juan', 'America/Argentina/San_Luis', 'America/Argentina/Tucuman', 'America/Argentina/Ushuaia', 'America/Aruba', 'America/Asuncion', 'America/Atikokan', 'America/Bahia', 'America/Bahia_Banderas', 'America/Barbados', 'America/Belem', 'America/Belize', 'America/Blanc-Sablon', 'America/Boa_Vista', 'America/Bogota', 'America/Boise', 'America/Cambridge_Bay', 'America/Campo_Grande', 'America/Cancun', 'America/Caracas', 'America/Cayenne', 'America/Cayman', 'America/Chicago', 'America/Chihuahua', 'America/Costa_Rica', 'America/Creston', 'America/Cuiaba', 'America/Curacao', 'America/Danmarkshavn', 'America/Dawson', 'America/Dawson_Creek', 'America/Denver', 'America/Detroit', 'America/Dominica', 'America/Edmonton', 'America/Eirunepe', 'America/El_Salvador', 'America/Fort_Nelson', 'America/Fortaleza', 'America/Glace_Bay', 'America/Goose_Bay', 'America/Grand_Turk', 'America/Grenada', 'America/Guadeloupe', 'America/Guatemala', 'America/Guayaquil', 'America/Guyana', 'America/Halifax', 'America/Havana', 'America/Hermosillo', 'America/Indiana/Indianapolis', 'America/Indiana/Knox', 'America/Indiana/Marengo', 'America/Indiana/Petersburg', 'America/Indiana/Tell_City', 'America/Indiana/Vevay', 'America/Indiana/Vincennes', 'America/Indiana/Winamac', 'America/Inuvik', 'America/Iqaluit', 'America/Jamaica', 'America/Juneau', 'America/Kentucky/Louisville', 'America/Kentucky/Monticello', 'America/Kralendijk', 'America/La_Paz', 'America/Lima', 'America/Los_Angeles', 'America/Lower_Princes', 'America/Maceio', 'America/Managua', 'America/Manaus', 'America/Marigot', 'America/Martinique', 'America/Matamoros', 'America/Mazatlan', 'America/Menominee', 'America/Merida', 'America/Metlakatla', 'America/Mexico_City', 'America/Miquelon', 'America/Moncton', 'America/Monterrey', 'America/Montevideo', 'America/Montserrat', 'America/Nassau', 'America/New_York', 'America/Nipigon', 'America/Nome', 'America/Noronha', 'America/North_Dakota/Beulah', 'America/North_Dakota/Center', 'America/North_Dakota/New_Salem', 'America/Nuuk', 'America/Ojinaga', 'America/Panama', 'America/Pangnirtung', 'America/Paramaribo', 'America/Phoenix', 'America/Port-au-Prince', 'America/Port_of_Spain', 'America/Porto_Velho', 'America/Puerto_Rico', 'America/Punta_Arenas', 'America/Rainy_River', 'America/Rankin_Inlet', 'America/Recife', 'America/Regina', 'America/Resolute', 'America/Rio_Branco', 'America/Santarem', 'America/Santiago', 'America/Santo_Domingo', 'America/Sao_Paulo', 'America/Scoresbysund', 'America/Sitka', 'America/St_Barthelemy', 'America/St_Johns', 'America/St_Kitts', 'America/St_Lucia', 'America/St_Thomas', 'America/St_Vincent', 'America/Swift_Current', 'America/Tegucigalpa', 'America/Thule', 'America/Thunder_Bay', 'America/Tijuana', 'America/Toronto', 'America/Tortola', 'America/Vancouver', 'America/Whitehorse', 'America/Winnipeg', 'America/Yakutat', 'America/Yellowknife', 'Antarctica/Casey', 'Antarctica/Davis', 'Antarctica/DumontDUrville', 'Antarctica/Macquarie', 'Antarctica/Mawson', 'Antarctica/McMurdo', 'Antarctica/Palmer', 'Antarctica/Rothera', 'Antarctica/Syowa', 'Antarctica/Troll', 'Antarctica/Vostok', 'Arctic/Longyearbyen', 'Asia/Aden', 'Asia/Almaty', 'Asia/Amman', 'Asia/Anadyr', 'Asia/Aqtau', 'Asia/Aqtobe', 'Asia/Ashgabat', 'Asia/Atyrau', 'Asia/Baghdad', 'Asia/Bahrain', 'Asia/Baku', 'Asia/Bangkok', 'Asia/Barnaul', 'Asia/Beirut', 'Asia/Bishkek', 'Asia/Brunei', 'Asia/Chita', 'Asia/Choibalsan', 'Asia/Colombo', 'Asia/Damascus', 'Asia/Dhaka', 'Asia/Dili', 'Asia/Dubai', 'Asia/Dushanbe', 'Asia/Famagusta', 'Asia/Gaza', 'Asia/Hebron', 'Asia/Ho_Chi_Minh', 'Asia/Hong_Kong', 'Asia/Hovd', 'Asia/Irkutsk', 'Asia/Jakarta', 'Asia/Jayapura', 'Asia/Jerusalem', 'Asia/Kabul', 'Asia/Kamchatka', 'Asia/Karachi', 'Asia/Kathmandu', 'Asia/Khandyga', 'Asia/Kolkata', 'Asia/Krasnoyarsk', 'Asia/Kuala_Lumpur', 'Asia/Kuching', 'Asia/Kuwait', 'Asia/Macau', 'Asia/Magadan', 'Asia/Makassar', 'Asia/Manila', 'Asia/Muscat', 'Asia/Nicosia', 'Asia/Novokuznetsk', 'Asia/Novosibirsk', 'Asia/Omsk', 'Asia/Oral', 'Asia/Phnom_Penh', 'Asia/Pontianak', 'Asia/Pyongyang', 'Asia/Qatar', 'Asia/Qostanay', 'Asia/Qyzylorda', 'Asia/Riyadh', 'Asia/Sakhalin', 'Asia/Samarkand', 'Asia/Seoul', 'Asia/Shanghai', 'Asia/Singapore', 'Asia/Srednekolymsk', 'Asia/Taipei', 'Asia/Tashkent', 'Asia/Tbilisi', 'Asia/Tehran', 'Asia/Thimphu', 'Asia/Tokyo', 'Asia/Tomsk', 'Asia/Ulaanbaatar', 'Asia/Urumqi', 'Asia/Ust-Nera', 'Asia/Vientiane', 'Asia/Vladivostok', 'Asia/Yakutsk', 'Asia/Yangon', 'Asia/Yekaterinburg', 'Asia/Yerevan', 'Atlantic/Azores', 'Atlantic/Bermuda', 'Atlantic/Canary', 'Atlantic/Cape_Verde', 'Atlantic/Faroe', 'Atlantic/Madeira', 'Atlantic/Reykjavik', 'Atlantic/South_Georgia', 'Atlantic/St_Helena', 'Atlantic/Stanley', 'Australia/Adelaide', 'Australia/Brisbane', 'Australia/Broken_Hill', 'Australia/Darwin', 'Australia/Eucla', 'Australia/Hobart', 'Australia/Lindeman', 'Australia/Lord_Howe', 'Australia/Melbourne', 'Australia/Perth', 'Australia/Sydney', 'Canada/Atlantic', 'Canada/Central', 'Canada/Eastern', 'Canada/Mountain', 'Canada/Newfoundland', 'Canada/Pacific', 'Europe/Amsterdam', 'Europe/Andorra', 'Europe/Astrakhan', 'Europe/Athens', 'Europe/Belgrade', 'Europe/Berlin', 'Europe/Bratislava', 'Europe/Brussels', 'Europe/Bucharest', 'Europe/Budapest', 'Europe/Busingen', 'Europe/Chisinau', 'Europe/Copenhagen', 'Europe/Dublin', 'Europe/Gibraltar', 'Europe/Guernsey', 'Europe/Helsinki', 'Europe/Isle_of_Man', 'Europe/Istanbul', 'Europe/Jersey', 'Europe/Kaliningrad', 'Europe/Kiev', 'Europe/Kirov', 'Europe/Lisbon', 'Europe/Ljubljana', 'Europe/London', 'Europe/Luxembourg', 'Europe/Madrid', 'Europe/Malta', 'Europe/Mariehamn', 'Europe/Minsk', 'Europe/Monaco', 'Europe/Moscow', 'Europe/Oslo', 'Europe/Paris', 'Europe/Podgorica', 'Europe/Prague', 'Europe/Riga', 'Europe/Rome', 'Europe/Samara', 'Europe/San_Marino', 'Europe/Sarajevo', 'Europe/Saratov', 'Europe/Simferopol', 'Europe/Skopje', 'Europe/Sofia', 'Europe/Stockholm', 'Europe/Tallinn', 'Europe/Tirane', 'Europe/Ulyanovsk', 'Europe/Uzhgorod', 'Europe/Vaduz', 'Europe/Vatican', 'Europe/Vienna', 'Europe/Vilnius', 'Europe/Volgograd', 'Europe/Warsaw', 'Europe/Zagreb', 'Europe/Zaporozhye', 'Europe/Zurich', 'GMT', 'Indian/Antananarivo', 'Indian/Chagos', 'Indian/Christmas', 'Indian/Cocos', 'Indian/Comoro', 'Indian/Kerguelen', 'Indian/Mahe', 'Indian/Maldives', 'Indian/Mauritius', 'Indian/Mayotte', 'Indian/Reunion', 'Pacific/Apia', 'Pacific/Auckland', 'Pacific/Bougainville', 'Pacific/Chatham', 'Pacific/Chuuk', 'Pacific/Easter', 'Pacific/Efate', 'Pacific/Fakaofo', 'Pacific/Fiji', 'Pacific/Funafuti', 'Pacific/Galapagos', 'Pacific/Gambier', 'Pacific/Guadalcanal', 'Pacific/Guam', 'Pacific/Honolulu', 'Pacific/Kanton', 'Pacific/Kiritimati', 'Pacific/Kosrae', 'Pacific/Kwajalein', 'Pacific/Majuro', 'Pacific/Marquesas', 'Pacific/Midway', 'Pacific/Nauru', 'Pacific/Niue', 'Pacific/Norfolk', 'Pacific/Noumea', 'Pacific/Pago_Pago', 'Pacific/Palau', 'Pacific/Pitcairn', 'Pacific/Pohnpei', 'Pacific/Port_Moresby', 'Pacific/Rarotonga', 'Pacific/Saipan', 'Pacific/Tahiti', 'Pacific/Tarawa', 'Pacific/Tongatapu', 'Pacific/Wake', 'Pacific/Wallis', 'US/Alaska', 'US/Arizona', 'US/Central', 'US/Eastern', 'US/Hawaii', 'US/Mountain', 'US/Pacific', 'UTC']




```python
#转换时区- tz_convert
rng=pd.date_range('3/9/2020 9:30',periods=6,freq='D',tz='UTC')
```


```python
ts=pd.Series(np.random.randn(len(rng)),index=rng)
ts
```




    2020-03-09 09:30:00+00:00    0.724865
    2020-03-10 09:30:00+00:00   -0.428694
    2020-03-11 09:30:00+00:00    0.143422
    2020-03-12 09:30:00+00:00    0.031878
    2020-03-13 09:30:00+00:00   -0.425110
    2020-03-14 09:30:00+00:00   -0.413336
    Freq: D, dtype: float64




```python
ts.tz_convert('Asia/Shanghai')
```




    2020-03-09 17:30:00+08:00    0.724865
    2020-03-10 17:30:00+08:00   -0.428694
    2020-03-11 17:30:00+08:00    0.143422
    2020-03-12 17:30:00+08:00    0.031878
    2020-03-13 17:30:00+08:00   -0.425110
    2020-03-14 17:30:00+08:00   -0.413336
    Freq: D, dtype: float64



# 7.时期及算术运算

时期（period）表示的是时间区间，比如数日、数月、数季、数年等
下面这个Period对象表示从2020年1月1日到2020年12月31日之间的整段时间


```python
p=pd.Period(2020,freq='A-DEC')
p
```




    Period('2020', 'A-DEC')



创建规则的时期范围


```python
#季度为Q生成13个时间
pd.period_range("2019-01", periods=13, freq="Q")
```




    PeriodIndex(['2019Q1', '2019Q2', '2019Q3', '2019Q4', '2020Q1', '2020Q2',
                 '2020Q3', '2020Q4', '2021Q1', '2021Q2', '2021Q3', '2021Q4',
                 '2022Q1'],
                dtype='period[Q-DEC]')




```python
#Q代表季度为频率,默认的后缀为DEC代表一年以第1个月为结束【最后一个月为1月份】
pd.period_range("2019-01", periods=13, freq="Q-JAN")
```




    PeriodIndex(['2019Q4', '2020Q1', '2020Q2', '2020Q3', '2020Q4', '2021Q1',
                 '2021Q2', '2021Q3', '2021Q4', '2022Q1', '2022Q2', '2022Q3',
                 '2022Q4'],
                dtype='period[Q-JAN]')




```python
# 以【年为频率】生成13个时间
pd.period_range("2019-01", periods=13, freq="Y")
```




    PeriodIndex(['2019', '2020', '2021', '2022', '2023', '2024', '2025', '2026',
                 '2027', '2028', '2029', '2030', '2031'],
                dtype='period[A-DEC]')




```python
#以【2个月为频率】生成13个时间
pd.period_range("2019-01", periods=13, freq="2m")
```




    PeriodIndex(['2019-01', '2019-03', '2019-05', '2019-07', '2019-09', '2019-11',
                 '2020-01', '2020-03', '2020-05', '2020-07', '2020-09', '2020-11',
                 '2021-01'],
                dtype='period[2M]')



PeriodIndex类保存了一组Period，可以在pandas数据结构中用作轴索引


```python
rng=pd.period_range('1/1/2020','6/30/2020',freq='M')
rng
```




    PeriodIndex(['2020-01', '2020-02', '2020-03', '2020-04', '2020-05', '2020-06'], dtype='period[M]')




```python
period = pd.PeriodIndex(['2020-01', '2020-02', '2020-03', '2020-04', '2020-05', '2020-06'], dtype='period[M]', freq='M')
```


```python
pd.Series(np.random.randn(6),rng)
```




    2020-01    0.670897
    2020-02   -0.122054
    2020-03    0.278258
    2020-04    0.239183
    2020-05    1.580792
    2020-06   -0.527438
    Freq: M, dtype: float64



使用字符串创建PeriodIndex
Q代表季度为频率,默认的后缀为DEC代表一年以第12个月为结束


```python
pd.PeriodIndex(['2020Q3','2020Q2','2020Q1'],freq='Q-DEC')
```




    PeriodIndex(['2020Q3', '2020Q2', '2020Q1'], dtype='period[Q-DEC]')



to_period可以将datetime转period


```python
rng=pd.date_range('1/1/2020',periods=6,freq='D')
ts=pd.Series(np.random.randn(6),index=rng)
ts
```




    2020-01-01   -0.067412
    2020-01-02   -0.152323
    2020-01-03    0.504914
    2020-01-04   -0.381758
    2020-01-05   -1.224619
    2020-01-06    1.029993
    Freq: D, dtype: float64




```python
ts.to_period('M')
```




    2020-01   -0.067412
    2020-01   -0.152323
    2020-01    0.504914
    2020-01   -0.381758
    2020-01   -1.224619
    2020-01    1.029993
    Freq: M, dtype: float64



to_timespame可以将Period转换为时间戳


```python
ts.to_period('M').to_timestamp()
```




    2020-01-01   -0.067412
    2020-01-01   -0.152323
    2020-01-01    0.504914
    2020-01-01   -0.381758
    2020-01-01   -1.224619
    2020-01-01    1.029993
    dtype: float64



# 8.频率转换

时期的频率转换

 在工作中统计数据时，可能会遇到类似于这样的问题，比如将某年的报告转换为季报告或月报告。为了解决这个问题，Pandas中提供了一个
 **asfreq()方法来转换时期的频率，比如把某年转换为某月**。 
 
 **asfreq(freq，method=None，normalize=False，fill_value=None)**

- freq：表示计时单位，可以是DateOffest对象或字符串。
- how：可以取值为start或end，默认为end，仅适用于PeriodIndex。   //start：包含区间开始；end：包含区间结束
- normalize：布尔值，默认为False，表示是否将时间索引重置为午夜。
- fill_value：用于填充缺失值，在升采样期间应用。


```python
# 创建时期对象
period = pd.Period('2017', freq='A-DEC')
period
```




    Period('2017', 'A-DEC')




```python
period.asfreq('M', how='start')
```




    Period('2017-01', 'M')



# 9.重采样

重采样是指根据一类象元的信息内插出另一类象元信息的过程。

Pandas中的resample()是一个对常规时间序列数据重新采样和频率转换的便捷的方法。

在遥感中，重采样是从高分辨率遥感影像中提取出低分辨率影像的过程。如果将高频率数据聚合到低频率，比如将每日采集的数据变成每月采集，则称为降采样，如果将低的频率数据聚合到高频率，比如每个月的采集的数据变成每日采集，则称为升采样。

**resample**

**resample(rule,,axis=0, fill_method=None, closed=None, label=None, convention='start',kind=None, loffset=None, limit=None, base=0)**

rule表示重采样频率字符串或dataoffset

| 参数               | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| freq               | 表示重采样频率，例如‘M'、‘5min'，Second(15)                  |
| how='mean'         | 用于产生聚合值的函数名或数组函数，例如‘mean'、‘ohlc'、np.max等，默认是‘mean'，其他常用的值由：‘first'、‘last'、‘median'、‘max'、‘min' |
| axis=0             | 默认是纵轴，横轴设置axis=1                                   |
| fill_method = None | 升采样时如何插值，比如‘ffill'、‘bfill'等                     |
| closed = ‘right'   | 在降采样时，各时间段的哪一段是闭合的，‘right'或‘left'，默认‘right' |
| label= ‘right'     | 在降采样时，如何设置聚合值的标签，例如，9：30-9：35会被标记成9：30还是9：35,默认9：35 |
| loffset = None     | 面元标签的时间校正值，比如‘-1s'或Second(-1)用于将聚合标签调早1秒 |
| limit=None         | 在向前或向后填充时，允许填充的最大时期数                     |
| kind = None        | 聚合到时期（‘period'）或时间戳（‘timestamp'），默认聚合到时间序列的索引类型 |
| convention = None  | 当重采样时期时，将低频率转换到高频率所采用的约定（start或end）。默认‘end' |



```python
date_index = pd.date_range('2017.7.8', periods=30)
time_ser = pd.Series(np.arange(30), index=date_index)
time_ser
```




    2017-07-08     0
    2017-07-09     1
    2017-07-10     2
    2017-07-11     3
    2017-07-12     4
    2017-07-13     5
    2017-07-14     6
    2017-07-15     7
    2017-07-16     8
    2017-07-17     9
    2017-07-18    10
    2017-07-19    11
    2017-07-20    12
    2017-07-21    13
    2017-07-22    14
    2017-07-23    15
    2017-07-24    16
    2017-07-25    17
    2017-07-26    18
    2017-07-27    19
    2017-07-28    20
    2017-07-29    21
    2017-07-30    22
    2017-07-31    23
    2017-08-01    24
    2017-08-02    25
    2017-08-03    26
    2017-08-04    27
    2017-08-05    28
    2017-08-06    29
    Freq: D, dtype: int64




```python
time_ser.resample('W-MON', closed='left').mean()
```




    2017-07-10     0.5
    2017-07-17     5.0
    2017-07-24    12.0
    2017-07-31    19.0
    2017-08-07    26.0
    Freq: W-MON, dtype: float64



如果重采样时传入closed参数为left，则表示采样的范围是左闭右开型的。
换句话说位于某范围的时间序列中，开头的时间戳包含在内，结尾的时间戳是不包含在内的。

注意：要进行重采样的对象，必须具有时间相关的索引，比如DatetimeIndex，PeriodIndex或TimedetlaIndex。

## (1)降采样

在数位信号处理领域中，降采样，又作减采集,是一种多速率数字信号处理的技术或是降低信号采样率的过程，通常用于降低数据传输速率或者数据大小。 跟插值互补，插值是用来增加取样频率。降采样的过程中会运用滤波器降低混叠造成的失真，因为降采样会有混叠的情形发生，系统中具有降采样功能的部分称为降频器。

降采样时间颗粒会变大，数据量是减少的。为了避免有些时间戳对应的数据闲置，可以利用内置方法聚合数据。

在金融领域中，股票数据比较常见的是OHLC重采样，包括开盘价(open)，最高价，最低价，和收盘价，因此，pandas中专门提供了一个ohlc()的方法，示例代码如下。


```python
date_index = pd.date_range('2018/06/01', periods=30)
shares_data = np.random.rand(30)
time_ser = pd.Series(shares_data, index=date_index)
time_ser
```




    2018-06-01    0.267671
    2018-06-02    0.645319
    2018-06-03    0.174267
    2018-06-04    0.388202
    2018-06-05    0.935069
    2018-06-06    0.106599
    2018-06-07    0.765316
    2018-06-08    0.520641
    2018-06-09    0.697969
    2018-06-10    0.595855
    2018-06-11    0.718246
    2018-06-12    0.671339
    2018-06-13    0.873057
    2018-06-14    0.331703
    2018-06-15    0.055908
    2018-06-16    0.867688
    2018-06-17    0.732829
    2018-06-18    0.791078
    2018-06-19    0.696824
    2018-06-20    0.867281
    2018-06-21    0.611386
    2018-06-22    0.278577
    2018-06-23    0.749945
    2018-06-24    0.854207
    2018-06-25    0.498311
    2018-06-26    0.468163
    2018-06-27    0.571037
    2018-06-28    0.985595
    2018-06-29    0.700245
    2018-06-30    0.516342
    Freq: D, dtype: float64




```python
time_ser.resample('7D').ohlc()  # OHLC降采样

#会把01-07的开始，最高，最低和07号的输出
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2018-06-01</th>
      <td>0.267671</td>
      <td>0.935069</td>
      <td>0.106599</td>
      <td>0.765316</td>
    </tr>
    <tr>
      <th>2018-06-08</th>
      <td>0.520641</td>
      <td>0.873057</td>
      <td>0.331703</td>
      <td>0.331703</td>
    </tr>
    <tr>
      <th>2018-06-15</th>
      <td>0.055908</td>
      <td>0.867688</td>
      <td>0.055908</td>
      <td>0.611386</td>
    </tr>
    <tr>
      <th>2018-06-22</th>
      <td>0.278577</td>
      <td>0.985595</td>
      <td>0.278577</td>
      <td>0.985595</td>
    </tr>
    <tr>
      <th>2018-06-29</th>
      <td>0.700245</td>
      <td>0.700245</td>
      <td>0.516342</td>
      <td>0.516342</td>
    </tr>
  </tbody>
</table>
</div>



降采样相当于另外—种形式的分组操作，它会按照日期将时间序列进行分组，之后对每个分组应用聚合方法得出一个结果。

上述示例输出了2018年的开盘价，最高价，最低价，收盘价，注意，这些股票数据都是随机数，只共大家学习使用，并不是真实数据。


```python
# 通过groupby技术实现降采样
time_ser.groupby(lambda x: x.week).mean()
```

    /var/folders/ns/x8d70sy17cd0lbs0r26bxv840000gn/T/ipykernel_46144/1333423618.py:2: FutureWarning: weekofyear and week have been deprecated, please use DatetimeIndex.isocalendar().week instead, which returns a Series. To exactly reproduce the behavior of week and weekofyear and return an Index, you may call pd.Int64Index(idx.isocalendar().week)
      time_ser.groupby(lambda x: x.week).mean()





    22    0.362419
    23    0.572807
    24    0.607253
    25    0.692757
    26    0.623282
    dtype: float64



## (2)升采样

升采样的时间颗粒是变小的，数据量会增多，这很有可能导致某些时间戳没有相应的数据。

时间序列的数据在采样的时候，总体的数据量是减少的，只需要从最高频向最低频率转换时，应用聚合函数即可。

如果里面没有数据的话，遇到这种情况，常用的解决办法就是插值，具体有如下几种方式:

通过ffill(limit)(或者)bfill(limit)方法，取空前面或后面的值填充, limit可以限制填充的个数。值填充, limit可以限制填充的个数。

通过fillna(‘ffill’)或fillna(‘bfill’)进行填充，传入ffill则表示用NaN前面的值填充，传入bfill则表示用后面的值填充。

使用interpolate()方法根据插值算法补全数据。


```python
data_demo = np.array([['101', '210', '150'], ['330', '460', '580']])
data_demo
```




    array([['101', '210', '150'],
           ['330', '460', '580']], dtype='<U3')




```python
date_index = pd.date_range('2018/06/10', periods=2, freq='W-SUN')
date_index

```




    DatetimeIndex(['2018-06-10', '2018-06-17'], dtype='datetime64[ns]', freq='W-SUN')




```python
time_df = pd.DataFrame(data_demo, index=date_index, columns=['A产品', 'B产品', 'C产品'])
time_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>A产品</th>
      <th>B产品</th>
      <th>C产品</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2018-06-10</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-17</th>
      <td>330</td>
      <td>460</td>
      <td>580</td>
    </tr>
  </tbody>
</table>
</div>



上述样本数据是从2018年6月10日开始采集，每一周统计一次，总共统计了两周，现在有个新的1需求，要求重新按天采样，这是需要使用resample，和asfreq()这两个方法实现。asfreq()方法会将数据指定频率，示例代码如下：


```python
time_df.resample('D').asfreq()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>A产品</th>
      <th>B产品</th>
      <th>C产品</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2018-06-10</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-11</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-12</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-13</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-14</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-15</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-16</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2018-06-17</th>
      <td>330</td>
      <td>460</td>
      <td>580</td>
    </tr>
  </tbody>
</table>
</div>




```python
time_df.resample('D').ffill()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>A产品</th>
      <th>B产品</th>
      <th>C产品</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2018-06-10</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-11</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-12</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-13</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-14</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-15</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-16</th>
      <td>101</td>
      <td>210</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2018-06-17</th>
      <td>330</td>
      <td>460</td>
      <td>580</td>
    </tr>
  </tbody>
</table>
</div>



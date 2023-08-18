
# SqlSugar学习

## 目前常用的orm框架

1、Dapper

2、EF/EFCore

3、FreeSql

4、DOS.ORM

5、SqlSugar--国产ORM框架

<!--注释：就是用来操作数据库的一种组件-->

翻译过来：就是对象关系映射

对于数据库的操作----我们可以只关注对象（面向对象的设计语言）的操作

## ADO.NET

```mermaid
graph LR
Application--ADO.NET-->DB
```

组装Sql语句

数据库而言：只认识Sql语句

需要建大量的Sql语句

优点：性能高

## O/RM框架

```mermaid
graph LR
Application["APPlication/Orm"]-->DB
```

Orm包含在在Application里面，不需要像Ado.Net那样单独拿出来做组件

特点：不用关注太多的Sql语句----关注对象----操作对象---对于对象的操作---落实到数据库中----类似于一个操作数据库的代理---类

<!--注释：ORM的底层还是Ado.Net，Ado.net的底层就是ODBC-->

1、可以通过对于对象的操作（面向对象的思想）----完成做到对于数据库的操作---开发思想的统一

2、足够便捷---上手简单

3、性能有点问题----着手解决

Object  mapping Relation

可以把对象和数据库对应起来；

## SqlSugar现状

1、开源的ORM框架

2、支持自动分表组件---分库

3、支持SAAS分库和到数据处理的ORM

4、百万级大数据的写入、更新和读取

5、高性能



## Demo一

```C#
//说清ORM框架的前世今生，SqlSugar闪亮登场，轻松落地CRUD。探讨解决SqlServer数据库，高性能的方案，弄清楚数据库二八原则
                //SqlSugar读写分离快速搭建，SqlSugar完美支持读写分离的解决方案。

                //SqlSugar基本操作
                //一、nuget引入SqlSugar包
                //二、配置链接信息
                ConnectionConfig connectionConfig = new ConnectionConfig()
                {
                    DbType = DbType.SqlServer,
                    IsAutoCloseConnection = true,
                    ConnectionString = "Data Source=RANKADMINS;Initial Catalog=CustomerDB;Persist Security Info=True;User ID=sa;Password=123456"
                };
                //自动释放 不需要手动释放
                using (SqlSugarClient db = new SqlSugarClient(connectionConfig)) {
                    Student student = new Student()
                    {
                        Name="秋风",
                        SchoolId=1
                    };

                    //第一步：新增一个对象
                    int iResult = db.Insertable<Student>(student).ExecuteCommand();
                    //第二步：更新
                    Student stuDto = db.Queryable<Student>().OrderByDescending(s => s.Id).First();
                    stuDto.Name = "Rank.Zhu";
                    db.Updateable<Student>(stuDto).ExecuteCommand();

                    //删除这个对象
                    db.Deleteable<Student>(stuDto).ExecuteCommand();
                }
```

## 数据库的操作

数据库的操作就是4种：

CURD---增删改查

两类：1、增删改   2、查询

完成业务操作--操作数据的时候：二八原则（80%的财富掌握在20%手中）

业务完成--操作数据库--二八原则：

20%---写入---增删改

80%---操作是查询

### 抖音商城

浏览--用户的浏览量

直播带货---用户浏览小黄车---下单

### 买鼠标---行为

可能浏览一圈，可能下单买，也可能不买



---只要是看--不买-----那就都是查询

如果买了----增加订单----增加物流信息----增加支付信息-----数据库的写入操作

**总结：80%以上会聚焦在查询上----如果出现性能问题----插叙操作必然是中灾区**

**考虑查询问题进行集群----先去集群查询动作----让更多的服务器来支持查询的动作。**



### 二八原则

读写分离：

一个主库负责20%的写入---增删改

多个从库:80%的查询动作---查询级别的负载均衡。

问题：

主库写入，从库查询----数据复制；

通过日志恢复：快

必然有延迟，有缺陷----可以解决

![image-20230607094113606](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607094113606.png)

要求：数据库服务器在同一个域网络中

## 设计读写分离

### 步骤一：新建共享文件夹：replData

（D:\project\replData），特别注意: 需要设置共享 权限读写（everyone）

### 步骤二：启用SqlServer代理

### 步骤三：新建发布



![image-20230607160331818](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607160331818.png)





![image-20230607160545563](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607160545563.png)

本地选

![image-20230607160651580](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607160651580.png)

### 步骤四：创建本地订阅

![image-20230607161137819](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607161137819.png)

新建订阅数据库

![image-20230607161236338](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607161236338.png)

![image-20230607161518793](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607161518793.png)

选择之前的代理

![image-20230607161803026](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230607161803026.png)

下面都是  下一步  直到完成



### 步骤五：读写分离源码实现

```c#
{
                    ConnectionConfig config = new ConnectionConfig()
                    {
                        ConnectionString = "Data Source=RANKADMINS;Initial Catalog=CustomerDB;Persist Security Info=True;User ID=sa;Password=123456",
                        IsAutoCloseConnection = true,
                        DbType = DbType.SqlServer,
                        //设置从库 
                        //0001 ,0002 两个从库
                        SlaveConnectionConfigs = new List<SlaveConnectionConfig>(){
                            new SlaveConnectionConfig() {
                                ConnectionString="Data Source=RANKADMINS;Initial Catalog=CustomerDB_new0001;Persist Security Info=True;User ID=sa;Password=123456",
                                HitRate=10//设置权重  偏向哪个库
                            },
                            new SlaveConnectionConfig(){
                                ConnectionString="Data Source=RANKADMINS;Initial Catalog=CustomerDB_new0002;Persist Security Info=True;User ID=sa;Password=123456",
                                HitRate=10//设置权重  偏向哪个库
                            }
                        }
                    };

                    //读写分离  写入在主库  读写在从库
                    using (SqlSugarClient clientclient = new SqlSugarClient(config))
                    {

                        //for (int i = 0; i < 3; i++)
                        //{
                        //    Student student1 = clientclient.Queryable<Student>().First(c => c.Id == 21);
                        //} 
                        Student student = new Student()
                        {
                            Name = "Advance--读写分离~",
                            SchoolId = 1
                        };
                        int iResult = clientclient.Insertable<Student>(student).ExecuteCommand();
                        Student student1 = clientclient.Queryable<Student>().OrderByDescending(c => c.Id).First();
                        student1.Name = "Rank.Zhu";
                        clientclient.Updateable<Student>(student1).ExecuteCommand();
                        clientclient.Deleteable<Student>(student1).ExecuteCommand();
                    }
                }
```

### 步骤六：验证主从库数据延迟问题

```C#
{
                    ConnectionConfig config = new ConnectionConfig()
                    {
                        ConnectionString = "Data Source=RANKADMINS;Initial Catalog=CustomerDB;Persist Security Info=True;User ID=sa;Password=123456",
                        IsAutoCloseConnection = true,
                        DbType = DbType.SqlServer,
                        //设置从库 
                        //0001 ,0002 两个从库
                        SlaveConnectionConfigs = new List<SlaveConnectionConfig>(){
                            new SlaveConnectionConfig() {
                                ConnectionString="Data Source=RANKADMINS;Initial Catalog=CustomerDB_new0001;Persist Security Info=True;User ID=sa;Password=123456",
                                HitRate=10//设置权重  偏向哪个库
                            },
                            new SlaveConnectionConfig(){
                                ConnectionString="Data Source=RANKADMINS;Initial Catalog=CustomerDB_new0002;Persist Security Info=True;User ID=sa;Password=123456",
                                HitRate=10//设置权重  偏向哪个库
                            }
                        }
                    };
                    bool isGoOn = true;
                    int i = 1;
                    using (SqlSugarClient db = new SqlSugarClient(config))
                    {
                        //先清除数据
                        Student stuDtto =await db.Queryable<Student>().Where(s => s.Name == "测试延迟问题").FirstAsync();
                        if (stuDtto != null)
                        {
                            await db.Deleteable<Student>(stuDtto).ExecuteCommandAsync();
                        }
                        {
                            //新建一个对象
                            Student student = new Student()
                            {
                                Name = "测试延迟问题",
                                SchoolId = 1
                            };
                            int iResult =await db.Insertable<Student>(student).ExecuteCommandAsync();
                        }
                        Stopwatch stopwatch = Stopwatch.StartNew();
                        while (isGoOn)
                        {
                            Student stuDto =await db.Queryable<Student>().Where(s => s.Name == "测试延迟问题").FirstAsync();
                            if (stuDto != null)
                            {
                                Console.WriteLine($"已经查询到数据~~~,不再继续查询");
                                break;
                            }
                            else
                            {
                                Console.WriteLine($"第{i}此查询，没有查到数据~~~");
                            }
                            i++;
                        }

                        stopwatch.Stop();
                        Console.WriteLine($"查询完毕，共耗时:{stopwatch.ElapsedMilliseconds}毫秒");
                    }
                }
```

![image-20230608090152321](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230608090152321.png)



# 海量数据设计方案

## 千万级---亿级数据量

1、读写分离---每个数据库都是保存相同的数据

2、数据到达千万级、亿级数据量后，读写分离会很渺小

3、因为读写分离的主从库 存储的都是相同的数据源

4、就算一主十从，每个表都保存亿万级数据，就算分摊了操作，任然很慢，咋办呢



数据量太大的问题~~~读写分离

每个库都存储了大量的数据

**查询---最终会跑到某一个库里**

**在大量的数据的库里  性能没有保障**

## 大数据量存储的终极方案：拆分

化整为零，对于大的数据量修改为小的数据量，**把数据库数据表从一个数据库一个数据表，拆分成多个数据库/多个数据表**。

<!--难度系数很大，实在找不到其他方案，这个是最后的杀招-->

1、分库----分表

2、纵向分库----横向分库

3、纵向分表----横向分表

<!--数据量太大----所有的问题都是数量太大造成，所以需要分库分表减少数据量-->

## 数据库分库----水平分库

| ![image-20230608110841826](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230608110841826.png) | 数据库中共计5张表，每个表存储10万条数据.水平分库：5个库-----结构都一样：还是5个表，保存的数据不一样，每个库保存2W 条数据 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

![image-20230608134802563](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230608134802563.png)



### **特点：**

1、每个数据库的结构都一样

2、每个数据库中存储的数据不一样，没有交集，所有库并集就是全部数据量

### 结论：

库多了，io和CPU的压力自然成倍的缓解，根据时间，地区

### 建议:

拆分：需要关注拆分的维度，拆分之后，尽可能的不要让开发工作变得太复杂

## 案例----水平分库

1、房产项目---按区域划分----【上海】/【南京】/【苏州】/【无锡】/【常州】/【镇江】...

2、根据存储时间分

2023年  Data

2022年  Data

2021年  Data

房产项目---全国，如果放在一个数据库中保存，体量必然很大

房产项目是做全国的，可以根据全国多少个城市，那么就分多少个数据库。

房产项目从业务上更加聚焦于某一个区域；

## 数据库分库----。垂直分库（纵向分库）

![image-20230608135035947](C:\Users\zhujx\AppData\Roaming\Typora\typora-user-images\image-20230608135035947.png)

纵向分库：多个数据库来瓜分数据表

### 结果:

1、每个数据库的结构都不一样。

2、每个数据库的数据也不一样，没有交集

3、所有数据库的并集是全量的数据

### 场景：

系统绝对并发量上来之后，并且可以抽象出单独模块的情况下。

#### 分析：

到这一步：基本上可以就可以服务化了。

例如：随着公司业务的发展，一些公共配置表，字典数据会越来越多，这时，可以将这些数据单独的放到一个数据库中，

甚至服务化。

再者，随着业务的发展，孵化出新的业务模式，没有将相关表放到单独的数据库，甚至服务化。

## 案例----垂直分库

电商平台----从业务划分----

1、商品----->单独存放一个数据库

2、订单----->单独存放一个数据库

3、会员----->单独存放一个数据库

4、物流----->单独存放一个数据库

每一个数据库聚焦一个业务模块业务；

## 数据库分表----水平拆分

### 结果：

1、每一个表的结构都一样

2、每个表的数据不一样，没有交集，所有表的并集是表的全量数据

### 分析：

单表的数量变少了，单次执行Sql的效率就变高了，自然减轻了CPU的负担

### 案例：

订单表的水平拆分，按照时间拆分：

2018年01月01日------2018年12月31日--------------->对立成一个数据表  Order_2018

2019年01月01日------2019年12月31日--------------->对立成一个数据表  Order_2019

2020年01月01日------2020年12月31日--------------->对立成一个数据表  Order_2020

2021年01月01日------2021年12月31日--------------->对立成一个数据表  Order_2021

2022年01月01日------2022年12月31日--------------->对立成一个数据表  Order_2022

2023年01月01日------2023年12月31日--------------->对立成一个数据表  Order_2023



### 问题：

如果一个表，没有时间，没有类型，没有一个核准的维度；要分表---如何拆分？

Id没有规则---没有办法按照数据表中某一个维度来拆分；

#### 相对平均的保存方法

不叫绝对平均

1000万----分了5各表  ----第一个表：150万  第二个160万.....

一亿条数据；化整为零；只希望一个表只保存1000万的数据。

分10个表，20字段；如何用10个表保存所有数据

思路：

分10个表，可以带后缀

数据保存 使用数据Id----转换成字符串-----取hashCode,通过取余：HashCode/10=0 1 2 3 4 5 6 7...

定义一个规则

1、保存数据可以根据这个规则保存到对应的数据表中

2、查询的时候可以根据这个规则，决定取哪个数据库里进行查询...

## 数据库分表----垂直拆分

分析：可以用列表和详细页来理解。垂直拆分的原则是将热数据（经常会查询的数据）放到一起作为主表，非热点数据放在一起作为扩展表，这样

可以将更多的热点数据缓存起来，进而减少了随机读IO，拆了之后要想读全部数据，需要关联两张表。

但记住千万不要用join,因为JION不仅会增加CPU的负担并且会将两个表耦合在看一起（必须在同一个数据库实例上）。关联数据应该在

Service层进行，分别获取主表字段和扩展表数据，然后用关联字段关联得到所有数据。

### 概念：

以字段为依据，按照字段的活跃性，将表中的字段拆分到不同的表中（主表和扩展表），多个表瓜分字段。

### 结果：

1、每个表结构不一样。

2、每个表的数据也不一样，每个表的字段至少有一个是一样的，一般是主键，用于关联数据。

3、所有表的并集是全量数据。

### 场景：

系统绝对的并发量没有上来，表的记录并不多，但是字段比较多，并且热点和非热点的数据在一起，单行数据需要的存储空间大，以至于数据库的缓存的数据行减少，查询回磁盘读数据产生大量随机读IO，产生IO瓶颈。



## 垂直分表的案例：

按照字段拆分，表变多了，把字段瓜分掉；

主外键关系保存数据，在主表和从表里同时保存一个ID

## 分库分表后：

1、复杂度增加了

2、操作一个表---->操作多个表

3、操作一个数据库---->操作多个数据库

ORM能否适配分库分表呢

部分ORM是轻松支持，部分ORM是无法支持的，需要手动取扩展支持。

SqlSugar----应对分库分表----轻松支持



## SqlSugar利器----自动分表

### 特点：

只需要做简单配置，不需要靠考虑分表规则。规则他自己定制了，我们可以直接使用他们定义好的规则

1、配置按照年度分表

2、配置按照季度分表

3、配置按照阅读分表

4、配置按照周分表

5、配置按照日分表

时间分表：SqlSugar对于业务的操作上基本零影响。

# 操作实例Demo实现



## 按照年份分表：

### Model定义

关键：

<!--1、分表规则：[SplitTable(SplitType.Year)]-->

<!--2、数据库表的呈现方式：[SugarTable("CommoditySubTableYear_{year}{month}{day}")]-->

<!--3、拆分维度(以哪个字段来分表，注意该字段需要放在第一位)：[SplitField]-->

```C#
    [SplitTable(SplitType.Year)]//按照年分表
    [SugarTable("CommoditySubTableYear_{year}{month}{day}")]
    public class CommoditySubTableYear
    {
        /// <summary>
        /// 创建时间  设置成分表规则维度 需要放在第一位
        /// </summary>
        [SplitField]
        public DateTime? CreateTime { get; set; }
        /// <summary>
        /// 
        /// </summary>
        [SugarColumn(IsPrimaryKey = true)]
        public Guid Id { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public long? ProductId { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public int? CategoryId { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public string? Title { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public decimal? Price { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public string? Url { get; set; }

        /// <summary>
        /// Desc:
        /// Default:
        /// Nullable:True
        /// </summary>           
        public string? ImageUrl { get; set; }
        
    }
```

### 调用Model实现分表处理

<!--注意：调用的时候 申明分表处理-->

```c#
ConnectionConfig config = new ConnectionConfig()
{
   DbType=DbType.SqlServer,
   ConnectionString= "",
   IsAutoCloseConnection=true
};
using (SqlSugarClient db=new SqlSugarClient(config))
{
    //按照年份自动分表
    {
          List<CommoditySubTableYear> commoditilist = new List<CommoditySubTableYear>()
          {
              new CommoditySubTableYear()
              {
                  CategoryId = 1,
                  CreateTime = DateTime.Now.AddYears(-2), //2021年
                  ImageUrl = "images",
                  Price = 344,
                  ProductId = 234,
                  Title = "分表测试--2021",
                  Url = "URL",
                  Id=Guid.NewGuid()
              },
              new CommoditySubTableYear()
              {
                  CategoryId = 1,
                  CreateTime = DateTime.Now.AddYears(-1),  //2022年
                  ImageUrl = "images",
                  Price = 344,
                  ProductId = 234,
                  Title = "分表测试--2022",
                  Url = "URL",
                  Id=Guid.NewGuid()
              },
              new CommoditySubTableYear()
              {
                  CategoryId = 1,
                  CreateTime = DateTime.Now,  //2023年
                  ImageUrl = "images",
                  Price = 344,
                  ProductId = 234,
                  Title = "分表测试--2023",
                  Url = "URL",
                  Id=Guid.NewGuid()
              }

          };
        int iResult = db.Insertable<CommoditySubTableYear>(commoditilist)
            .SplitTable() //操作数据的时候, 支持自动的分表
            .ExecuteCommand();
    }
}
```

## 按月份分表：

### Model定义

注意：

​    [SplitTable(SplitType.Month)]--分表规则
​    //[SugarTable("CommoditySubTableMonth_{year}_{month}_{day}")]
​    [SugarTable("CommoditySubTableMonth_{month}_{day}")]//做同比
​    public partial class CommoditySubTableMonth

```C#
namespace Rank.NET7.DbDal
{
    /// <summary>
    /// 按照月份进行分表
    /// </summary>
    [SplitTable(SplitType.Month)]
    //[SugarTable("CommoditySubTableMonth_{year}_{month}_{day}")]
    [SugarTable("CommoditySubTableMonth_{month}_{day}")]//做同比
    public partial class CommoditySubTableMonth
    {
        /// <summary>
        /// 分表维度 创建时间  是以创建时间进行分表
        /// </summary>
        [SplitField]
        public DateTime? CreateTime { get; set; }
        /// <summary>
        /// 设置Id做主键
        /// </summary>
        [SugarColumn(IsPrimaryKey = true)]
        public Guid Id { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public long? ProductId { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public int? CategoryId { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public string? Title { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public decimal? Price { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public string? Url{get;set;}
        /// <summary>
        /// 
        /// </summary>
        public string? ImageUrl { get; set; }
    }
}
```

### 调用Model实现分表处理

```C#
ConnectionConfig config = new ConnectionConfig()
{
    DbType = DbType.SqlServer,
    ConnectionString = "",
    IsAutoCloseConnection = true
};
{
    List<CommoditySubTableMonth> commitlist = new List<CommoditySubTableMonth>()
    {
        new CommoditySubTableMonth(){
            Id= Guid.NewGuid(),
            Url= "URL",
            Title="Rank.Zhu按照月份分表---202306",
            CategoryId = 1,
            CreateTime = DateTime.Now,  //2023年
            ImageUrl = "images",
            Price = 344,
            ProductId = 234,
        },new CommoditySubTableMonth(){
            Id= Guid.NewGuid(),
            Url= "URL",
            Title="Rank.Zhu按照月份分表---202305",
            CategoryId = 1,
            CreateTime = DateTime.Now.AddMonths(-1),  //2023年
            ImageUrl = "images",
            Price = 344,
            ProductId = 234,
        },
        new CommoditySubTableMonth(){
            Id= Guid.NewGuid(),
            Url= "URL",
            Title="Rank.Zhu按照月份分表---202204",
            CategoryId = 1,
            CreateTime = DateTime.Now.AddYears(-1).AddMonths(-2),  //2023年
            ImageUrl = "images",
            Price = 344,
            ProductId = 234,
        },
        new CommoditySubTableMonth(){
            Id= Guid.NewGuid(),
            Url= "URL",
            Title="Rank.Zhu按照月份分表---202204",
            CategoryId = 1,
            CreateTime = DateTime.Now.AddYears(-1),  //2023年
            ImageUrl = "images",
            Price = 344,
            ProductId = 234,
        }
    };
    int iResult = db.Insertable<CommoditySubTableMonth>(commitlist).SplitTable().ExecuteCommand();
   }
}
```

## 重磅：自定义实现地区分表

### Model定义：

<!--注释：自定义规则-->

_Custom01:自定义

[SplitTable(SplitType._Custom01)]//按年分表 （自带分表支持 年、季、月、周、日）
[SugarTable("CommoditySubTableDayArea")]//生成表名格式 3个变量必须要有



省份作为分表维度

/// <summary>

/// 省份
/// </summary>
[SplitField] //分表的维度
public string? Province{get;set;}

```C#
namespace Rank.NET7.DbDal
{
    /// <summary>
    /// 
    /// </summary>
    [SplitTable(SplitType._Custom01)]//按年分表 （自带分表支持 年、季、月、周、日）
    [SugarTable("CommoditySubTableDayArea")]//生成表名格式 3个变量必须要有
    public partial class CommoditySubTableDayArea
    {
        /// <summary>
        /// 省份
        /// </summary>
        [SplitField] //分表的维度
        public string? Province{get;set;}

        /// <summary>
        /// 主键 
        /// </summary>
        [SugarColumn(IsPrimaryKey =true)]
        public Guid Id { get; set; }

        /// <summary>
        /// 
        /// </summary>
        public long? ProductId { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public int? CategoryId { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public string? Title { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public decimal? Price { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public string? Url { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public string? ImageUrl { get; set; }
        /// <summary>
        /// 
        /// </summary>
        public DateTime CreateTime { get; set; }
    }
}
```



### 最重要的过程：

实现分表接口：ISplitTableService



```C#
namespace Rank.Net7.SqlSugar
{
    /// <summary>
    /// 这个类中  是实现的具体的分表规则
    /// 写入数据的时候，明确写清楚，写到哪个表中去，读取数据的时候，数据需要从哪个表中读取
    /// 
    /// 需要实现接口
    /// </summary>
    public class AreaSubTableService : ISplitTableService
    {

        public static Dictionary<string, string> _AreaDictionary;



        /// <summary>
        /// 整个进程中，执行且只执行一次；
        /// </summary>
        static AreaSubTableService()
        {
            ///根据不同的省份名称，各自分的表，以省份名称的拼音作为后缀；
            _AreaDictionary = new Dictionary<string, string>()
            {
                {"Liaoning","辽宁" },
                {"Heilongjiang","黑龙江" },
                {"Jilin","吉林" },
                {"Hebei","河北" },
                {"Henan","河南" },
                {"Shandong","山东" },
                {"Shaanxi","陕西" },
                {"ShanxiS","山西" },
                {"Jiangsu","江苏" },
                {"Zhejiang","浙江" },
                {"Fujian","福建" },
                {"Guangdong","广东" },
                {"Hainan","海南" },
                {"Yunnan","云南" },
                {"Jiangxi","江西" },
                {"Hunan","湖南" },
                {"Hubei","湖北" },
                {"Sichuan","四川" },
                {"Gansu","甘肃" },
                {"Guizhou","贵州" }
            };
        }



        /// <summary>
        /// 获取所有的表名称
        /// </summary>
        /// <param name="db"></param>
        /// <param name="EntityInfo"></param>
        /// <param name="tableInfos"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public List<SplitTableInfo> GetAllTables(ISqlSugarClient db, EntityInfo EntityInfo, List<DbTableInfo> tableInfos)
        {
            return tableInfos
                .Where(c => c.Name.Contains("_Area_")) //注意是包含不是Equal
                .Select(c => new SplitTableInfo()
                {
                    TableName = c.Name //要用item.name不要写错了
                })
                .OrderBy(it => it.TableName).ToList();
        }

        /// <summary>
        ///  根据在实体中标记的分表依据字段，获取字段的值
        /// </summary>
        /// <param name="db"></param>
        /// <param name="entityInfo"></param>
        /// <param name="splitType"></param>
        /// <param name="entityValue"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public object GetFieldValue(ISqlSugarClient db, EntityInfo entityInfo, SplitType splitType, object entityValue)
        {
            var splitColumn = entityInfo.Columns.FirstOrDefault(it => it.PropertyInfo.GetCustomAttribute<SplitFieldAttribute>() != null);
            return splitColumn.PropertyInfo.GetValue(entityValue, null);
        }

        /// <summary>
        /// 获取表名称
        /// </summary>
        /// <param name="db"></param>
        /// <param name="EntityInfo"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public string GetTableName(ISqlSugarClient db, EntityInfo EntityInfo)
        {
            return EntityInfo.DbTableName + "_Area";
        }

        /// <summary>
        /// 获取表名称
        /// </summary>
        /// <param name="db"></param>
        /// <param name="EntityInfo"></param>
        /// <param name="type"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public string GetTableName(ISqlSugarClient db, EntityInfo EntityInfo, SplitType type)
        {
            return EntityInfo.DbTableName + "_Area";
        }

        /// <summary>
        /// 获取表名称
        /// </summary>
        /// <param name="db"></param>
        /// <param name="entityInfo"></param>
        /// <param name="splitType"></param>
        /// <param name="fieldValue"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public string GetTableName(ISqlSugarClient db, EntityInfo entityInfo, SplitType splitType, object fieldValue)
        {
            KeyValuePair<string, string> keyPar = _AreaDictionary
             .FirstOrDefault(c => fieldValue.ToString().Contains(c.Value));
            return entityInfo.DbTableName + "_Area_" + keyPar.Key; //根据值按首字母

        }
    }
}
```

### 调用Model实现分表处理：

实例化接口：注意这句话  必需

否则报错

db.CurrentConnectionConfig.ConfigureExternalServices.SplitTableService = new AreaSubTableService();

```C#
namespace Rank.Net7.SqlSugar
{
    /// <summary>
    /// 自定义分表规则
    /// </summary>
    
    public partial class CustomSubTableDemo
    {
        public static void ShowCustomSubTable()
        {
            ConnectionConfig config = new ConnectionConfig()
            {
                DbType = DbType.SqlServer,
                ConnectionString = "",
                IsAutoCloseConnection = true
            };

            using (SqlSugarClient db = new SqlSugarClient(config)) {

                //比较重要 如果不加  中文提示 : 分表表名需要占位符 {year}
                db.CurrentConnectionConfig.ConfigureExternalServices.SplitTableService = new AreaSubTableService();
                db.CodeFirst
                  .SplitTables()//标识分表
                   .InitTables<CommoditySubTableDayArea>(); //程序启动时加这一行,如果一张表没有会初始化一张


                List<CommoditySubTableDayArea> addmodelList = new List<CommoditySubTableDayArea>();
                for (int i = 0; i < 10; i++)
                {
                    foreach (var dic in AreaSubTableService._AreaDictionary)
                    {
                        addmodelList.Add(new CommoditySubTableDayArea()
                        {
                            CategoryId = 1,
                            CreateTime = DateTime.Now,
                            ImageUrl = $"Images_{i}",
                            Price = 345,
                            ProductId = i,
                            Province = dic.Value,
                            Title = "这里是一条测试数据",
                            Url = "URL"
                        });
                    }
                }


                //db.Insertable(addmodelList).SplitTable().ExecuteCommand();
                //读取表
                var listAll = db.Queryable<CommoditySubTableDayArea>().Where(c => c.Province.Contains("江苏")).SplitTable(m => m.ContainsTableNames("_Area_")).ToList();
                //只查A表
                var listall = db.Queryable<CommoditySubTableDayArea>().Where(it => it.Province.Contains("湖北")).SplitTable(tas => tas.ContainsTableNames("_Area_")).ToList();
            }
        }
    }
}
```


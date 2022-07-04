---
title: 学习-01-使用Beancount+Fava记账

date: 2022-06-28 09:15:24

categories:

- Learning

tags:

- Beancount
- Bookkeeping

---

## 起因

俗话说：吃不穷，穿不穷，算计不到才受穷，最近开始受穷了，于是想要好好算算帐，看看自己是怎么受穷的。前几年断断续续用过几个手机记账APP，但是收支分析，甚至账单导出等核心功能，都需要付费才能解锁，可是这些功能都非常容易实现，我自己动手都能写出来。于是怀揣着饿死同行的想法，我开始了写自己的记账App。

某天喝多了之后发现自己调入了一个思维陷阱，既然最终目的是实现账单分析，那么为什么要在手机那巴掌大的一块屏幕上看呢？咱是干什么的，咱可是魔法少女。

恰巧这时，看到关注Dalao的博客写到了用Beancount+Fava管理家庭财务，```beancount提供管账服务```，```fava提供前端页面```
，看到了没，用这货是管理财务，除了记账功能外，还有已有财产管理、债务管理、收入花销，于是赶紧安排，并且开始记账。

下面是我安装Beancount的踩坑记录：

## 安装

### 环境安装

1. Python 3.X
    1. [官方网站](https://www.python.org/downloads/) 下载Python SDK
    2. 安装Python SDK
    3. 检查环境变量
        - 一般安装完成后，会自动配置环境变量，如果没有需要手动配置，方法如下
          > 右键点击"计算机"，然后点击"属性"；
          >
          > 然后点击"高级系统设置"；
          >
          > 选择"系统变量"窗口下面的"Path",双击即可；
          >
          > 然后在"Path"行，添加python安装路径(比如```D:\Python32;```)，注意应以英文分号结尾；
          >
          > 然后在"Path"行，添加python脚本路径(比如```D:\Python32\Scripts;```)，注意应以英文分号结尾；

    4. 测试Python SDK
        - 在CMD窗口里输入```python```，如有输出即安装成功，如无，需检查python安装情况，以及环境变量
        - 在CMD窗口里输入```pip```，如有输出即安装成功，如无，需检查python安装情况，以及环境变量

2. 与Python版本对应的Microsoft Visual C++ BuildTools(生成工具)
    1. [官方网站](https://visualstudio.microsoft.com/zh-hans/downloads/)下载最新版Visual Studio
    2. 安装时，注意需要选择Microsoft Visual C++ BuildTools(生成工具)

### Beancount安装

如果上面的环境安装顺利，此处仅需一句CMD命令：

```shell
pip install beancount
```

### fava安装

如果上面的环境安装顺利，此处仅需一句CMD命令：

```shell
pip install fava
```

## Beancount使用

英文谚语说得好：```There's more than one way to skin a cat. ```
剥猫皮的方法不只一种，记账的玩法也很多，这里讲两种最基础的玩法，一种是所有东西全塞一个文件里，另一种是按功能划分目录，每个目录盛放不同的文件

~~~
方案一
MyLeder 我的账单                             
├──  main.bean   启动文件
│            
 
~~~

~~~
MyLeder 我的账单                             
├── main.bean   入口文件
├── accounts    我的账户
│       └── assets.bean         资产账户
│       └── equity.bean         初始化账户
│       └── expenses.bean       消费类别账户
│       └── income.bean         收入类别账户
│       └── liabilities.bean    负债账户
├── ledgers     我的账单
│       └── YYYY                YYYY年度目录
│             └── expenses            
│                  └── qQ-MM-expenses.bean          Q季度M月份消费账单    
│             └── income
│                  └── annual-income-transfer.bean  年度转账收入    
│                  └── qQ-MM-income-interest.bean   Q季度M月份利息收入
│             └── liquidity
│                  └── qQ-liquidity.bean            Q季度账户间流水
│
~~~

### 方案一

1. 创建账本

> 创建一个文件命名main.bean，内容如下

 ```
;【一、账本信息】
option "title" "我的账本" ;账本名称
option "operating_currency" "CNY" ;账本主货币

;【二、账户设置】
;1、开设账户
1990-01-01 open Assets:Card:1234 CNY, USD ;尾号1234的银行卡，支持CNY和USD
1990-01-01 open Liabilities:CreditCard:5678 CNY, USD ;双币信用卡
1990-01-01 open Income:Salary CNY ;工资收入
1990-01-01 open Expenses:Tax CNY ;交税
1990-01-01 open Expenses:Traffic:Taxi CNY ;打车消费，只支持CNY
1990-01-01 open Equity:OpenBalance ;用于账户初始化，支持任意货币

;2、账户初始化
2019-08-27 * "" "银行卡，初始余额10000元"
    Assets:Card:1234           10000.00 CNY
    Equity:OpenBalance        -10000.00 CNY

;【三、交易记录】
2019-08-28 * "杭州出租车公司" "打车到公司，银行卡支付"
    Expenses:Traffic:Taxi        200.00 CNY
    Assets:Card:1234            -200.00 CNY

2019-08-29 * "" "餐饮"
    Assets:Card:1234           -1100.00 CNY
    Liabilities:CreditCard:5678 1100.00 CNY
    
2019-08-31 * "XX公司" "工资收入"
    Assets:Card:1234           12000.00 CNY
    Expenses:Tax                1000.00 CNY
    Income:Salary       
```


2. 启动页面

> 从CMD进入到main.bean所在目录，执行命令

 ``` shell
 fava main.bean
 ```

浏览器打开 [http://localhost:5000](http://localhost:5000)即可

至此，最简版的beancount+fava搭建完成，按示例添加现有资产、收入、消费记录即可

### 方案二

方案二将各个账本分离开，可以更明了地管理账目

我把方案二的部署代码放到[github](https://github.com/nszaaa3/MyBeancountLedger-Demo)上，克隆下来即可使用。

#### main.bean

```shell
;==main文件==
;【一、账本设置】
option "title" "My Ledger"
option "operating_currency" "CNY"
2022/07/01 custom "fava-option" "language" "zh"

;【二、账户设置】
include "accounts/*.bean"                       ;正则匹配账户文件

;【三、交易记录】
include "ledgers/*/*/*.bean"                    ;正则匹配账单文件-第一个*匹配年份，第二个*匹配用途，第三个*匹配具体.bean

;【四、引入插件】
plugin "beancount.plugins.forecast"             ;beancount官方提供的账单分期插件

```

#### accounts/assets.bean

在这里定义资财类型，包括各银行或在线账户

```shell
;初始财产
;任何Assets在open日期前不可使用
2022/07/01 open Assets:Current:Bank:CN:XXB      ;XX银行
2022/07/01 open Assets:Current:Bank:CN:YYB      ;YY银行
2022/07/01 open Assets:Current:Bank:CN:WX       ;微信
2022/07/01 open Assets:Current:Bank:CN:Alipay   ;支付宝
2022/07/01 open Assets:Deposit:BusCard:CN:LCT   ;郑州绿城通公交卡
2022/07/01 open Assets:Deposit:SIM:CN:Mobile    ;手机卡移动
2022/07/01 open Assets:Deposit:SIM:CN:Unicom    ;手机卡联通
2022/07/01 * "" "YY银行-初始余额"
    Assets:Current:Bank:CN:YYB                  +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "XX银行-初始余额"
    Assets:Current:Bank:CN:XXB                  +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "微信-初始余额"
    Assets:Current:Bank:CN:Alipay               +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "支付宝-初始余额"
    Assets:Current:Bank:CN:Alipay               +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "公交卡-初始余额"
    Assets:Deposit:BusCard:CN:LCT               +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "手机卡联通-初始余额"
    Assets:Deposit:SIM:CN:Unicom                +100.00 CNY
    Equity:Opening-Balances
2022/07/01 * "" "手机卡移动-初始余额"
    Assets:Deposit:SIM:CN:Mobile                +100.0 CNY
    Equity:Opening-Balances

```

#### accounts/equity.bean

equity.bean使用pad和balance来定义某一时刻的财产余额，用于初创账单时的财产或负债导入，但本文没有使用此种方法，转而使用在创建资产账户和负债账户创建完毕后即导入账户，故这里仅创建```Equity:
Opening-Balances```账户

```shell
;权益账户
;任何Equity在open日期前不可使用
2022/07/01 open Equity:Opening-Balances
;初始化资产

```

#### accounts/expenses.bean

在这里定义消费类型，这里仅作示例

```shell
;任何Expenses在open日期前不可使用
;居家
2022/07/01 open Expenses:Home:Phone                     ;手机电话
2022/07/01 open Expenses:Home:Mortgage:Interest         ;房贷利息
2022/07/01 open Expenses:Home:Insurance:ZJX             ;重疾险
2022/07/01 open Expenses:Home:Insurance:SX:MDW          ;寿险
2022/07/01 open Expenses:Home:Insurance:YWX:MDW         ;意外险
2022/07/01 open Expenses:Home:Insurance:Alipay:YLX      ;医疗险
2022/07/01 open Expenses:Home:Insurance:CCX             ;财产险
2022/07/01 open Expenses:Home:Water-Electricity-Gas     ;水电燃气
2022/07/01 open Expenses:Home:WYF                       ;物业费
2022/07/01 open Expenses:Home:Delivery                  ;快递费
2022/07/01 open Expenses:Home:Haircut                   ;理发费
2022/07/01 open Expenses:Home:Omission                  ;漏记款
2022/07/01 open Expenses:Home:Other                     ;其他
;人情
2022/07/01 open Expenses:Relationship:Gift              ;礼物
2022/07/01 open Expenses:Relationship:Relative          ;礼金
2022/07/01 open Expenses:Relationship:PrePayment        ;代付款
2022/07/01 open Expenses:Relationship:FilialPiety       ;孝敬
2022/07/01 open Expenses:Relationship:RedEnvelope       ;红包
2022/07/01 open Expenses:Relationship:Other             ;其他
;购物
2022/07/01 open Expenses:Shopping:Clothing              ;服饰鞋包
2022/07/01 open Expenses:Shopping:Digital               ;电子数码
2022/07/01 open Expenses:Shopping:Home                  ;家居百货
2022/07/01 open Expenses:Shopping:Book                  ;买书
2022/07/01 open Expenses:Shopping:Tea                   ;茶叶
2022/07/01 open Expenses:Shopping:Makeup                ;化妆护肤
2022/07/01 open Expenses:Shopping:Other                 ;其他
;餐饮
2022/07/01 open Expenses:Food:Breakfast                 ;早餐
2022/07/01 open Expenses:Food:Lunch                     ;午餐
2022/07/01 open Expenses:Food:Dinner                    ;晚餐
2022/07/01 open Expenses:Food:Alcohol                   ;酒水
2022/07/01 open Expenses:Food:DrinkFruit                ;饮料水果
2022/07/01 open Expenses:Food:Vegetables                ;买菜原料
2022/07/01 open Expenses:Food:Invite                    ;请客吃饭
2022/07/01 open Expenses:Food:Omission                  ;漏记款
2022/07/01 open Expenses:Food:Other                     ;其他
;医疗健康
2022/07/01 open Expenses:Health:Outpatient              ;门诊
2022/07/01 open Expenses:Health:Medical                 ;药品
2022/07/01 open Expenses:Health:Examination             ;体检
2022/07/01 open Expenses:Health:Other                   ;其他
;娱乐
2022/07/01 open Expenses:Entertainment:Movie            ;电影
2022/07/01 open Expenses:Entertainment:Travel           ;旅游度假
2022/07/01 open Expenses:Entertainment:Hotel            ;酒店住宿
2022/07/01 open Expenses:Entertainment:Media            ;网络流媒体服务
2022/07/01 open Expenses:Entertainment:Show             ;演出门票
2022/07/01 open Expenses:Entertainment:Other
;交通
2022/07/01 open Expenses:Transport:Airline              ;飞机
2022/07/01 open Expenses:Transport:Railway              ;火车
2022/07/01 open Expenses:Transport:Taxi                 ;打车
2022/07/01 open Expenses:Transport:Transit              ;公交地铁
2022/07/01 open Expenses:Transport:Car:Oil              ;加油
2022/07/01 open Expenses:Transport:Car:Tolls            ;过路过桥
2022/07/01 open Expenses:Transport:Car:Maintenance      ;保养维修
2022/07/01 open Expenses:Transport:Car:Insurance        ;车险
2022/07/01 open Expenses:Transport:Car:Parking          ;停车费
2022/07/01 open Expenses:Transport:Car:Wash             ;洗车
2022/07/01 open Expenses:Transport:Bike                 ;共享单车
2022/07/01 open Expenses:Transport:Other                ;其他
;五险一金
2022/07/01 open Expenses:Government:Pension             ;养老保险
2022/07/01 open Expenses:Government:Unemployment        ;失业保险
2022/07/01 open Expenses:Government:Medical             ;医疗保险
2022/07/01 open Expenses:Government:Injury              ;工伤保险
2022/07/01 open Expenses:Government:Maternity           ;生育保险
2022/07/01 open Expenses:Business:Medical               ;商业医疗保险
;个人税
2022/07/01 open Expenses:Government:IncomeTax           ;工资个税
2022/07/01 open Expenses:Government:Customs             ;关税
;投资
2022/07/01 open Expenses:Invest:Dev                     ;技术基础设施费用
2022/07/01 open Expenses:Invest:Study                   ;学习费用
2022/07/01 open Expenses:Invest:Portfolio:Interest      ;利息支出
2022/07/01 open Expenses:Invest:Cost                    ;手续费
2022/07/01 open Expenses:Invest:Other                   ;其他
;虚拟服务
2022/07/01 open Expenses:Virtual:OnePlusCloud           ;一加云
2022/07/01 open Expenses:Virtual:XiaoMiCloud            ;小米云
2022/07/01 open Expenses:Virtual:AppleCloud             ;苹果云
2022/07/01 open Expenses:Virtual:YouKu                  ;优酷VIP
```

#### accounts/income.bean

在这里定义收入类型，这里仅作示例

```shell
;任何Income在open日期前不可使用
;主动收入
2022/07/01 open Income:CN:Salary:MyCompany CNY            ;上班公司收入
;被动收入
;奖金
;赔付款
;影响力：咨询、打赏等
;退款返款
;红包
2022/07/01 open Income:CN:Interest:WX-RedEnvelope CNY     ;微信红包
;投资
2022/07/01 open Income:CN:Interest:Alipay-YEB CNY         ;余额宝
;薅羊毛
;其他
2022/07/01 open Income:CN:Transfer:FromWho CNY            ;他人转账

```

#### accounts/liabilities.bean

在这里定义负债类型，这里仅作示例

```shell 
;任务Liabilities在open日期前不可使用
;贷款
2022/07/01 open Liabilities:Mortgage:CN:YYB CNY             ;YY银行
;信用卡
2022/07/01 open Liabilities:CreditCard:CN:XXB CNY           ;招行信用卡
2022/07/01 open Liabilities:CreditCard:CN:Alipay-Huabei CNY ;花呗

2022/07/01 * "" "初始债务-房贷"
    Liabilities:Mortgage:CN:YYB                             -123456.12 CNY
    Equity:Opening-Balances
2022/07/01 * "" "初始债务-信用卡-XX行"
    Liabilities:CreditCard:CN:XXB                           -123.45 CNY
    Equity:Opening-Balances
2022/07/01 * "" "初始债务-支付宝-花呗"
    Liabilities:CreditCard:CN:Alipay-Huabei                 -1234.56 CNY
    Equity:Opening-Balances
;初始分期债务
2022/07/01 # "初始分期债务-支付宝-花呗 [MONTHLY REPEAT (3-0) TIMES]"   ;周期为月，共3期，已还0期
    Liabilities:CreditCard:CN:Alipay-Huabei      -100.00 CNY       ;每期需还100元
    Equity:Opening-Balances
2022/07/01 # "初始分期债务-支付宝-花呗 [MONTHLY REPEAT (6-1) TIMES]"   ;周期为月，共6期，已还1期
    Liabilities:CreditCard:CN:Alipay-Huabei      -100.00 CNY       ;每期需还100元
    Equity:Opening-Balances
2022/07/01 # "初始分期债务-支付宝-花呗 [MONTHLY REPEAT (12-2) TIMES]"  ;周期为月，共12期，已还2期
    Liabilities:CreditCard:CN:Alipay-Huabei      -100.00 CNY       ;每期需还100元
    Equity:Opening-Balances

```

#### ledgers/2022/income/annual-income-transfer.bean

```shell
;年度薪资及转账收入
2022/07/01 * "" "微信红包"
    Assets:Current:Bank:CN:WX     +2.00 CNY
    Income:CN:Interest:WX-RedEnvelope

```

#### ledgers/2022/income/q3-07-income-interest.bean

```shell
;7月份收入利息明细
2022/07/01 * "利息-支付宝余额宝-昨日"
    Assets:Current:Bank:CN:Alipay                +0.10 CNY
    Income:CN:Interest:Alipay-YEB

```

#### ledgers/2022/liquidity/q3-liquidity.bean


```shell 
;q3账户间流水
2022/07/01 * "" "公交卡充值"
    Assets:Current:Bank:CN:Alipay     -100.00 CNY       ;从支付宝账户中减去100
    Assets:Deposit:BusCard:CN:LCT     +100.00 CNY       ;在绿城通账户中加上100，需保持加减平衡
2022/07/01 * "" "微信充值"
    Assets:Current:Bank:CN:XXB        -0.50 CNY
    Assets:Current:Bank:CN:WX         +0.50 CNY

```

#### ledgers/2022/expenses/q3-07-expenses.bean


```shell
;7月份消费明细
2022/07/01 * "饮食-午餐-手擀面"
    Liabilities:CreditCard:CN:Alipay-Huabei      -10.00 CNY
    Expenses:Food:Lunch
2022/07/02 * "家居百货-纸巾"
    Liabilities:CreditCard:CN:Alipay-Huabei      -14.70 CNY
    Expenses:Shopping:Home
2022/07/03 * "Taxi-旭然园-南上庄"
    Liabilities:CreditCard:CN:Alipay-Huabei      -11.69 CNY
    Expenses:Transport:Taxi
2022/07/03 * "共享单车-南上庄-旭然园"
    Liabilities:CreditCard:CN:Alipay-Huabei      -1.5 CNY
    Expenses:Transport:Bike

```

全部编辑好之后，即可查看账目
> 从CMD进入到main.bean所在目录，执行命令

 ``` shell
 fava main.bean
 ```

浏览器打开 [http://localhost:5000](http://localhost:5000)即可

![示例](/img/2022/june/28/01.png)

小朋友们，你学会了吗？

还是不知道我为啥受穷。

## 多设备间同步账单

1. 拿U盘拷
2. 拿网络传
3. Github
4. 意念传输


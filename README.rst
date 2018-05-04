├── ci    -  持续集成相关
├── conda - 生成conda 包先关
├── docs - 文档
│   ├── notebooks - notebook代码
│   └── source  - 教程和what’s new
├── etc - 依赖配置和一些 hook shell 脚本
├── tests - 测试代码
├── zipline - 代码主目录
│   ├── __init__.py - 集中引入包内容
│   ├── __main__.py - 主程序入口
│   ├── _protocol.pyx - current, history, can_trade 之类的一些数据操作接口的实现
│   ├── _version.py - 版本管理相关的
│   ├── algorithm.py - 策略算法的主逻辑抽象，算法最后会被实例化为TradingAlgorithm的实例或者继承它， 并且里面进行了主要的api的定义，zipine的 cli会调用它的run方法启动回测
│   ├── api.py 常用api
│   ├── api.pyi 常用api说明
│   ├── assets - 资产类抽象 里面封装了常用的资产如股票Equity，期货Funtrue, 作为Asset的子类，并且封装了其数据库操作（这里是sqlite)
│   ├── data - 数据相关，所有的数据操作封装为dataportal 
│   │   ├── __init__.py
│   │   ├── _adjustments.pyx - 除权除息等信息的读取
│   │   ├── _equities.pyx - 从bcolz里面获取行情的索引的抽象
│   │   ├── _minute_bar_internal.pyx - 分钟bar相关的索引
│   │   ├── bar_reader.py - BarReader接口定义
│   │   ├── benchmarks.py - 从雅虎获取基准数据
│   │   ├── bundles - 官方的提供的data bundle 
│   │   ├── continuous_future_reader.py 
│   │   ├── data_portal.py DataPortal定义，整合了所有的reader,writer，等，是biplane获取数据的入口，提供reader,writer数据的简单高层封装
│   │   ├── dispatch_bar_reader.py - 结合trading calendar 读取asset的bar信息
│   │   ├── history_loader.py  - asset 历史信息的获取, 包括附加复权信息
│   │   ├── loader.py - loader 封装了基准信息和国债收益率曲线
│   │   ├── minute_bars.py - 分钟线reader/writer相关的抽象
│   │   ├── resample.py - 把分钟线数据resample为日线数据
│   │   ├── session_bars.py -  SessionBarReader
│   │   ├── treasuries.py - 国债收益率曲线
│   │   ├── treasuries_can.py - 加拿大国债收益率曲线
│   │   └── us_equity_pricing.py - 主要是针对Equity的日线读取，adjustment数据读取，
│   ├── dispatch.py - 分发逻辑
│   ├── errors.py - 异常的抽象
│   ├── examples - 一些例子
│   ├── finance - 主要抽象了交易和财务相关的逻辑，这些接口大多会出现在zipline或者quantopian的代码策略代码里，可以进行import 
│   │   ├── __init__.py 
│   │   ├── asset_restrictions.py - 资产交易限制
│   │   ├── blotter.py - 账号？
│   │   ├── cancel_policy.py - 取消策略
│   │   ├── commission.py - 佣金
│   │   ├── constants.py - 一些常亮定义
│   │   ├── controls.py - 分控相关
│   │   ├── execution.py - 订单类型
│   │   ├── order.py - 订单逻辑
│   │   ├── performance - 收益
│   │   ├── risk - 风险相关
│   │   ├── slippage.py - 滑点
│   │   ├── trading.py - TradingEnvironment， SimulationParameters的抽象，如果使用自己的loader， TradingCalendar 则需要自己初始化这个对象 
│   │   └── transaction.py - Transaction - 交易的抽象
│   ├── gens - 应该是集合了大部分的generator , 主要是回测过程的generator
│   │   └── tradesimulation.py - 回测主要过程的generator
│   ├── lib - 一些主要用的的数据结构和算法
│   ├── pipeline - pipeline相关的逻辑
│   ├── protocol.py - Order Account, Portfolio, Position等的抽象
│   ├── resources - etc相关的一些资源
│   ├── sources - 基准数据源等
│   ├── test_algorithms.py - 测试策略...
│   ├── testing - 测试
│   ├── utils - 一些工具类， 其中 run_algo.py, tradingcalendar.py 相关的需要重点关注下



Zipline－一个正在成长的项目
=======
Zipline:当前在线的量化平台基本都是基于zipline进行开发，使用这些平台，首先，自己的
策略会泄密，其次，这些平台速度慢，而且不够灵活。
然而，A股并不能直接使用zipline，需要对数据,基准，交易日期，手续费等部分做修改。
本项目修改zipline平台，以使得其能适用于A股市场。


项目文档：

https://github.com/zhanghan1990/zipline/wiki

安装方法
========
运行环境：linux, OSX，建议不要使用windows,因为zipline涉及到gcc的编译，windows可能编译过程中有一些问题。

（1）windows 用户可以下载virtual box，在这个镜像中，集成了数据，和开发环境，以及ipython notebook,virtual box 虚拟机地址： https://pan.baidu.com/s/1bp5roxL

虚拟机密码为:zipline

具体使用方法：打开virtual box ,输入以下命令

- service mongodb start

- source zipline/zip_env/bin/activate

- sudo jupyter notebook

- ifconfig

得到虚拟机的IP 地址，例如IP为：192.168.1.120，则在windows浏览器输入 192.168.1.120:8888


(2)对于本地安装，以ubuntu 为例：

- git clone https://github.com/zhanghan1990/zipline
- cd zipline
- sudo apt-get install python-pip
- sudo apt-get install mongodb
- sudo pip install virtualenv
- sudo apt-get install python-tk
- virtualenv zipline_env
- source zipline_env/bin/activate
- pip install -r requirements.txt
- python setup.py install
- sudo service mongodb start
- pip install xlrd
- pip3 install jupyter




version_1.0 完成的主要工作
=======================

- 交易日历纠正，从1990年开始的所有有效交易日都包含其中，剔除非交易时段
- A股数据源，把数据写入mongodb中，每次从mongodb中读取需要的数据
- benchmark，使用A股的几个标准（HS300指数等）
- return 计算，计算alpha和beta当前使用中国国债作为基准
- 手续费模型设定


关于数据
========

- 您可以使用自己的数据，也可以使用我配置的数据源，数据源我已经配置好，如果自己配置，需要修改文件 data/constants.py 下的IP和PORT
- 我的机器的IP为166.111.68.233 PORT为27017
- 本版本的数据源,只更新到2017.02.28,后面我会每天更新数据

本地数据导入
===========
- 交易数据地址：
http://pan.baidu.com/s/1i4GZWFF

- 关于数据导入：
脚本 https://github.com/zhanghan1990/zipline/blob/master/zipline/data/mongodb.py 提供数据导入，修改line 29为您数据解压缩位置
然后执行脚本python mongodb.py


关于例子
========

-在examples下面有3个例子，这3个例子可以满足基本的回测需求，这三个例子我和joinquant做了比对，差距很小（ps，完全一样还是很难，手续费那里有问题，我会继续修改)

联系方式
========

欢迎感兴趣的朋友加入到这个项目来，有问题请给我发邮件：
zganghanhan@foxmail.com

加入我们
=======
欢迎有兴趣的朋友伙伴加入我们的开源讨论群：


QQ群：556125593

# XMan 量化交易系统使用说明  {#mainpage}

---
## 系统介绍
xman高频量化交易系统，由上海量赢科技有限公司(www.quantin.cn)自主开发，采用基于实时交易领域的全内存交易方案，支持宽睿OES和华鑫CTP,策略编写灵活。
目前提供开源半路板、盘前抢板和盘中抢板等功能。

## 环境安装
### 操作系统
需要centos 7.2及以上版本;

### gcc 9.2.0
wget https://down.24kplus.com/linux/gcc/gcc-install.sh
chmod +x gcc-install.sh && ./gcc-install.sh

### 编译
cd build && makefile.sh

### 文档生成
安装doxygen、graphviz生成对应的帮助文档

## 系统架构

### 目录结构

|目录|文件|说明|
|:--:|:--:|:--:|
|bin |    								|    		 |
|    |xman								|框架服务    |
|    |xbasket           				|盘中抢板程序    |
|    |xload								|策略加载服务|
|    |xpurch            				|新股新债申购|
|    |xwebserver                        |对外服务程序,提供基于websocket的协议接口|
|    |xauction                          |集合竞价抢单程序|
|conf|    								|    		 |
|    |system.conf       				|进程绑核及行情订阅配置|
|    |user.conf         				|用户配置|
|    |mds_client.conf   				|行情配置|
|    |oes_client.conf   				|交易配置|
|    |sdb.conf          				|内存配置|
|data|    								|    		|
|    |plot.conf         				|策略管理|
|    |submkt.csv        				|订阅行情文件,只在system.conf配置项oesmkt.resub为1时生效|
|sbin|    								|    |
|    |xhisday           				|历史行情处理|
|    |xhorder           				|手工报单|
|    |xbtest         					|策略回测|
|logs|    								|    |


## 进程说明
### XMan(框架服务)

	###############################################################
	#    量赢策略交易平台 (2.0.0)  
	# -a [all] 初始化并启动程序
	#                                                             
	# -i[init] 初始化数据及建立内存
	# -m[market] 启动行情接受,需要先init
	# -t[trade] 启动交易处理,需要先init
	# -o[xorden] 启动交易处理,需要先init
	# -r[xrebld] 重建丁单薄,需要先init
	# -n[xrsnap] 生成重建后快照,需要先init
	# -y[xstore] 存储重构好的快照,需要先init
	# -x[xetick] 存储逐笔数据,需要先init
	#                                                             
	# -e[export] 导出行情
	# -p[print] 打印当前系统状态
	#                                                             
	# -s[stop] 停止程序并清理内存
	###############################################################

### xload(策略加载服务)

	###############################################################
	#    XMan策略交易平台(2.0.0) 
	# -h [help] 帮助
	# 默认不带参数,加载data\plot.conf策略
	# -t [type]操作类型,-1:默认,0:帮助,1:按证券代码+本地单号修改测量,2:按策略编号修改,3:按证券代码修改所有策略状态,4:按本地单号修改策略状态
	# -c [customer] 客户号
	# -m [market] 市场,1:上海,2:深圳
	# -s [security] 证券代码
	# -b [bstype] 买卖方向,1:买入,2:卖出
	# -l [localid] 策略请求编号,data\plot.conf中的块内容
	# -r [run] 修改的策略状态,1:启动,其它停止
	# -p [plotid] 策略编号

	# -u [localids]篮子标号,data\plot.conf块内容
	# -d [cdl]撤单率，万分比
	# 修改策略【按证券代码(需要输入-c -m -s -b -l -r)|按策略编号(-c -p -r)】
	# 如：
	# 1. 按证券代码启动策略
	# ./xload -t1 -ccustomer -m1 -s600837 -b1 -l3 -r1
	# 2. 按策略编号停止策略
	# ./xload -t2 -ccustomer -p30000487 -r0
	# 3. 按证券代码启动策略
	# ./xload -t3 -m1 -s600837 -r1
	# 4. 按本地单号停止策略状态
	# ./xload -t4 -l3 -r0
	# 5. 按篮子修改正确代码撤单率
	# ./xload -t5 -ccustomer -m1 -s600837 -b1 -u1,2 -d12
	###############################################################

### xhisday(历史行情处理)

	###############################################################
	#    量赢策略交易平台(2.0.0) 
	# -h [help] 帮助
	# -t [2:tsnap2csv(转重构后的快照),3:trade2csv(转交易),4:tick2csv(转逐笔和快照)]
	# -m [market] 市场
	# -s [securityid] 证券代码
	# -c [channel] 频道
	###############################################################

### xhorder(手工报单)

	###############################################################
	#     手动交易 1.0.0 
	# -t [业务模式 0:交易,1:查询行情,2:查资金,3:查持仓
	# -c [客户号]
	# -m [市场 1:上海,2:深圳(默认)] 
	# -s [证券代码 6位] 
	# -b [买卖 1: 买入,2:卖出(默认),3:逆回购卖出, 4:撤单]
	# -p [价格为实际价格*10000的整数]
	# -q [买卖数量]
	# -l [撤单编号]
	###############################################################

### xbtest(策略回测)

	###############################################################
	#    XMan策略交易平台(2.0.0) 
	# -h [help] 帮助
	# -t [1:backtest(回测平台启动),2:运行(设置策略后启动运行)
	# -i [staticfile] 静态文件二进制,回测时指定静态文件
	# -q [mktstorefile] 行情二进制文件，回测时指定
	# -m [market] 市场
	# -s [securityid] 证券代码
	# -c [channel] 频道号
	# -v [volume] 回放多少条暂停对应的时间
	# -k [time] 暂停的时间ms
	###############################################################

## 策略文件说明
### 篮子全局参数
|字段|中文|说明|
|-|-|-|
plotType|策略类型|Basket:抢板程序,BondRob:可转债盘前和收盘抢板|
isForbidTrade|是否禁止|0：允许，1禁止|
customer|客户号| |
allowHoldQty|允许最大持有数量| |
slipBuyTimes|买滑点次数|默认3 |
slipSellTimes|卖滑点次数|默认5 |
ordGapTime|委托间隔| |
ctrGapTime|撤单间隔| |
isCtrlStop|撤单后是否停止|0:不停止,其他停止|
isAutoCtrl|是否允许自动撤单|1:自动撤单,0:不自动撤单 |
isOpenStop|涨停打开后是否继续下单|0:涨停停止,1:涨停不停止 |
beginTime|开始委托时间|HHMMSSsss|
endTime|结束委托时间|HHMMSSsss|

#### 抢板买(xbasket)

| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|1：买| |
|4|conPx|信号价格|-1：涨停价,其它实际价格 * 10000| |
|5|ordPx|买卖价格|-1：涨停价,其它实际价格 * 10000| |
|6|qtyType|委托数量类型|0:数量，1：资金,卖出固定为0| |
|7|money,qty|委托金额or数量|数量或金额| |
|8|cdl|买一撤单率|0:不控制，其他万分比| |
|9|askQty|卖一未成交量|-1:不控制，其他未成交数量| |
|10|buyMoney|买一封单金额| |买一封单金额，万元|
|11|buyCtrlMoney|买一封单不足撤单|-1:不控制，其他万元| |
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志|买入时0：抢板| |
|14|kpzdf|开盘涨幅|小于-2000不控制| |
|15|upperQtyMulty| | | |
|16|upperQtyMultyMin| | | |
|17|nxtCtrlMoney|封板后1分钟成交量撤单|0,生效|万元|
|18|followCtrlMoney|封板后连续2分钟成交量撤单|0,不生效|万元|


#### 半路买(xbasket)
| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|1：买| |
|4|conPx|最低涨幅| |万分比|
|5|ordPx|最高涨幅| |万分比|
|6|qtyType|委托数量类型|0:数量，1：资金,卖出固定为0| |
|7|money,qty|委托金额or数量|数量或金额| |
|8|cdl|涨速| |万分比,实际涨速大于该值时通过|
|9|askQty|主买与流通股比例| |主买与流通股比例,万分比|
|10|buyMoney|1分钟成交金额| |万元|
|11|buyCtrlMoney|3分成成交金额| |万元|
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志|2半路| |
|14|kpzdf| | | |
|15|upperQtyMulty|涨停卖与昨日倍量| |万分比|
|16|upperQtyMultyMin|涨停卖与前5分钟倍量| |万分比|
|17|nxtCtrlMoney|压单/托单比例| |万分比|
|18|followCtrlMoney|压单笔数 | |压单笔数>设置值,且压单/托单比例 >设置值允许买入 |

#### 正常卖(xbasket)
| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|2：卖| |
|4|conPx|时间批次1| |HHMMSSsss|
|5|ordPx|时间批次2| |HHMMSSsss|
|6|qtyType|委托数量类型|0:数量| |
|7|qty|数量| | |
|8|cdl|最低涨速| |万分比,涨速>最低涨速 <最高涨速,且1分钟成交金额小于多少时触发|
|9|askQty| | | |
|10|buyMoney|1分钟成交金额| |最近1分钟成交金额,万元,设置为0时涨速同时无意义|
|11|buyCtrlMoney|最高涨速| |万分比，最高涨速要>=最低涨速|
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志|0:正常卖出| |
|14|kpzdf|开盘涨幅|小于-2000不控制，该值设置要小于200|万分比,默认-2500,开盘涨跌幅小于设置且在设置的时间批次1和时间批次2行情往下卖出| 
|15|upperQtyMulty|拉高涨幅|为0,拉高回落不触发|万分比|
|16|upperQtyMultyMin|回落涨幅| |万分比|
|17|nxtCtrlMoney|托单/压单比例| |万分比|
|18|followCtrlMoney|托单笔数 | | 托单笔数>设置值,且托单/压单比例 > 设置值允许卖出|

2023.11.04 启动开张涨幅、拉高涨幅、回落涨幅字段；

#### 盈利卖出(xbasket)
| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|2：卖| |
|4|conPx|时间批次1| |HHMMSSsss|
|5|ordPx|时间批次2| |HHMMSSsss|
|6|qtyType|委托数量类型|0:数量| |
|7|qty|数量| | |
|8|cdl|最低涨速| |万分比|
|9|askQty| | | |
|10|buyMoney|1分钟成交金额| |当前成交金额或最近2分钟成交金额,万元,设置为0时涨速同时无意义|
|11|buyCtrlMoney| | | |
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志|6:盈利卖出| |
|14|kpzdf| | | |
|15|upperQtyMulty| | | |
|16|upperQtyMultyMin| | | |
|17|nxtCtrlMoney| | | |
|18|followCtrlMoney| | | |

### 篮子买卖
| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|1:买,2：卖| |
|4|conPx|信号价格|0自动转为涨停价|转债开盘为1300000,转债收盘1573000|
|5|ordPx|实际价格|0自动转为涨停价|转债开盘为1300000,转债收盘1573000|
|6|qtyType|委托数量类型|0:数量| |
|7|qty|数量| | |
|8|cdl| | | |
|9|askQty| | | |
|10|buyMoney| | | |
|11|buyCtrlMoney| | | |
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志| | |
|14|kpzdf| | | |
|15|upperQtyMulty| | | |
|16|upperQtyMultyMin| | | |
|17|nxtCtrlMoney| | | |
|18|followCtrlMoney| | | |


### 集合竞价抢板(xauction)

| 序号 | 字段 | 中文 | 取值 | 说明 |
|---|---|---|---|---|
|1|market|市场|1：上海，2：深圳| |
|2|securityId|证券代码| |证券代码|
|3|bsType|买卖方向|1:买,2：卖| |
|4|conPx|信号价格|0自动转为涨停价|转债开盘为1300000,转债收盘1573000|
|5|ordPx|实际价格|0自动转为涨停价|转债开盘为1300000,转债收盘1573000|
|6|qtyType|委托数量类型|0:数量,1:金额| |
|7|qty or money|数量| | |
|8|cdl|封单比例| |万分比,集合竞价买二量/集合竞价买一量|
|9|askQty|封单不足数量| |对应封单数量不足,(尾盘集合竞价时填-1)|
|10|buyMoney| | | |
|11|buyCtrlMoney| | | |
|12|isNoneTrade|是否允许交易|0：允许，1：不允许| |
|13|sign|标志|0:开盘集合竞价抢单,1:收盘集合竞价抢单| |
|14|kpzdf| | | |
|15|upperQtyMulty| | | |
|16|upperQtyMultyMin| | | |
|17|nxtCtrlMoney| | | |
|18|followCtrlMoney| | | |

## 导出文件说明
[导出文件说明](md_export.html)

## 交流QQ群： 12277157

## 公众号： 量赢科技
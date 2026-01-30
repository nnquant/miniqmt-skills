# XtQuant API 完整文档

本文档包含 XtQuant 的完整 API 说明、代码示例和常见问题解答。

**文档来源**：迅投知识库官方文档
**页面数量**：5个页面
**主要内容**：行情模块、交易模块、完整示例、常见问题

---

## 目录

1. [XtData 行情模块](#xtdata-行情模块)
2. [XtTrader 交易模块](#xttrader-交易模块)
3. [完整代码示例](#完整代码示例)
4. [常见问题解答](#常见问题解答)

---

## XtData 行情模块

### 模块概述

**官方文档**：https://dict.thinktrader.net/nativeApi/xtdata.html

XtData 作为行情模块，提供精简直接的数据接口，满足量化交易者的数据需求。

**主要功能**：
- 历史和实时的 K 线数据
- 分笔成交数据
- 财务数据
- 合约基础信息
- 板块和行业分类信息

### 使用前须知

**重要提示**：

1. **连接机制**：xtdata 提供和 MiniQMT 的交互接口，本质是和 MiniQMT 建立连接，由 MiniQMT 处理行情数据请求，再把结果回传到 Python 层。使用的行情服务器以及能获取到的行情数据和 MiniQMT 是一致的。

2. **数据获取接口**：使用时需要先确保 MiniQMT 已有所需要的数据，如果不足可以通过补充数据接口补充，再调用数据获取接口获取。

3. **订阅接口**：直接设置数据回调，数据到来时会由回调返回。订阅接收到的数据一般会保存下来，同种数据不需要再单独补充。

### 基础示例：获取历史行情

```python
from xtquant import xtdata
import time

# 设定标的列表和周期
code_list = ["000001.SZ"]
period = "1d"

# 下载历史行情数据到本地
# 注意：xtquant 的大部分历史数据以压缩形式存储在本地
for code in code_list:
    xtdata.download_history_data(code, period=period, incrementally=True)

# 下载财务和板块数据
xtdata.download_financial_data(code_list)
xtdata.download_sector_data()

# 读取本地历史行情数据
history_data = xtdata.get_market_data_ex([], code_list, period=period, count=-1)
print(history_data)
```

### 实时行情订阅（轮询模式）

```python
from xtquant import xtdata
import time

code_list = ["000001.SZ"]
period = "1d"

# 向服务器订阅数据
for code in code_list:
    xtdata.subscribe_quote(code, period=period, count=-1)

# 等待订阅完成
time.sleep(1)

# 获取订阅后的行情（自动拼接历史+实时数据）
kline_data = xtdata.get_market_data_ex([], code_list, period=period)
print(kline_data)

# 以固定间隔刷新行情（循环10次）
for i in range(10):
    kline_data = xtdata.get_market_data_ex([], code_list, period=period)
    print(kline_data)
    time.sleep(3)  # 每3秒刷新一次
```

### 实时行情订阅（回调模式）

```python
from xtquant import xtdata

code_list = ["000001.SZ"]
period = "1d"

# 定义回调函数
def on_data_callback(data):
    """
    当订阅的标的tick发生变化时，此函数会被异步调用
    注意：本地已有的数据不会触发callback
    """
    code_list = list(data.keys())
    kline = xtdata.get_market_data_ex([], code_list, period=period)
    print(kline)

# 订阅时设定回调函数
for code in code_list:
    xtdata.subscribe_quote(code, period=period, count=-1, callback=on_data_callback)

# 使用回调时，必须调用 run() 阻塞程序
xtdata.run()
```

**回调模式说明**：
- 回调函数会异步执行，每当订阅的标的数据更新时自动调用
- 必须使用 `xtdata.run()` 阻塞程序，否则程序会直接退出
- 适合需要实时响应行情变化的场景

### VIP服务器连接

**官方文档**：https://dict.thinktrader.net/nativeApi/code_examples.html

VIP服务器提供更快的行情推送和更多的数据服务。

```python
from xtquant import xtdatacenter as xtdc
from xtquant import xtdata

# 设置token（从投研用户中心获取：https://xuntou.net/#/userInfo）
xtdc.set_token('这里输入你的token')

# 设置VIP服务器连接池
addr_list = [
    '115.231.218.73:55310',
    '115.231.218.79:55310',
    '42.228.16.211:55300',
    '42.228.16.210:55300',
    '36.99.48.20:55300',
    '36.99.48.21:55300'
]
xtdc.set_allow_optmize_address(addr_list)

# 开启K线全推功能（VIP功能）
xtdc.set_kline_mirror_enabled(True)

# 初始化并监听端口
xtdc.init()
port = xtdc.listen(port=58621)  # 指定固定端口
xtdata.connect(port=port)

print('连接成功')
print(f'数据目录: {xtdata.data_dir}')

# 查看服务器状态
servers = xtdata.get_quote_server_status()
for k, v in servers.items():
    print(k, v)

xtdata.run()
```

### 自定义端口连接

当默认端口58609被占用时，可以指定自定义端口：

```python
from xtquant import xtdatacenter as xtdc

xtdc.set_token("你的token")
xtdc.init(False)  # 不自动初始化
port = 58601  # 自定义端口
xtdc.listen(port=port)
print(f"服务启动，开放端口：{port}")
```

---

## XtTrader 交易模块

### 模块概述

**官方文档**：https://dict.thinktrader.net/nativeApi/xttrader.html

XtTrader 封装了策略交易所需要的 Python API 接口，可以和 MiniQMT 客户端交互进行交易操作。

**主要功能**：
- 报单、撤单操作
- 查询资产、委托、成交、持仓
- 接收资金、委托、成交和持仓等变动的主推消息
- 支持回调机制处理交易事件

### 快速入门示例

```python
from xtquant.xttrader import XtQuantTrader, XtQuantTraderCallback
from xtquant.xttype import StockAccount
from xtquant import xtconstant

# 定义交易回调类
class MyXtQuantTraderCallback(XtQuantTraderCallback):
    def on_disconnected(self):
        """连接断开"""
        print("连接断开")

    def on_stock_order(self, order):
        """委托回报推送"""
        print("委托回报:", order.stock_code, order.order_status, order.order_sysid)

    def on_stock_trade(self, trade):
        """成交变动推送"""
        print("成交回报:", trade.account_id, trade.stock_code, trade.order_id)

    def on_order_error(self, order_error):
        """委托失败推送"""
        print("委托失败:", order_error.order_id, order_error.error_id, order_error.error_msg)

    def on_cancel_error(self, cancel_error):
        """撤单失败推送"""
        print("撤单失败:", cancel_error.order_id, cancel_error.error_id, cancel_error.error_msg)

# 初始化交易客户端
path = 'D:\\迅投极速交易终端\\userdata_mini'  # MiniQMT 安装路径
session_id = 123456  # 会话编号，不同策略使用不同编号

xt_trader = XtQuantTrader(path, session_id)

# 创建账号对象
acc = StockAccount('1000000365')  # 替换为实际账号

# 注册回调
callback = MyXtQuantTraderCallback()
xt_trader.register_callback(callback)

# 启动交易线程并连接
xt_trader.start()
connect_result = xt_trader.connect()  # 返回0表示成功
subscribe_result = xt_trader.subscribe(acc)  # 订阅交易推送

print(f"连接结果: {connect_result}, 订阅结果: {subscribe_result}")
```

### 下单和撤单操作

```python
# 接上面的初始化代码...

stock_code = '600000.SH'

# 1. 限价单买入
order_id = xt_trader.order_stock(
    acc,
    stock_code,
    xtconstant.STOCK_BUY,      # 买入
    200,                        # 数量
    xtconstant.FIX_PRICE,      # 限价单
    10.5                        # 价格
)
print(f"限价单下单成功，订单号: {order_id}")

# 2. 市价单买入
order_id_market = xt_trader.order_stock(
    acc,
    stock_code,
    xtconstant.STOCK_BUY,
    200,
    xtconstant.MARKET_PRICE,   # 市价单
    0                           # 市价单价格填0
)
print(f"市价单下单成功，订单号: {order_id_market}")

# 3. 撤单
cancel_result = xt_trader.cancel_order_stock(acc, order_id)
print(f"撤单结果: {cancel_result}")

# 4. 查询委托
orders = xt_trader.query_stock_orders(acc)
print(f"当前委托: {orders}")

# 5. 查询持仓
positions = xt_trader.query_stock_positions(acc)
print(f"当前持仓: {positions}")

# 6. 查询资产
asset = xt_trader.query_stock_asset(acc)
print(f"账户资产: {asset}")
```

### 交易数据结构

**委托类型 (order_type)**：
- `xtconstant.FIX_PRICE` - 限价单
- `xtconstant.MARKET_PRICE` - 市价单

**交易操作**：
- `xtconstant.STOCK_BUY` - 买入
- `xtconstant.STOCK_SELL` - 卖出

**账号类型 (account_type)**：
- `'STOCK'` - 普通账户
- `'HUGANGTONG'` - 沪港通
- `'SHENGANGTONG'` - 深港通

---

## 完整代码示例

### 示例1：行情订阅与策略判断

**场景说明**：订阅沪深市场行情，判断涨幅超过9%的股票。

**风险提示**：本策略仅供参考学习，实盘交易请充分测试，交易有风险。

```python
from xtquant import xtdata

# 行情回调函数
def on_market_data(data):
    """处理实时行情数据"""
    for code, quote in data.items():
        # 计算涨幅
        if 'close' in quote and 'preclose' in quote:
            change_pct = (quote['close'] - quote['preclose']) / quote['preclose'] * 100
            if change_pct > 9.0:
                print(f"{code} 涨幅 {change_pct:.2f}%，触发信号")

# 订阅股票行情
stocks = ["600000.SH", "000001.SZ"]
for code in stocks:
    xtdata.subscribe_quote(code, period="1d", count=-1, callback=on_market_data)

xtdata.run()
```

---

## 常见问题解答

**官方文档**：https://dict.thinktrader.net/nativeApi/question_function.html

### 问题1：导入 xtquant 库失败

**错误信息**：`NO module named 'xtquant.IPythonAPiClient'`

**解决方案**：
1. 确认使用的是 **64位 Python 3.6-3.12** 版本
2. 检查 xtquant 库是否正确安装
3. 如果出现 `PermissionError`，说明存在文件权限问题

### 问题2：连接失败返回 -1

**原因**：同一个 session 的两次 Python 进程 connect 之间必须超过 3 秒

**解决方案**：
- 等待至少 3 秒后再次连接
- 或者使用不同的 session_id

### 问题3：端口 58609 被占用

**错误信息**：执行 `xtdatacenter.init()` 时提示监听 58609 端口失败

**解决方案**：

**方法1：指定自定义端口**
```python
from xtquant import xtdatacenter as xtdc
xtdc.init(False)
port = 58601
xtdc.listen(port=port)
```

**方法2：关闭冲突程序或重启电脑**

### 问题4：委托备注字段被截断

**问题描述**：查询委托的投资备注（order_remark）只有前半部分

**原因**：极简客户端的 `order_remark` 字段有长度限制
- 最大 24 个英文字符
- 一个中文字符占 3 个字符
- 超出部分会被丢弃

**解决方案**：
- 控制备注长度在限制范围内
- 或使用大 QMT（无长度限制）

### 问题5：userdata_mini 目录下生成大量 down_queue 文件

**原因**：xttrade 指定新的 session 时会产生该文件

**解决方案**：控制 session id 范围，避免文件大量产生

```python
# 指定 session id 范围连接交易
path = 'D:\\迅投极速交易终端\\userdata_mini'
session_id = 100  # 使用固定的 session_id，避免每次都创建新的
xt_trader = XtQuantTrader(path, session_id)
```

**注意**：down_queue 文件可以安全删除。

---

## 文档说明

本文档整理自迅投知识库官方文档，包含以下内容：

- **XtData 行情模块**：数据获取、实时订阅、VIP服务器连接
- **XtTrader 交易模块**：交易初始化、下单撤单、回调处理
- **完整代码示例**：实际应用场景的完整代码
- **常见问题解答**：开发中常见问题及解决方案

**相关资源**：
- 官方网站：http://www.thinktrader.net
- 投研用户中心：https://xuntou.net/#/userInfo
- 迅投学院：https://www.xuntou.net/plugin.php?id=keke_video_base

**更新时间**：2026年1月

---

*本文档由 Skill Seekers 自动生成并整理，内容来源于迅投知识库官方文档。*

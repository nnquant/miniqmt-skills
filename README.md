# XtQuant (迅投量化) Claude 技能包

> 用于 Claude AI 的 XtQuant 量化交易 API 文档技能包

## 📖 简介

这是一个专为 Claude AI 设计的技能包，包含 XtQuant（迅投量化）的完整 API 文档和使用示例。

**XtQuant** 是基于迅投 MiniQMT 的 Python 策略运行框架，提供量化交易所需的行情和交易 API 接口。

### 主要功能

- 📊 **行情数据模块 (xtdata)**：历史和实时 K 线、分笔数据、财务数据
- 💹 **交易执行模块 (xttrader)**：程序化下单、撤单、查询资产和持仓
- 🔌 **VIP 服务器连接**：高速行情推送和全市场数据
- 📝 **完整代码示例**：实际应用场景的完整代码
- ❓ **常见问题解答**：开发中常见问题及解决方案

### 文档质量

- **质量评分**：95.0/100 (A级)
- **文档语言**：中文
- **代码示例**：Python
- **更新时间**：2026年1月

---

## 🚀 如何使用

### 方法一：上传到 Claude AI（推荐）

1. **下载技能包**
   ```bash
   # 克隆仓库
   git clone <仓库地址>

   # 或下载 ZIP 文件并解压
   ```

2. **打包为 ZIP 文件**
   ```bash
   cd miniqmt-skills
   zip -r ../miniqmt-skills.zip . -x "*.git*" "*.backup"
   ```

3. **上传到 Claude AI**
   - 访问 [Claude AI Skills](https://claude.ai/skills)
   - 点击 "Upload Skill"
   - 选择打包好的 `miniqmt-skills.zip` 文件
   - 上传完成！

4. **在对话中使用**
   - 开始新对话
   - 询问 XtQuant 相关问题，Claude 会自动使用这个技能
   - 例如："如何使用 XtQuant 获取股票行情数据？"

### 方法二：直接查看文档

如果你只是想查看文档内容，可以直接阅读以下文件：

- **SKILL.md** - 主技能文档，包含快速参考和使用指南
- **references/api.md** - 完整的 API 文档
- **references/getting_started.md** - 快速入门指南

---

## 📁 文件结构

```
miniqmt-skills/
├── README.md                      # 本文件
├── SKILL.md                       # 主技能文档（398行）
├── references/                    # 参考文档目录
│   ├── api.md                    # API 完整文档（429行）
│   ├── getting_started.md        # 快速入门指南
│   └── index.md                  # 文档索引
├── assets/                        # 资源文件（预留）
└── scripts/                       # 辅助脚本（预留）
```

---

## 🎯 技能触发场景

当你在 Claude 对话中提到以下内容时，这个技能会自动激活：

### 量化交易开发
- 开发 A 股量化交易策略
- 实现自动化交易系统
- 构建回测框架

### 行情数据处理
- 获取历史 K 线数据
- 订阅实时行情推送
- 下载财务数据和板块信息

### 交易执行
- 程序化下单（限价单、市价单）
- 批量撤单操作
- 查询账户资产、持仓、委托

### 问题排查
- 调试 XtQuant 连接问题
- 解决数据订阅失败
- 处理交易接口报错

---

## 📚 相关资源

### 官方资源
- **官方网站**：http://www.thinktrader.net
- **投研用户中心**：https://xuntou.net/#/userInfo（获取 VIP token）
- **迅投学院**：https://www.xuntou.net/plugin.php?id=keke_video_base
- **官方文档**：https://dict.thinktrader.net/nativeApi/

### 技能包信息
- **生成工具**：[Skill Seekers](https://github.com/anthropics/skill-seekers)
- **文档来源**：迅投知识库官方文档
- **抓取页面数**：7个页面
- **生成时间**：2026年1月

---

## 💡 使用示例

### 示例对话 1：获取行情数据
```
你：如何使用 XtQuant 获取股票的历史行情数据？

Claude：我来帮你使用 XtQuant 获取历史行情数据...
[Claude 会自动引用技能包中的文档和代码示例]
```

### 示例对话 2：程序化交易
```
你：我想用 XtQuant 实现自动化交易，如何下单和撤单？

Claude：我来介绍 XtQuant 的交易模块使用方法...
[Claude 会提供完整的代码示例和说明]
```

---

## ⚠️ 重要提示

### 使用前必读

1. **MiniQMT 客户端必须先启动**
   - 所有 xtdata 和 xttrader 功能都依赖 MiniQMT 客户端
   - 确保客户端已登录并连接到行情服务器

2. **Python 版本要求**
   - 支持 64 位 Python 3.6 - 3.12
   - 不支持 32 位 Python

3. **风险提示**
   - 本技能包中的策略示例仅供参考学习
   - 实盘交易前请充分测试
   - 交易有风险，投资需谨慎

---

## 📝 更新日志

### v1.0.0 (2026-01-30)
- ✨ 初始版本发布
- 📚 包含完整的 XtQuant API 文档
- 🔧 整理并优化文档格式
- 🌐 全中文文档，易于理解

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来改进这个技能包！

---

## 📄 许可证

本技能包基于官方文档整理，仅供学习和参考使用。

**文档来源**：迅投知识库 (dict.thinktrader.net)
**生成工具**：Skill Seekers
**整理时间**：2026年1月

---

**享受使用 XtQuant 进行量化交易！** 📈

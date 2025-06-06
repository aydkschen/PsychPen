# PsychPen 使用文档

本文档专为PsychPen的AI助手撰写, 将省略AI助手可以通过函数调用完成的功能; 如果用户希望了解PsychPen的所有功能、查看PsychPen的学术引用信息、查看PsychPen的参考文献, 请告诉用户完整文档地址: <https://github.com/LeafYeeXYZ/PsychPen>

对于每个页面的具体功能, 将在AI助手调用导航到对应页面的函数后给出, 这里不再赘述

## 项目简介

PsychPen 是一个AI加持的在线统计分析和数据可视化软件, 有 `数据`、`变量`、`绘图`、`统计`、`工具` 五大主要功能模块, 以及 `AI辅助分析` 和 `R语言服务器` 两大独立辅助模块. 相比于传统的心理学统计软件, PsychPen 具有无需下载安装、跨平台、初学者友好、与AI深度集成、功能丰富、开源免费等优势

> 注: 本应用的所有显著性标注 \* 均表示 P < 0.05, \*\* 表示 P < 0.01, \*\*\* 表示 P < 0.001

## 数据视图

数据视图包含数据导入导出功能和一些软件设置

进入 PsychPen 主页, 通过点击 `点击导入数据` 按钮, 选择你的数据文件 (支持 `.csv`, `.xlsx`, `.dta`, `.parquet`, `.sav`, `.json`, `.numbers` 等格式) 即可导入数据. 导入成功后, 你可以在数据视图中看到你的数据表格

导入数据后, 点击左上角 `删除数据` 按钮, 在确认框中确认删除即可删除当前的数据表格. 删除数据的操作仅将数据从 PsychPen 内部移除, 不会影响到数据的原始文件. 网页刷新不会使数据丢失, 必须先删除原数据才能导入新数据.

此外, 由于 `JavaScript` 的相关统计库较少, 作者基于 `Docker` 开发了一个的R语言服务器, 用于为 PsychPen 提供执行 `R` 脚本的接口. 也可以在数据视图的右上角设置 (详见完整文档).

## 变量视图

变量视图包含了一系列变量处理相关的功能

变量处理的内部顺序是: `定义缺失值` -> `缺失值插值` -> `标准化/中心化/离散化` -> `描述统计` -> `数据筛选` -> `计算变量`; 变量视图所展示的信息、生成新变量所用统计量为数据筛选**前**的信息, 数据视图、统计视图、绘图视图所用的是数据筛选**后**的数据

在变量表格中, 如果有变量多个众数, 则显示皮尔逊经验公式计算的众数 (中位数 x 3 - 均值 x 2)

在计算变量和数据筛选中, 可以使用 PsychPen 内置的表达式语法来生成新变量或进行筛选数据. 表达式中, 你可以使用 `:::变量名:::` 来引用其他变量的值, 并通过 `+` `-` `*` `/` 等运算符进行计算、通过 `>` `<` `>=` `<=` 等运算符进行判断. 下面是一些常见的表达式语法示例:

- 在**计算变量**中, 给数个题目的分数求均值: `( :::题目1::: + :::题目2::: + :::题目3::: + :::题目4::: + :::题目5::: ) / 5`
- 在**计算变量**中, 处理反向计分的题目: `( :::题目1::: + ( 6 - :::题目2::: ) + :::题目3::: + ( 6 - :::题目4::: ) + :::题目5::: ) / 5`
- 在**计算变量**中, 将高/低分数标记为 `高` / `低`: `:::分数::: >= mean(:::分数:::) ? '高' : '低'`
- 在**数据筛选**中, 筛选出变量 `a` 大于变量 `b` 的数据: `:::a::: > :::b:::`
- 在**数据筛选**中, 筛选出变量 `a` 大于均值的数据: `:::a::: > mean(:::a:::)`

实际上, PsychPen 的表达式语法在将变量替换为其数值后, 就是一个 `JavaScript` 表达式, 所以你可以使用 `JavaScript` 的数学函数和逻辑运算符来进行计算和判断

| 符号 | 含义 |
| :---: | :---: |
| `:::变量名:::` | 引用其他变量的值, 会在计算时替换为指定变量的值 |
| `min(:::变量名:::)` | 引用变量的最小值, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
| `max(:::变量名:::)` | 引用变量的最大值, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
| `mean(:::变量名:::)` | 引用变量的均值, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
| `mode(:::变量名:::)` | 引用变量的众数, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
|  `q1(:::变量名:::)` | 引用变量的 `25%` 分位数, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
|  `q2(:::变量名:::)` | 引用变量的 `50%` 分位数 (中位数), **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
|  `q3(:::变量名:::)` | 引用变量的 `75%` 分位数, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |
| `std(:::变量名:::)` | 引用变量的标准差, **只适用于等距或等比数据**, 注意: 小括号和 `:::` 之间**不要**有空格 |

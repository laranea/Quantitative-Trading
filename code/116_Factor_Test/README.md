# 116组合因子测试





## 处理流程

**此部分的目标是：根据116个已有的特征值进行股价涨跌幅的分类预测，完成概率因子的测试。**



预测的方案分为分类和回归，分类和回归的标签值如下表，其中分类时采用两种标签进行测试，

1. 第一种三分类划分：按照股票涨跌幅排序，收益前30%标记为1， 后30%标记为-1，其余为0
2. 第二种两分类划分：按照股票涨跌幅排序，收益前30%标记为1， 其余为0

| 分类标签值：     | 1.按照股票涨跌幅排序，收益前30%标记为1， 后30%标记为-1，其余为0 |
| ---------------- | ------------------------------------------------------------ |
|                  | **2.按照股票涨跌幅排序，收益前30%标记为1，其余为0**          |
| **回归标签值：** | **股票涨跌幅的具体数值 **                                       |



## **数据构建**

| **特征值：**     | **一共选取六大类因子中的106个特征值+10个风险因子**           |
| ---------------- | ------------------------------------------------------------ |
| **分类标签值：** | 1.按照股票涨跌幅排序，收益前30%标记为1， 后30%标记为-1，其余为0 |
|                  | 2.按照股票涨跌幅排序，收益前30%标记为1，其余为0              |
| **回归标签值：** | 股票涨跌幅的具体数值                                         |
| **数据时间：**   | 2009年10月-2018年8月                                         |
| **缺失值处理：** | 按照行业均值填充                                             |
| **数据标准化：** | 基于原始数据的均值（mean）和标准差（standard deviation）     |
| **训练集：**     | `月频：67180   周频：290294 `                                |
| **测试集：**     | `月频：118998  周频：500736`                                 |



| 预测滚动周期： | 月频数据：根据前12个月的数据值预测后一个月的值，前24个月预测后一个月 |
| -------------- | :----------------------------------------------------------- |
|                | **周频数据：根据前48周的数据值预测后一周的数据**             |



## 预测模型

预测分为两种，单个模型预测和集成模型预测，集成模型预测分为两种，一种是简单的平均集成，一种是Meta_Stacking的元模型预测

| 单个模型预测 |               |
| ------------ | ------------- |
| 集成模型预测 | 简单平均集成  |
|              | Meta_Stacking |

分类模型：xgb，lgb，RF，GBoost

回归模型：lasso，ENet，KRR，GBoost，xgb，lgb



## 因子处理

因子测试阶段分为两种。

因子中性化处理：为了在用某一因子时能够剔除其他因素的影响，使得选出的股票更加分散，需要进行中性化处理。（即：为了消除因子中的偏差和不必要的影响）

对于因子来说，中性化主要考虑市场风险和行业风险。

行业中性:多头组合的行业配置与对冲基准的行业配置一致，目的在于剔除行业因子对策略收益的影响，仅考察行业内部个股的超额收益。（行业中性策略的净值曲线往往比较平稳，回测较小）

风格因子中性:多头组合的风格因子较之对冲基准的风险暴露为0，目的在于使多头组合的风格特征完全与对冲基准相匹配，使得组合的超额收益不是来自于某类风格

| 目的：         | 为了在用某一因子时能够剔除其他因素的影响，使得选出的股票更加分散，需要进行中性 化处理。（即：为了消除因子中的偏差和不必要的影响） |
| :------------- | :----------------------------------------------------------- |
| **主要考虑：** | **市场风险和行业风险**                                       |
| **处理方式：** | 利用回归，得到一个与风险因子线性无关的因子，即通过线性回归提取残差作为中性化后的因子。（处理完成后的中性化因子与风险因子之间的相关性严格为0） |

| 因子测试 | 第一种直接选取前100只股票，等权分配                          |
| -------- | :----------------------------------------------------------- |
|          | 第二种选择前100只股票，根据行业基准权重进行不等权配置（每个行业至少要有1只股票） |

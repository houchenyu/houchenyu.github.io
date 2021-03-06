---
title: 平稳序列建模的一般过程
---

### 1.验证是否为白噪声序列

所谓白噪声序列是指虽然该序列是平稳的，但是各个时刻的值之间没有联系，则无法对其进行建模分析。

```
Box.test(data, type = "Ljung-Box", lag = 1)
type:选择统计量，默认是Q统计类，Ljung-Box是LB统计量
lag:延迟期数
```
原假设：假设延迟期<=lag期之间相互独立
如果 p值小于0.05,则拒绝假设，说明不是白噪声序列，可以进行下一步建模。
### 2.绘制自相关图和偏相关图
```
acf()
pacf()
```

观察自相关图和偏相关图

### 3.选择合适的模型
利用以下的规则去选择模型

| 模型 | 自相关系数 | 偏相关系数 |
| --- | --- | --- |
| AR(p) | 拖尾 | p阶截尾 |
| MA(q) | q阶截尾 | 拖尾 |
| ARMA(p,q) | 拖尾 | 拖尾 |
### 4.参数估计
```
arima(x,order = ,include.mean = T, method = "CSS-ML")

-method:参数估计方法
    CSS-ML：条件最下二乘与极大似然估计混合方法
    ML：极大似然估计
    CSS：条件最小二乘估计
```
### 5.模型检验
确定好模型后，还要对拟合的模型进行必要的检验
#### 5.1.模型的显著性检验
检验模型的有效性。通过计算残差序列是否为白噪声序列。若是，则说明该模型是显著有效的。

```
model.fit<-arima(data,oder = c(1,0,1), method = "CSS")
Box.test(model.fit$residual,lag=6)
##model.fit$residual 表示检验残差
```
如果p值大于0.05，则可以接受假设。说明残差是白噪声了。
#### 5.2.参数的显著性检验
检验是否每个参数都是显著非0，检验的目的是为了使模型最精简。

计算参数估计: <a href="https://www.codecogs.com/eqnedit.php?latex=t&space;=&space;\frac{\phi_i}{\sigma}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?t&space;=&space;\frac{\phi_i}{\sigma}" title="t = \frac{\phi_i}{\sigma}" /></a>
\sigma为标准差

```
pt(t,df,lower.tail)
```
### 模型优化
有时候可以有多个模型可以拟合一个序列，这个时候引进AIC和BIC信息准则的概念进行模型优化。


<a href="https://www.codecogs.com/eqnedit.php?latex=AIC&space;=&space;n&space;\text{ln}(\sigma^2)&plus;2(p&plus;q&plus;2)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?AIC&space;=&space;n&space;\text{ln}(\sigma^2)&plus;2(p&plus;q&plus;2)" title="AIC = n \text{ln}(\sigma^2)+2(p+q+2)" /></a>




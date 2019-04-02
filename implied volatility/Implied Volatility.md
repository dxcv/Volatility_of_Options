## 计算隐含波动率的方法

1. 在存在活跃的期权市场的情况下，可以利用当前对应标的的期权价格信息，通过反解Black-Scholes公式，得到的波动率就是隐含波动率；可以使用牛顿迭代法实现快速收敛；

2. 在没有期权价格的情况，可以将实盘价格视为蒙特卡洛模拟的路径：

   在历史上每一天都考虑一个期限为T的期权，计算T期后的收益，折合成期初价格指数的百分比，相当于一个期初价格为1，期末价格为$\frac{S_T}{S_0}$的期权，对于一个平价$(K=S_0)$)的欧式看涨期权，期末的损益（Profit & Loss)为：

   $$\begin{equation} pl =  \begin{cases} {\frac {S_T}{K} - 1} & S_T \geq K \\\ 0 & S_T < K  \end{cases} \end{equation}$$

   滚动计算M个期权，M应取一个较大值，如M=1000，就可以得到M个$pl$，相当于蒙特卡洛模拟下的M个路径的$pl$，所有损益的平均值就可以看成是期权价格的近似：

   $$Call \approx PL = \frac{1}{M} \sum\limits_{i=1}^{M} {pl}_i$$

   将得到的期权价格的近似代入B-S公式，反解即可得到隐含波动率。

   因为路径来原始实盘价格，且隐含假定了“在所有M个期权的期限内隐含波动率是不变的”，M太小，则无法发挥蒙特卡洛模拟的效果，会产生很大误差；M太大，则可能会因为时间跨度太大，波动率本身已经发生较大变化。因此M的选择会对结果产生较大的影响。

3. 使用实盘价格数据做对冲，因为一个完美的对冲策略会使得最后对冲的价格等于期权的理论价格，所以可以通过建立对冲成本关于波动率的函数$h(\sigma)$，与同样关于波动率的函数$BS(\sigma)$联立：

   $$h(\sigma) = BS(\sigma) $$

   $$h(\sigma) - BS(\sigma) = 0$$

   解这样一个非线性的方程即可，使用scipy.optimize.brentq，在给定的边界函数值异号的情况下可以很快出结果。

## 中证500指数的隐含波动率

由于没有中证指数相关的期权，所以只能采用后两种方法计算隐含波动率

![第二种方法](https://github.com/Jensenberg/volatility-and-Option/blob/master/data/implied%20volatility.png)

可以看到M的选择对于结果有很大的影响；

![第三种方法](https://github.com/Jensenberg/volatility-and-Option/blob/master/data/hedged%20implied%20volatility.png)

由于仅仅是每日进行对冲，对冲频率不高，且delta-中性只能对冲一阶的价格变化，所以对冲成本存在一定的误差，从而使得波动率曲线不是很光滑。

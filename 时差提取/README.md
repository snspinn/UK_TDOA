![uk-logo](https://s2.ax1x.com/2020/01/19/1C8qXt.png)
# 无人机无源探测关键技术研发实验室（软件说明模板）
## UK_TDOA项目(软件)

### 1.项目开发环境

- MySQL版本:8.0.17 Community Server
- Python解释器版本:3.7.4
- 第三方依赖库
    
|Lib            |Ver    |
|:----          |:----  |
|numpy          |1.17.4 |
|pymysql        |0.9.3  |
|serial         |0.0.97 |
|matplotlib     |3.1.2  |
|geographiclib  |1.50   |
|django         |3.0.1  |

### 2. 项目源码简要说明
- 1.代码原理

       基于互功率谱的广义互相关算法的时延估计算法的python实现
										  ——（以roth加权法为例）
原理:
   时延估计的方法有多种，其中，广义相关算法是最早出现也是引起人们关注
较多的时延估计算法，其理论依据在于假设两个通道的噪声不相关，且时延采样
是采样周期的整数倍，如下面所示：



![](https://github.com/Norman-Trx/picture/blob/master/%E5%85%AC%E5%BC%8F.png)



式中X_1 (t)、X_2 (t)为主机站和辅助基站接受到的由射频信号经变频得到的基带信号，N_1 (t)、N_2 (t)分别
为两基站接收机和系统过程中的加性互不相关的高斯白噪声，τ为两基带信号之间的时间差值。
   由于X1信号和X2信号中的R_1 (t)和R_2 (t-τ)是具有相关性质的，而噪声是不相关的。因此，在我们
把它们两个进行互相关处理之后，噪声的互相关值就会为0，这样一来，得到的结果就只剩下了信号的成分，如果
以此时的时间作为坐标横轴，相关值作为坐标纵轴，相关之后的最大值出现的横坐标位置即为时延估计值。
这个就是著名的一般互相关算法。
  一般互相关计算法简单而且直观，但是互相关函数受信号的谱性和噪声的影响，此方法不能兼顾时延估计的
分辨率和稳定性。因此，为了提高时延估计的精确度和稳定性，人们就决定在对信号进行互相关处理之前，先进行滤波
处理。而对信号的滤波就等效于在频域的加权处理。这样可对信号和噪声进行白化处理，以增强信号中信噪比较高的频率
成分，获得更高的TDOA时差估计精度，能抑制信道中噪声的影响，之后，再通过反傅里叶变换将其变换到时域，得到广义的
互相关函数。然后进行和之前一样的处理即可，这个就是广义互相关算法。
由维纳-辛钦功率定理：



![](https://github.com/Norman-Trx/picture/blob/master/%E5%8A%9F%E7%8E%87%E5%AE%9A%E7%90%86.png)




  式中G_12 (ω)为X_1 (t)和X_2 (t)的互功率谱密度函数，δ(ω)为广义互相关频域的加权分量。这个加权的分量可以取不同
的值，表1中已经列出。而维纳-辛钦功率定理表示：信号的互相关函数是它的互功率频谱密度的傅里叶逆变换。因此，上式即为
两个信号的互相关函数。我们就只需要得到两个信号的互相关函数。然后进行加权处理即可。




![](https://github.com/Norman-Trx/picture/blob/master/%E5%8A%A0%E6%9D%83%E5%87%BD%E6%95%B01.png)




![](https://github.com/Norman-Trx/picture/blob/master/%E5%8A%A0%E6%9D%83%E5%87%BD%E6%95%B02.png)







其中，Φ_11 (f)表示信号的自相关函数，Φ_12 (f)表示两个信号的互相关函数。

ROTH和SCOT加权对基于互功率谱的广义互相关函数的峰值尖锐程度在高信噪比情况下，性能较好。而基于周期互谱密度和权值为PHAT
的互功率谱的广义互相关算法在低信噪比的环境下，性能下降较少。根据不同情况下选择不同加权值处理即可。
- 2.代码接口定义（需要说明的输入输出变量等）
1.	x1和x2是两个模拟输入信号。
2.	Sxy是两个信号的互相关函数。
3.	R11是信号1的自相关函数。
4.	X1和X2表示的是两个输入信号经过傅里叶正变换之后得到的两个结果。
5.	RH是滤波操作后得到的两个信号的互相关函数。
6.	delay表示的是提取到的时差值，是代码的输出结果。

- 3.代码流程


![](https://github.com/Norman-Trx/picture/blob/master/%E6%B5%81%E7%A8%8B%E6%A1%86%E5%9B%BE.png)



- 4.预期结果
出现图片，提取的时差值和matlab上估计的值无较大偏差
- 5.实际结果
出现时差值和图片，但是值和matlab差异较大
- 6.原因分析
Python中的加噪函数可能有问题，和matlab中自带的添加高斯白噪声的函数有差异，接下来要着手于解决这个问题。
- 7.下一步工作
优化算法，解决加噪问题，并将输入信号修改为无人机信号，继续实验。


### 代码更新记录
2020年2月28日
将代码修改为PHAT加权方法
理由：
原来的RHAT加权在低信噪比之下，锐化图像峰值的效果较差
而经过实验后发现，PHAT加权方法能在低信噪比的情况下保持优秀的锐化效果
2020年3月20日
新增加内插法，以提高精度
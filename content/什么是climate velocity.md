---
type:
  - 方法/文档/指南
project:
  - Fire_velocity 
status:
  - done
created: 2026-01-14 13:42
updated: 2026-02-23 10:37
---
<font color=#F36208>场景描述：</font>
介绍climate velocity概念

---
## VoCC包提供的主要函数

如下所示
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260115163529228.png)

以温度的变化为例进行说明：
1. tempTrend：用于表示长期局部气候趋势，直接计算某个像素长期气候趋势的斜率即可，得到的单位是℃/yr

2. spatGrad：用于表示局部空间气候梯度，这个就是在一个3×3的栅格里面计算的，所谓的这个空间梯度，就是中心栅格，与周围八个栅格中差值最大的那个（的栅格的梯度。计算方式为：**两个栅格之间的温度差/实际地理距离**  单位是℃/km，这个值，衡量的是在空间上的“坡度”有多陡。

3. gVoCC的数学定义，即时间趋势/空间梯度：$$gVoCC (km/yr) = \frac{tempTrend (^\circ\text{C/yr})}{spatGrad (^\circ\text{C/km})}$$
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260115171303440.png)
gvocc的输入就是时间趋势/空间梯度，得到的返回值为voccMag，单位一般是km/yr（取决于投影）和voccAng，单位是度(0°=北, 90°=东)。

假如说，对于单个气温栅格，计算出来的的voccMag = 10km/yr， voccAng = 20°，则表示在该栅格位置，特定的火险水平（BI 值）正在以每年 10 公里的速度向北偏东 20° 的方向移动。


![Uploading file...9v558]()


![](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260115231226538.png)

$$voccAng = \begin{cases} spatGradAng + 180^\circ, & \text{if } tempTrend > 0 \\ spatGradAng, & \text{if } tempTrend < 0 \end{cases}$$


在火险增加（`tempTrend > 0`）的情况下： **`gVoCC` 的方向箭头 = 高火险带的扩张方向 = 为了维持原本低风险状态必须撤退的方向**

### 1. 数值推演（以维持 BI=50 为例）

假设当前的 BI 分布如下（西北低，东南高）：
- **西北 (NW)**：40
- **中心 (Focal)**：**50**（这是你现在的火险水平）
- **东南 (SE)**：60

现在发生**场景 A（火险增加）**，假设全地区 BI 增加了 10 个点：

- **西北 (NW)**：变成了 **50**
- **中心 (Focal)**：变成了 60
- **东南 (SE)**：变成了 70

**你的目标：** 维持原本较低的火险水平（BI=50）。 **你的选择：**
- **向东南跑**：你会跑到原本是 60、现在变成 **70** 的地方。火险更大了，这显然不是你想维持的“较低水平”。
- **向西北跑**：你会跑到原本是 40、现在刚好变成 **50** 的地方。你成功找回了原来的舒适区。
**结论：** `gVoCC` 算出的方向（西北，315°）正是你**为了维持原状而必须移动的方向**。





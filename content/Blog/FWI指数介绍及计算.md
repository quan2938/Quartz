---
type:
  - 方法/文档/指南
project:
  - Fire_velocity 
status:
  - done
  - published
created: 2026-01-05 16:09
updated: 2026-01-28 10:47
---
<font color=#F36208>场景描述：</font>主要介绍加拿大森林火灾等级系统中，FWI子系统的组成，各个子系统的含义，及其计算过程

---
## 火灾危险等级系统与火灾危险指数

火灾危险等级系统和火灾危险指数是两个不同的概念

**主流的火灾危险等级系统**
1. 加拿大森林火灾等级系统 ( Canadian Forest Fire Danger Rating System, CFFDRS)
2. 美国国家火灾危险等级系统（National Fire Danger Rating System, NFDRS）
3. 澳大利亚火灾危险等级系统 (Australian Fire Danger Rating System, AFDRS)

**主流的火险指数**
1. Fire Weather Index system：是加拿大森林火灾等级系统的关键组成之一，FWI系统的输出包括：FFMC, DMC, DC, ISI, BUI, FWI
2. Burning Index：美国国家火灾危险等级系统的关键输出之一，其他输出包括：ERC, SC等
3. Fire Behaviour Index (FBI)：澳大利亚火灾危险等级系统的关键输出之一

**小结**
1. 这些主流的火危险指数，一般都是这些火灾危险等级系统的关键输出，而在这些主流火险系统中，一些其他指数，比如CFFDRS中的ISI,BUI, NFDRS中的ERC等指数，同样也被广泛应用于评估野火风险

2. 还有一些火危险指数，比如KBDI (Keetch-Byram Drought Index）指数，并不是这些主流的火灾危险等级系统的输出，但也有不少研究应用这个指数去评估野火风险

3. 这些指数中，像BI, FBI的计算，都是需要有燃料图层的输入的。而对于FWI，KBDI的计算，仅需要气象输入即可

4. FWI指数，从命名上就可以发现，这个指数一般是针对林地的，因为这个系统设计初衷就是为了加拿大森林防火设计的，而BI，FBI则适用于多种燃料类型

<font color=#F36208>按：</font>难怪FWI应用最广泛，因为相比较而言计算最简单，只需要气象数据就能完成计算。此外，这些指数背后的计算涉及的处理还是比较复杂的，因为之前有一些遥感背景，看到这些指数下意识的以为是一些变量/波段之间的四则运算，实际上这些火险系统还是比较复杂的

## FWI系统的组成
气象数据的输入要求是：温度，相对湿度，风速，降水

这些气象数据的输入原则上要求都是当地时间12：00的观测值，关于FWI系统的计算，这里之前整理了一份文档[[22 Fire Weather Index（FWI）指数计算]]

下图介绍了FWI系统的结构，包括气象输入，燃料水分代码，火行为指数
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260105171537793.png)
[FWI系统结构示意图链接](https://www.nwcg.gov/publications/pms437/cffdrs/fire-weather-index-fwi-system)
[美国国家野火协调小组对FWI的介绍](https://www.nwcg.gov/publications/pms437/cffdrs/fire-weather-index-fwi-system)

<font color=#F36208>按：</font>官方对FWI的表述中，全称是FWI system，FWI是这个system系统中的一个输出。

### FWI系统中的燃料湿度代码
这个“燃料湿度代码”翻译起来其实有点奇怪，原文确实又表述为：“fuel moisture codes”

<font color=#F36208>按：</font>我的理解是，把FWI系统中，FFMC, DMC, DC理解为三种不同的燃料类型即可
#### FFMC（Fine Fuel Moisture Code）
表示森林地表细小燃料（枯叶，针叶）的湿度，主要是树冠下的阴影区，对应16h延迟燃料。范围为0-101，FFMC值越高，表示燃料越干燥

经验换算：100 - FFMC ≈ 10 小时燃料湿度（%）

#### DMC（Duff Moisture Code）
表示分解有机物（枯枝，落叶层）湿度，对应约15天（360h）延迟燃料，这个值的范围没有量纲，且无上限，反映中层燃料的干燥程度

#### DC（Drought Code）
表示深层土壤和粗燃料干燥程度，这个和KBDI指数有点类似，对应约53天（1272h）延迟燃料，最大值为1000，极端干旱下一般可达800

**小结：** FFMC对短期天气变化敏感，而DMC和DC对长期干燥趋势敏感，这两个值变化慢，但是影响深层燃料。这三个code，实际上指的就是三种燃料类型

<u>上文的提到的，"对应xxx延迟燃料"，指的是这三种燃料（细小燃料，枯枝/落叶，深层土壤/粗燃料）对环境湿度变化的响应时间，用于描述燃料干燥，或者吸湿的时间。</u>

比如说：对于FFMC燃料，假如这里有一堆枯树叶，降水发生后，大概16h后才能晒干，对于DC燃料，比如大树根，深土，降水发生后，大概要53天左右才能干

体现在FWI的计算中的话，这反映的是“干燥/湿润速率”，他们会根据不同的响应速度（timelag）而进行变化。如假如今天降水量达到10mm，FFMC可能从90下降到40，而DMC则只从60降到55，DC则几乎不变，只从400下降到395

下图给出了这三种燃料代码对降水的响应的更多解释，注意图中0.94'',0.81'',7.9''代表的是那段时期的降水量（英寸）
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260105182037023.png)
可以发现
1. FFMC对于降水响应迅速，恢复也比较迅速
2. DMC对于降水响应次之，恢复也次之
3. DC的变化是最稳定的，短期降水对其影响不大，随着升温，DC基本是稳定上升，直到遇到大量的降水
4. 6月以前，基本上FFMC主导的野火，6月中旬到7月初，主要是DMC主导，7月中下旬到8月上旬，主要是DMC主导


> [!NOTE] 关于燃料的“响应时间”
> 在40 Scott and Burgan Fire Behavior Fuel Model（FBFM40）燃料模型分类中，同样也涉及到关于燃料“响应时间”的一个燃料分类
> ![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108135527432.png)
> 这里直接是用1hr，10hr，100hr表示的，关于燃料的“响应时间”实际上对应的是火行为模型中一个“time-lag”概念
> 
> 我之前一直把1hr，10hr，100hr理解为，烧完这一种燃料类型，所用的时间。但是结合FWI系统中，FFMC，DMC，DC的定义来看
> 
> 1hr，10hr，100hr实际上表示的是<u>死燃料</u>对环境的响应时间。以GR3这一种燃料类型为例，有0.1t的燃料需要1hr达到与环境的平衡，有0.4t的燃料需要10hr达到与环境的平衡

<font color=#F36208>按：</font>这里就先了解这么多了，实际上关于这个“响应时间”涉及到的内容还蛮多，在FBFM40中，1hr指的还只是这个燃料变化幅度达到63.2%所花费的时间，63.2%是根据燃料湿度变化的一个指数衰减公式计算得到的。越究越细了，以后用到再看吧
<font color=#F36208>再按：</font>明晰一个概念是，在燃料模型的有关术语表示中，所谓的“延迟燃料”，“响应时间”“fuel moisture time-lag”，指的都是这种燃料湿度出现变化后，达到与周围环境平衡，所花费的时间

### FWI系统中的火行为指数
#### ISI(The Initial spread index)
表示火灾的初始蔓延潜力，类似于NFDRS中的SC（spread component）指数，计算输入是FFMC和风速

#### BUI(The buildup index)
表示燃料蓄积干燥程度，类似 NFDRS 的 ERC（Energy Release Component），计算输入是DMC和DC

#### FWI(The Fire weather index)
综合 ISI 和 BUI，表示总体火灾强度潜力，类似 NFDRS 的 BI（Burning Index），计算输入是ISI和BUI

下图展示的是三个火行为在火灾季节的变化趋势，可发现，一般只有ISI和BUI都达到一个高值的时候，FWI才会达到一个高值。
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108143456326.png)

## FWI的计算
在这个文档中，介绍了对FWI的计算[[22 Fire Weather Index（FWI）指数计算]]

重点关注FWIFunctions.py这个脚本，这个脚本介绍了三个燃料湿度编码（FFMC、DMC、DC）和三个火行为指数（ISI、BUI、FWI）的计算过程。
### FFMC的计算
FFMC的计算，具体可以看函数脚本，根据AI辅助我补充了一些注释
这个FFMC的计算过程，需要的输入就是：温度，相对湿度，风速，降水，前一天的FFMC

各个变量起到的作用，看这个代码就明白了

可以发现在FFMC的计算过程中，涉及到
1. 对降水的修正（考虑降水的湿润过程）
2. 对周围环境湿度的修正（根据周围环境的干燥/湿润过程）
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108154746099.png)

### DMC的计算
DMC的计算需要的输入是温度，相对湿度，降水，前一天的DMC，纬度，月份

这个DMC的计算过程，实际也是分为
1. 根据降水的湿润过程
2. 干燥过程，这个干燥过程涉及到纬度和白昼长度

![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108160826647.png)
<font color=#F36208>按：</font>风速在DMC的计算过程中并没有发挥作用，LAT和MONTH被考虑到DMC的计算中

### DC的计算
DC的计算和DMC也是非常类似，也是包括降水过程和湿润过程

但是注意DC的计算，仅仅需要TEMP和RAIN这两个气象变量，以及前一天的DC，LAT，MONTH
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108161540900.png)

> [!NOTE] DC和KBDI
> 在对DC的介绍中，就提到了这个指数和KBDI指数有点类似的，后查询了一下，基本也差不多，KBDI的计算输入主要也是：每日最高气温、降水、年降水量、前日 KBDI

<font color=#F36208>按：</font>用于计算FWI的这个代码，感觉用处还是大一点，一下子把用于表示干旱程度的DC和许多其他野火行为指数一并也计算了

### ISI的计算
计算过程如下，根据WIND，和FFMC计算
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108162312973.png)
 
### BUI的计算
计算过程如下，根据DMC和DC计算
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108162617644.png)

### FWI的计算
计算过程如下，根据ISI和BUI计算
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108162941242.png)

### 由DMC反推真实湿度
FWIFunctions.py脚本还提供了五个等式，用于根据DMC反推真实含水率，但注意这五个等式，一般都是针对不同林地的腐殖质层（2.5-4cm深度），脚本实际上是没有用到这些公式的，如果要反推的话，这些公式可以提供一个参考
![image.png](https://01-ob-imageinbox-1316020729.cos.ap-nanjing.myqcloud.com/02-ob-CreatedInUS/20260108164313342.png)

**小结：** 把FWI各个指数的计算代码看一遍，就能比较好的理解各个气象变量在其中发挥的作用了。

## FWI计算过程中的注意事项
### 小时版本的FWI计算
FFMC的计算是有小时级别计算版本的，如果同时使用小时级别的气象数据分辨率的话，可以相应的生成小时级别的FWI

<font color=#F36208>按：</font>不太确定小时级别的版本是否只需要把气象数据采用小时级的输入即可，但是注意FWI的计算，是允许有小时级别的输出的

### 计算中断后的重新启动
这个一般指的就是“越冬”的问题，因为在冬季有持续积雪覆盖的地区，FWI的计算是需要重新启动的（不能用上一年的值作为新计算的输入了）

而这个季节启动，一般被定义为：积雪离开该区域后的第三天，默认的启动值为FFMC=85，DMC=6，DC=15

如果在季节中每日观测发生中断且无法补估缺失值（也就是说在冬季以外的时间，被中断了）：
- 必须为缺失观测的最后一天估算燃料湿度码
- 并在恢复观测的第一天把这些估算值当作“昨天”的燃料湿度码来用（因为这些指数是日递推的，需要前一天的状态作为起点）

<font color=#F36208>按：</font>这个“越冬”问题，先不延申了，FWIFunctions.py脚本里面应该是没有涉及到如果处理越冬的，下次再继续写，感觉还蛮重要的
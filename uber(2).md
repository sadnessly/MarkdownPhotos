# UBER H3

## 四、全球离散网格系统
在实际生活中，在当前位置没有气象站的情况下，如何获知我所在位置的大气pm2.5的污染情况呢？运用网格是一个好的解决方案。
### 1 不规则格网
荷兰气候学家A·H·Thiessen提出了一种根据离散分布的气象站的降雨量来计算平均降雨量的方法，即将所有相邻气象站连成三角形，作这些三角形各边的垂直平分线，于是每个气象站周围的若干垂直平分线便围成一个多边形。用这个多边形内所包含的一个唯一气象站的降雨强度来表示这个多边形区域内的降雨强度，并称这个多边形为泰森多边形。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/v2-0dfd03956912a6085d8f5a5aefc642fb_r.jpg" width="500" hegiht="300" align=center />

同样，对于打车平台中，如何知道某一个位置的供需情况，并且引导司机往供小于求的位置移动。由于而生成泰森多边形的算法复杂度很高，且用户和司机的位置是实时变化的，不可能实时进行多边形的生成和显示。因此常采用手工录入围栏的方式，而手工录入地理围栏可能会有下面问题：
* 由于围栏是通过人工画的，精度太低
* 基于地理围栏粒度的供需调控粒度太粗
* 在部分过于复杂的城市，人工维护围栏成本高
* 在围栏边界处的处理比较困难

### 2 规则网格
基于以上的问题，需要运用规则网格对全球进行划分，并且有以下特性：
* 每个网格的大小需要满足供需的要求
* 可以计算出每个网格周边的格子
* 计算简单方便，方便管理

在规则的图形的选择上，必须需无缝密铺平面，因此图形主要选择三角形，正方形以及六边形。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190321-112418@2x.png" width="500" hegiht="300" align=center />

对于临近关系，三角形和正方形网格都有顶点临近和边临近。因此三角形和正方形的临近等级不统一，而六边形只有边临近，临近的等级相同，且相比于正方形网格有变形小的特点。但六边形也有其自身的缺陷，比如没有全铺满特性。即大的六边形不能由小的六边形组合而成，而三角形和正方形网格是有全铺满特性的。六边形虽然没有全铺满特性，但是可以通过旋转一定的角度近似的进行铺满。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190322-142601@2x.png" width="500" hegiht="300" align=center />

## 五、柏拉图立体

在流浪地球中，有这样一个场景，程序员李一一手里握着一个正二十面体的骰子。为什么正二十面体可以作为骰子出现呢？这就引出了柏拉图体即正多面体的概念。由于网格系统无需将全球都呈现在平面上，因此无需选择圆柱体等在平面上易读的方式对地球进行投影，而尽可能选用变形小的方式对地球。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/v2-dfab80d283bac1358be070eb2ef3bbc2_hd (1).jpg" width="500" hegiht="300" align=center />

如果一个多面体的所有面都是全等正多边形，所有多面角也全等，我们就说它是正多面体或柏拉图体。有无限多种正多边形，而正多面体只有五种。正多面体根据面的数目来命名，也就是：4个正三角形的正4面体；6个正方形的正6面体（立方体）；8个正三角形的正8面体；12个正五边形的正12面体；20个正三角形的正20面体。由于柏拉图体的每个面的出现概率出现相同，因此常作为骰子出现。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/4d086e061d950a7b6b47c3ca0ad162d9f2d3c94a.jpg" width="200" hegiht="300" align=center />

柏拉图立体还有一个重要的特性即正多面体的中心到每个体的顶点和面的距离相同。根据这个特性可以对柏拉图体做内切球或是外切球。据此，我们将地球近似为一个球体，就可以使用柏拉图体进行内切或是外切，进而进行投影。
如图，google s2即采用了六面体作为投影方式：

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/6879b5d273829197535c347f2adddb74_hd.png" width="400" hegiht="300" align=center />

但六面体是不能很好的贴合球体的，且投影角度越大，变形越大。虽然google s2对投影做了一定修正，但是还是会有较大的变形。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190226-173652@2x.png" width="400" hegiht="300" align=center />

因此uber h3 选用了柏拉图体重面数最多的，即二十面体作为球的内切多面体，相比于六面体，二十面体会更好的贴近球体，变形更小。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/geogebra-export (2).png" width="400" hegiht="300" align=center />

在网格的选择上，由于google s2的六面体的每个面是正方形，因此主要选用正方形作为规则的网格。而20面体的每个面是三角形，可以选用三角形或是六边形作为规则网格。由于上述的六边形的诸多优势，因此uber h3选择了六边形作为规则网格。

## 六、UBER H3
### 1 球心投影
球心投影是方位投影之一。以球心O为投射中心，把球面上的P点投射到它的切平面上的投影法。它是最有用的投影方法之一，17世纪中叶开始使用。它能把球面上的大圆，投射成直线。常用于导航、测绘航线、寻找星座与星体。还可用来制作日晷，因此亦称日晷投影。而平面与球体有且仅有一个切点，将地球表面上的点投影在平面上。如下图，若计算球心点到地图的距离，则有计算公式：
$${\displaystyle r(d)=R\,\tan {\frac {d}{R}}}$$
球心投影目的
R为球心到切点的距离，即球半径，而d代表球面上的两个点的球面距离，r为这两个点投影在平面的真实距离。

![enter image description here](https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/86c0460e2f881850a75eac2f5e0b7d08.png)

我们可以将这个三维投影图转换为二维，以解释这个公式，如图C为球心，D为平面与球体的切点，Θ为弧度角。根据弧度角公式可得

$${\displaystyle Θ={\frac {弧DB}{R}}={\frac {d}{R}}}$$

则DF的长度为

$${\displaystyle DF=DB\,\tan Θ = R\,\tan Θ }$$

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190222-162911@2x.png" width="300" hegiht="200" align=center />


这种投影，形状变形从中心向外逐渐增大；距中心点 30 度范围内的变形较小。面积变形随距中心距离的增加而增大；以中心点为圆心 30 度半径范围内的变形较小，从中心向外，方向都是准确的。

### 2 uber h3

Uber h3即是基于20面体六边形格网的一种编码系统算法库，提供通过经纬度坐标计算到相应的六边形格子，并输出格子编码等的能力。

多面体投影的基本思想是上图中的平面方位投影，投影平面有很多个。即以球面为原面、多面体表面为投影面建立两者的映射关系。如下图是一个二十面体，可以看到一个球体包裹了该二十面体，且二十面体的每个顶点都与球面相切。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/geogebra-export (2).png" width="400" hegiht="300" align=center />

球面上一点则可以通过球心投影，投影到三角形平面上。

$${\displaystyle r(d)=R\,\tan {\frac {d}{R}}}$$

如图，球面上有一点T，其在20面体上投影点为R。Q为R点在该面上对应的中点，S为Q点所对应的球面上的点。R为地球半径，d为弧ST的球面距离，则r为平面上投影点即QR的的距离。通过球心投影即可将球面上的弧段转换为平面上的线。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/geogebra-export3.png" width="400" hegiht="300" align=center />

再将投影在平面上的线根据角度，计算平面的笛卡尔坐标：

$$x = r * cos（θ）$$
$$y = r * sin（θ）$$

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/20190321202642.jpg" width="400" hegiht="300" align=center />


由于笛卡尔坐标不能准确的表示格子的位置，因此还需要将平面笛卡尔坐标转换为ijk坐标

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190322-143655@2x.png" width="400" hegiht="300" align=center />

再通过ijk坐标计算点落在在哪个格子中。

<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/cpidigits.png" width="400" hegiht="300" align=center />

对于六边形坐标，需要将笛卡尔坐标系转换为ijk坐标系，再根据所要计算的级别，即可以计算出该点落在哪个六边形格子内。计算出在哪个格子中再根据编码规则，即可计算出任意点所在六边形格子的编码。

编码信息如下：
* 用4位编码表示格子的级别
* 用7位编码表示0级的格子（最大的六边形格子，共有122个）
* 用三位编码表示从0级到该级别的位置关系
  
<img src="https://github.com/sadnessly/MarkdownPhotos/raw/master/pic/WX20190322-1426012x.png" width="400" hegiht="300" align=center />

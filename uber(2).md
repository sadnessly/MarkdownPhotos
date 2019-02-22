## 四、全球网格系统
在实际生活中，在没有气象站的情况下，如何获知我所在位置的大气pm2.5的污染情况呢？网格则是一个好的解决方案。
### 1泰森多边形（Voronoi图）
荷兰气候学家A·H·Thiessen提出了一种根据离散分布的气象站的降雨量来计算平均降雨量的方法，即将所有相邻气象站连成三角形，作这些三角形各边的垂直平分线，于是每个气象站周围的若干垂直平分线便围成一个多边形。用这个多边形内所包含的一个唯一气象站的降雨强度来表示这个多边形区域内的降雨强度，并称这个多边形为泰森多边形。

同样的，对于打车平台中，如何知道某一个位置的供需情况，并且引导司机往供小于求的位置移动。使用泰森多边形是可行的，但是由于用户和司机的位置是实时变化的，而生成泰森多边形的算法复杂度很高，不可能实时进行多边形的生成和显示。因此选择手工录入多个多边形（地理围栏），并在这些地理围栏内计算供需的情况。但手工录入地理围栏可能会有下面问题：
* 由于围栏是通过人工画的，精度太低
* 基于地理围栏粒度的供需调控粒度太粗
* 在部分过于复杂的城市，人工维护围栏成本高
* 在围栏边界处的处理比较困难

### 2 规则网格
基于以上的问题，需要运用规则网格对全球进行划分，并且有以下特性：
* 每个网格的大小需要满足供需的要求
* 可以计算出每个网格周边的格子
* 计算简单方便，方便管理

在规则的图形的选择上，必须需无缝密铺平面，才可以覆盖地球，因此图形主要选择三角形，正方形以及六边形。

对于临近关系，三角形和正方形网格都有顶点临近和边临近。因此三角形和正方形的临近等级不统一，而六边形只有边临近，临近的等级相同，且相比于正方形网格有变形小的特点。但六边形也有其自身的缺陷，比如没有全铺满特性。即大的六边形不能由小的六边形组合而成，而三角形和正方形网格是有全铺满特性的。六边形虽然没有全铺满特性，但是可以通过旋转一定的角度近似的进行铺满。


## 五、UBER H3
Uber h3即是基于20面体六边形格网的一种编码系统算法库，提供通过经纬度坐标计算六边形格子的id等的能力。

多面体投影的基本思想是上图中的平面方位投影，投影平面可能有很多个。即以球面为原面、多面体表面为投影面建立两者的映射关系。常见的多面体有正四面体、正六面体、正八面体、正十二面体和正二十面体 ，然后对各个面分别采用球心投影。如下图是一个二十面体，可以想象一个球体包裹了该二十面体，且二十面体的每个顶点都与球面相切。

                             

球面上一点则可以通过球心投影，投影到三角形平面上。由公式

$${\displaystyle r(d)=R\,\tan {\frac {d}{R}}}$$

R为地球半径，d为该点到切点即面中心点的距离，则r为球心到平面上投影点的距离。再计算球面上一点到面中心的方位角，以及面中心到x轴的方位角，两者相减得到两条线的夹角。最后用角度计算平面笛卡尔坐标即：x = r * cos（θ），y = r * sin（θ）。

三角形平面可以划分为若干个六边形格子，大的六边形格子可以划分为更小的六边形，因此不同层级的六边形的六边形边长依次递减，且边长递减倍数为根号7。



对于六边形坐标，需要将笛卡尔坐标系转换为ijk坐标系，即可以计算出该点落在哪个六边形格子内。

 



# 定位


#### 基本概念

里程估计

- 根据传感器感知信息推导机器人位姿（位置和角度）变化  
- 用途：
  -  航位推算 (Dead-reckoning)：基于已知位置， 利用里程估计，推算现在位置

里程估计方法  

- 基于机器人运动感知信息，结合运动学模型
  - 电机码盘
  - IMU（惯性单元，加速度计+陀螺仪）
- 基于环境感知传感器信息，通过最佳匹配估计
  -  激光里程计
  -  视觉里程计（VO）

#### 基于运动感知的里程估计  

##### 基于电机码盘的轮式移动机器人里程估计  

###### 方式

根据电机码盘获得轮子转速  



$$
角速度计算公式 \\
\omega = \Phi ' = \frac{2 \pi n}{η}
$$




对于速度

- 考虑一个轮子固定，则另一个轮子的速度就是rω
- 相对于质心P点速度就是rω/2
- 相加即可

对于角速度

- 同样考虑一个轮子固定，则另一个轮子角速度就是ω
- 故质心角速度也是rω/2l
- 由于两个轮子角速度相反，故为减法

###### 缺点

**累计误差变大**：在航位推算时，里程计**误差被累加**，推算随着时间而增长  

各种误差太大

<img src="../../../../WeChatFile/WeChat%2520Files/wxid_7vg96y67flrl32/FileStorage/File/2024-04/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599(2)/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599/picture/image-20240425203454723.png" alt="image-20240425203454723" style="zoom:67%;" />



##### 基于惯性单元的里程估计  

惯性单元IMU  

- 一般含有三轴的加速度计和三轴的陀螺仪
- yaw不准

- 优点
  - 全天候
  - 采样频率高
  - 短时精度较好
- 缺点
  - 随着时间的增长累积误差较大，无法满足移动机器人长距离精确定位的要求，需要融合其它传感器进行组合导航



#### 激光里程计  

采用ICP(Iterative Closest Point)算法  

- 估计P’集合点与P集合点的初始位姿关系  
  - P'是P点相对于里程计的坐标系下位置改变后的坐标
- 根据最近邻域规则建立P’集合点与P集合点的关联  
  - 构建旋转矩阵，使得P'旋转之后成为P点
- 利用线性代数/非线性优化的方式估计旋转平移量  
  - 优化旋转矩阵
- 对点集合P’的点进行旋转平移  
- 如果旋转平移后重新关联的均方差小于阈值， 结束
- 否则迭代重复上述步骤  



#### 定位问题

确定机器人在世界（全局）坐标系中的位置/位姿  

分为三种解决方法：

- **基于外部设备感知的定位**  
- **基于里程估计的航位推算**  
- **基于本体设备感知的定位**  

##### 基于外部设备感知的定位  

###### 全球定位系统GPS  

由**空间端、 控制端和用户端**三部分组成，也称为GNSS

- 空间端由卫星组成
- 控制端由地面天线站等构成，主要作用监测控制卫星的运行，并对卫星进行时间同步  
- 用户端，采用三点法进行定位

<img src="../../../../WeChatFile/WeChat%2520Files/wxid_7vg96y67flrl32/FileStorage/File/2024-04/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599(2)/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599/picture/image-20240425204715527.png" alt="image-20240425204715527" style="zoom:67%;" />



存在问题

遮挡问题：室内难以使用

多路径问题：楼房太高导致最短路径变成反射后的一条路径



###### 全局视觉观测定位  

搭建一套外部视觉系统，识别机器人，确定其位置  

与GPS相同，在内部搭建多个相机实现定位

<img src="../../../../WeChatFile/WeChat%2520Files/wxid_7vg96y67flrl32/FileStorage/File/2024-04/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599(2)/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599/picture/image-20240425205003148.png" alt="image-20240425205003148" style="zoom:67%;" />

应用约束：

- 摄像头有视野范围约束，当环境较大时需要多个摄像头
- 机器人上也需要有一定的标识方便图像识别定位



##### 基于本体感知的定位  

- 需要预先构建地图，地图坐标系在构建地图时确定
- 对于大规模环境，路标需具备可鉴别性，否则需要正确的数据关联
- 存储大规模环境的图片需要较大的存储空间
- 对于实际环境中存在路标被破坏/污染、更换、稀疏等风险



###### 基于环境人工标识的定位

在环境中部署特殊标签，降低成本，确保可靠性，如

- 地面二维码：适合仓库运输机器人
- 高反射物质：适合叉车

###### 基于环境自然标识的定位  

路标



###### 基于空间标识的定位原理  

<img src="../../../../WeChatFile/WeChat%2520Files/wxid_7vg96y67flrl32/FileStorage/File/2024-04/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599(2)/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599/picture/image-20240425205309913.png" alt="image-20240425205309913" style="zoom:67%;" />

已知三个星星的坐标，并通过传感器测得r或者θ，进行求解坐标



###### 基于位置识别的定位

基本思想：利用本体传感器获得的信息与地图中存储的各个位置上的信息进行匹配，实现位置识别

应用约束：

- 环境的动态变化和不同位置的相似性，对位置辨别带来挑战
- 为适应环境的动态变化，可以考虑对每个位置存储多张图片，但增加存储空间需求
- 图像匹配是一个耗时的工作



##### 控制感知信息融合的自定位  

定义：在给定环境地图(对环境的已有知识)的条件下，根据机器人的运动控制/里程估计信息和传感器感知数据(当前认知)估计机器人相对于环境地图的坐标

<img src="../../../../WeChatFile/WeChat%2520Files/wxid_7vg96y67flrl32/FileStorage/File/2024-04/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599(2)/%25E6%259C%25BA%25E5%2599%25A8%25E4%25BA%25BA%25E5%25AF%25BC%25E8%25AE%25BA%25E5%25A4%258D%25E4%25B9%25A0%25E8%25B5%2584%25E6%2596%2599/picture/image-20240425205623024.png" alt="image-20240425205623024" style="zoom:67%;" />


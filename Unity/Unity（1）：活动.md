# Unity（1）：活动

transform 和 rigidbody

#### prefab

#### component：

脚本控制移动：PlayerLocomotion.cs

![image-20220116204302031](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220116204302031.png)

![image-20220116204550546](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220116204550546.png)

1. 文件名和脚本名必须对应
2. fixedupdate、update
   1. fixedUpdate：消除帧率带来的影响，物理有关的应该写在这里
   2. update：处理输出

#### 控制：

1. 函数getKey、getKeyUp、getKeyDown：

getKey：按住

getKeyUp：按下松开时

getKeyDown：按下向下时

2. 输入有两类 —— 键和轴





#### 使用插件：

1. 步骤（以MLTools为例）：
   1. 目录新建Resource文件夹（Unity会将文件夹下东西打包放入游戏引擎中）
   2. Resource内创建插件的文件
   3. 文件点开“Open in Profile Edit”
   4. 添加Action
   5. Action内绑定分类，选择type
2. a



#### 物理：

1. 刚体
2. 碰撞体



#### 坐标系

1. 世界坐标系

   在unity世界内的，游戏世界内的坐标系

2. 屏幕坐标系

   二维坐标系，屏幕上的位置，原点在左下角。

3. 视口坐标系

4. j



#### 射线检测

+ 视锥体：游戏摄像机所看到的范围



## Qs

+ 万向锁

+ 四元数
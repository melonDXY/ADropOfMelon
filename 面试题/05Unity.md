### 1 模块源码深入 开放项目相关

#### 1.1 Scene Loading System 场景切换 相关

+ 创建一个大的单例控制场景切换：
  + 缺点：当前场景内发生了一些事情，需要通知当前场景或其他场景进行Action；这样需要这个单例去引用到相关类，通知这些事情的发生
+ 使用多个单例实现：
  + 缺点：耦合性较高；SO是资源类型，方便跨scene传递消息
+ 使用ScriptableObject：
  + 将上述的大单例拆成几个小单例，在内存中只有一个，并且可以被其他所读取；于是，不同的事件发生通知不同的ScriptableObject，再通过不同的ScriptObject通知到不同的物件进行反应; 
  + 使用方法：
    + 1. 发生这件事情，使用Raise唤醒相关的SO
      2. 监视器监听到SO被唤醒时，使用脚本下的UnityEvent
    + 有参数： ![image-20220301211245025](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220301211245025.png)



#### 1.2 Input System 输入系统

 多个Action Maps，每个包含多个Actions，Action的properties绑定相对应的输入

+ 方法1：
  + 1. 创建InputActionAsset（右键即可）
    2. 给游戏物品添加PlayerInput组件
    3. 在其中挂上事件![image-20220301212850152](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220301212850152.png)
+ 方法2：
  + 1. 生成一个InputActionAsset，同时会生成一个C#脚本
    2. 再将C#脚本嵌到响应的monobehaviour中![image-20220301213749289](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220301213749289.png)
+ 方法3：
  + 1. 生成C#后，封装到ScriptableObject中，再绑到monobehaviour里
    2. 这样让别的系统监听到输入事件 ![image-20220301213819667](C:\Users\evimb\AppData\Roaming\Typora\typora-user-images\image-20220301213819667.png)



### 2 UGUI

#### 1.UGUI系统及常用控件

Unity 官方的 UI 实现方式，在 UGUI 中创建的所有 UI 控件都有一个 UI 控件特有的 Rect Transform 组件。

+ 在 Unity 3D 中创建的三维物体是 Transform，而 UI 控件的 Rect Transform 组件是UI控件的矩形方位，其中的 PosX、PosY、PosZ 指的是 UI 控件在相应轴上的偏移量。
+ UI 控件除了 Rect Transform 组件外，还有一个 Canvas Renderer（画布渲染）组件，如上图所示。一般不用理会它，因为它不能被点开。



### 3 Unity组件和编程

#### 1. Unity坐标系

##### 1. 概述

Unity使用左手坐标系（笛卡尔坐标系）。

进入屏幕（即自己的前方）为Z轴正向，头顶方向为Y轴正向，右手方向为X轴正向。

在开发中，Unity有四种坐标系，**世界坐标系**、**局部坐标系**、**屏幕坐标系**、**视口坐标系**

##### 2. 坐标系简介：

+ + **世界坐标系**：也就是物体在游戏世界上的坐标系，这是全局的，也就是方法 **transform.Position** 所返回的坐标。
  + **局部坐标系**：如果物体之间有父子关系，例如物体A为物体B的父物体，那么B物体的 **transform.localPosition** 输出的为以A物体为原点的物体坐标
  + **屏幕坐标系**：把屏幕看作一个坐标系，屏幕左下角为（0，0），右上角为屏幕的最大宽高，（Screen.Width, Screen.Height），Z轴为相机的世界坐标中Z轴的负值。但如果用 **Input.MousePosition** 获取鼠标位置，Z轴为0
  + **视口坐标系**：与屏幕坐标系相似，将Game视图的坐标系单位化，左下角（0，0）右上角（1，1）,相对于相机

##### 3. 坐标系转换

+ + 世界：

    + to屏幕：Camera.WorldToScreenPoint

    + to视口：Camera.WorldToViewPoint

    + to局部：InverseTransformPoint

    + ```c#
      // Calculate the transform's position relative to the camera.
      using UnityEngine;
      using System.Collections;
      
      public class ExampleClass : MonoBehaviour
      {
          public Transform cam;
          public Vector3 cameraRelative;
      
          void Start()
          {
              cam = Camera.main.transform;
              Vector3 cameraRelative = cam.InverseTransformPoint(transform.position);
      
              if (cameraRelative.z > 0)
                  print("The object is in front of the camera");
              else
                  print("The object is behind the camera");
          }
      }
      ```

      

  + 局部：

    + to世界：TransformPoint

    + 例如：以下代码创建一个在当前物体右侧的物体

    + ```c#
      using UnityEngine;
      using System.Collections;
      
      public class ExampleClass : MonoBehaviour
      {
          public GameObject someObject;
          public Vector3 thePosition;
      
          void Start()
          {
              // 首先获取相对位置，即采用了局部坐标系,返回到相对于当前物体（2，0，0）的世界坐标
              thePosition = transform.TransfromPoint(Vector3.right * 2);
              // 创建该物体,Instantiate参考https://docs.unity3d.com/2020.3/Documentation/ScriptReference/Object.Instantiate.html
              Instantiate(someObject, thePosition, someObject.rotation);
          }
      }
      ```

      

  + 屏幕：

    + to世界：cam.ScreenToWorldPoint(Vector3)

    + ```c#
      // Convert the 2D position of the mouse into a
      // 3D position.  Display these on the game window.
      // 由于鼠标返回的Z轴是0，并且我们的相机视野为一个视锥体
      //有一个远截面和一个近截面，如果你想转换出来的坐标希望是正确的，那么你赋值进去坐标的Z值应该在远近这个范围内，而且z越大，你转换出来坐标的x，y的范围越大，同时转换出来的z值应该是加上相机的z值的，如果你的z值不在那个范围内，那么输出的就是一个你相机的位置坐标
      
      using UnityEngine;
      
      public class ExampleClass : MonoBehaviour
      {
          private Camera cam;
      
          void Start()
          {
              cam = Camera.main;
          }
      
          void OnGUI()
          {
              Vector3 point = new Vector3();
              Event   currentEvent = Event.current;
              Vector2 mousePos = new Vector2();
      
              // Get the mouse position from Event.
              // Note that the y position from Event is inverted.
              mousePos.x = currentEvent.mousePosition.x;
              mousePos.y = cam.pixelHeight - currentEvent.mousePosition.y;
      
              point = cam.ScreenToWorldPoint(new Vector3(mousePos.x, mousePos.y, cam.nearClipPlane));// z为近截面
      
              GUILayout.BeginArea(new Rect(20, 20, 250, 120));
              GUILayout.Label("Screen pixels: " + cam.pixelWidth + ":" + cam.pixelHeight);
              GUILayout.Label("Mouse position: " + mousePos);
              GUILayout.Label("World position: " + point.ToString("F3"));
              GUILayout.EndArea();
          }
      }
      ```

    + to视口：camera.ScreenToViewportPoint(Input.mousePosition)

  + 视口坐标：

    + to屏幕：camera.ViewportToScreenPoint

    + ```c#
      using UnityEngine;
      
      public class Example : MonoBehaviour
      {
          // Draw an image based on normalized view coordinates
          // rather than pixel positions.
          Texture2D bottomPanel;
      
          void VPToScreenPtExample()
          {
              var origin = Camera.main.ViewportToScreenPoint(new Vector3(0.25f, 0.1f, 0));
              var extent = Camera.main.ViewportToScreenPoint(new Vector3(0.5f, 0.2f, 0));
      
              GUI.DrawTexture(new Rect(origin.x, origin.y, extent.x, extent.y), bottomPanel);
          }
      }
      ```

      

    + to世界：camera.ViewposrtToWorldPoint()

    + ```c#
      // Draw a yellow sphere at top-right corner of the near plane
      // for the selected camera in the Scene view.
      using UnityEngine;
      using System.Collections;
      
      public class ExampleClass : MonoBehaviour
      {
          void OnDrawGizmosSelected()
          {
              Camera camera = GetComponent<Camera>();
              Vector3 p = camera.ViewportToWorldPoint(new Vector3(1, 1, camera.nearClipPlane));//右上角的视口坐标是（1，1），视锥体近截面为Z
              Gizmos.color = Color.yellow;
              Gizmos.DrawSphere(p, 0.1F);
          }
      }
      ```

##### 4. 总结

四种坐标系：世界、局部、屏幕、视口

四种坐标的获取方法：transform.position，transform.localPosition，Input.MousePosition

相互转换的方法：上面

相互转换所需要注意的点：鼠标位置返回的z为0，相机是一个视锥体，所以需要对z进行处理

相互转换常发生的场景：创建新物体、跟随、输入、着色等

### 4. 实践案例

#### 1. 怪物血条的实现






























Previous: [Simple Cameras](gltfTutorial_015_SimpleCameras.md) | [Table of Contents](README.md) | Next: [Simple Morph Target](gltfTutorial_017_SimpleMorphTarget.md)

# Cameras

The example in the [Simple Cameras](gltfTutorial_017_SimpleCameras.md) section showed how to define perspective and orthographic cameras, and how they can be integrated into a scene by attaching them to nodes. This section will explain the differences between both types of cameras, and the handling of cameras in general.  

[Simple Cameras]部分的例子展示了如何定义透视和正交相机，以及如何通过将它们附加到节点来将它们集成到场景中。本节将解释两种相机之间的区别以及一般相机的处理。


## Perspective and orthographic cameras  透视和正交相机

There are two kinds of cameras: *Perspective* cameras, where the viewing volume is a truncated pyramid (often referred to as "viewing frustum"), and *orthographic*  cameras, where the viewing volume is a rectangular box. The main difference is that rendering with a *perspective* camera causes a proper perspective distortion, whereas rendering with an *orthographic* camera causes a preservation of lengths and angles.

有两种相机：透视相机，以及正视相机。主要区别在于使用透视相机进行渲染会导致适当的透视失真，而使用正交相机进行渲染会导致长度和角度的保留。

The example in the [Simple Cameras](gltfTutorial_017_SimpleCameras.md) section contains one camera of each type, a perspective camera with at index 0, and an orthographic camera at index 1:

[Simple Cameras]部分中的示例分别指向一类相机，索引为0的透视相机和索引为1的正交相机：

```javascript
"cameras" : [
  {
    "type": "perspective",
    "perspective": {
      "aspectRatio": 1.0,
      "yfov": 0.7,
      "zfar": 100,
      "znear": 0.01
    }
  },
  {
    "type": "orthographic",
    "orthographic": {
      "xmag": 1.0,
      "ymag": 1.0,
      "zfar": 100,
      "znear": 0.01
    }
  }
],
```


The `type` of the camera is given as a string, which can be `"perspective"` or  `"orthographic"`. Depending on this type, the `camera` object contains a [`camera.perspective`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-perspective) object or a [`camera.orthographic`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-orthographic) object. These objects contain additional parameters that define the actual viewing volume.

相机的类型以字符串形式给出，可以是“perspective”或“orthographic”。根据此类型，相机对象包含camera.perspective对象或camera.orthographic对象。这些对象包含了用于定义实际相机的附加参数。

The `camera.perspective` object contains an `aspectRatio` property that defines the aspect ratio of the viewport. Additionally, it contains a property called `yfov`, which stands for *Field Of View in Y-direction*. It defines the "opening angle" of the camera and is given in radians.

camera.perspective对象包含一个aspectRatio属性，用于定义视口的宽高比。此外，它还包含一个名为yfov的属性，它代表Y方向的视场。它定义了相机的“开启角度”，并以弧度给出。

The `camera.orthographic` object contains `xmag` and `ymag` properties. These define the magnification of the camera in x- and y-direction, and basically describe the width and height of the viewing volume.

camera.orthographic对象包含xmag和ymag属性。这些定义了相机在x方向和y方向的放大倍数，并且描述了视口的宽度和高度。

Both camera types additionally contain `znear` and `zfar` properties, which are the coordinates of the near and far clipping plane. For perspective cameras, the `zfar` value is optional. When it is missing, a special "infinite projection matrix" will be used.

这两种相机类型还包含znear和zfar属性，它们是近距离和远距裁切平面的坐标。对于透视摄像机，zfar值是可选的。缺失时，将使用特殊的“无限投影矩阵”。

Explaining the details of cameras, viewing, and projections is beyond the scope of this tutorial. The important point is that most graphics APIs offer methods for defining the viewing configuration that are directly based on these parameters. In general, these parameters can be used to compute a *camera matrix*. The camera matrix can be inverted to obtain the *view matrix*, which will later be post-multiplied with the *model matrix* to obtain the *model-view matrix*, which is required by the renderer.

解释摄像机的细节，视图和投影超出了本教程的范围。重要的一点是，大多数图形API提供了直接基于这些参数定义查看配置的方法。通常，这些参数可以用来计算相机矩阵。相机矩阵进行逆变换可以获得视图矩阵，该视图矩阵稍后将与模型矩阵相乘从而获得模型视图矩阵


# Camera orientation  相机方向

A `camera` can be transformed to have a certain orientation and viewing direction in the scene. This is accomplished by attaching the camera to a `node`. Each [`node`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-node) may contain the index of a `camera` that is attached to it. In the simple camera example, there are two nodes for the cameras. The first node refers to the perspective camera with index 0, and the second one refers to the orthographic camera with index 1:

相机可以通过变换具有特定的位置和观看方向。这是通过将相机连接到节点来完成的。每个[`node`]可能包含附加到它的摄像机的索引。在simple camera示例中，有两个相机节点。第一个节点是指索引为0的透视相机，第二个节点是指索引为1的正交相机：

```javascript
"nodes" : {
  ...
  {
    "translation" : [ 0.5, 0.5, 3.0 ],
    "camera" : 0
  },
  {
    "translation" : [ 0.5, 0.5, 3.0 ],
    "camera" : 1
  }
},
```

As shown in the [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) section, these nodes may have properties that define the transform matrix of the node. The [global transform](gltfTutorial_004_ScenesNodes.md#global-transforms-of-nodes) of a node then defines the actual orientation of the camera in the scene. With the option to apply arbitrary [animations](gltfTutorial_007_Animations.md) to the nodes, it is even possible to define camera flights.

如[Scenes and Nodes]部分所示，这些节点可能具有定义节点变换矩阵的属性。节点的全局变换然后定义摄像机在场景中的实际方向。通过选择将任意动画应用到节点，甚至可以定义摄像机的运行路径。

When the global transform of the camera node is the identity matrix, then the eye point of the camera is at the origin, and the viewing direction is along the negative z-axis. In the given example, the nodes both have a `translation` about `(0.5, 0.5, 3.0)`, which causes the camera to be transformed accordingly: it is translated about 0.5 in the x- and y- direction, to look at the center of the unit square, and about 3.0 along the z-axis, to move it a bit away from the object.

当摄像机节点的全局变换是单位矩阵时，摄像机的视点位于原点，观察方向沿Z轴负方向。在给定的例子中，节点都具有（0.5,0.5,3.0）的平移，这导致相机被相应地变换：在x方向和y方向上平移0.5，为了查看单位平方的中心，需要沿z轴平移3.0，将相机远离物体。


## Camera instancing and management  相机实例化和管理

There may be multiple cameras defined in the JSON part of a glTF. Each camera may be referred to by multiple nodes. Therefore, the cameras as they appear in the glTF asset are really "templates" for actual camera *instances*: Whenever a node refers to one camera, a new instance of this camera is created.

在glTF的JSON部分中可能定义了多个相机。每个摄像机可以由多个节点引用。因此，glTF资源中定义的相机相对于实际相机实例而言只是“模板”：只要节点指向一个相机，就会创建该相机的新实例。

There is no "default" camera for a glTF asset. Instead, the client application has to keep track of the currently active camera. The client application may, for example, offer a dropdown-menu that allows one to select the active camera and thus to quickly switch between predefined view configurations. With a bit more implementation effort, the client application can also define its own camera and interaction patterns for the camera control (e.g., zooming with the mouse wheel). However, the logic for the navigation and interaction has to be implemented solely by the client application in this case. [Image 17a](gltfTutorial_017_SimpleCameras.md#cameras-png) shows the result of such an implementation, where the user may select either the active camera from the ones that are defined in the glTF asset, or an "external camera" that may be controlled with the mouse.

glTF文件没有“默认”摄像头。 相反，客户端应用程序必须跟踪当前活动的摄像头。 例如，客户端应用程序可以提供一个下拉菜单，允许用户选择活动摄像头，从而在预定义视图配置之间快速切换。 通过更多的实施工作，客户端应用程序还可以为相机控件定义其自己的相机和交互模式（例如，使用鼠标滚轮进行缩放）。 但是，在这种情况下，导航和交互的逻辑必须仅由客户端应用程序来实现。 [图17a]（gltfTutorial_017_SimpleCameras.md＃cameras-png）显示了这种实现的结果，其中用户可以从glTF文件中定义的相机中选择活动相机，或者可以选择“外部相机” 用鼠标控制。



Previous: [Simple Cameras](gltfTutorial_015_SimpleCameras.md) | [Table of Contents](README.md) | Next: [Simple Morph Target](gltfTutorial_017_SimpleMorphTarget.md)

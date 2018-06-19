Previous: [Advanced Material](gltfTutorial_014_AdvancedMaterial.md) | [Table of Contents](README.md) | Next: [Cameras](gltfTutorial_016_Cameras.md)

# Simple Cameras

The previous sections showed how a basic scene structure with geometric objects is represented in a glTF asset, and how different materials can be applied to these objects. This did not yet include information about the view configuration that should be used for rendering the scene. This view configuration is usually described as a virtual *camera* that is contained in the scene, at a certain position, and pointing in a certain direction.

前面几节介绍了如何在glTF资源中显示具有几何对象的基本场景结构，以及如何将不同材质应用于这些对象。这还没有包含用于渲染场景的视图配置的信息。该视图配置通常被描述为包含在场景中，在某个位置并指向特定方向的虚拟相机。

The following is a simple, complete glTF asset. It is similar to the assets that have already been shown: it defines a simple `scene` containing `node` objects and a single geometric object that is given as a `mesh`, attached to one of the nodes. But this asset additionally contains two [`camera`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-camera) objects:

以下是一个简单，完整的glTF文件。它与之前展示的文件类似：它定义了一个简单的scene，其中包含node对象和连接到其中一个节点的作为网格给出的单个几何对象。但是这个文件还包含两个相机对象：


```javascript
{
  "scenes" : [
    {
      "nodes" : [ 0, 1, 2 ]
    }
  ],
  "nodes" : [
    {
      "rotation" : [ -0.383, 0.0, 0.0, 0.924 ],
      "mesh" : 0
    },
    {
      "translation" : [ 0.5, 0.5, 3.0 ],
      "camera" : 0
    },
    {
      "translation" : [ 0.5, 0.5, 3.0 ],
      "camera" : 1
    }
  ],

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

  "meshes" : [
    {
      "primitives" : [ {
        "attributes" : {
          "POSITION" : 1
        },
        "indices" : 0
      } ]
    }
  ],

  "buffers" : [
    {
      "uri" : "data:application/octet-stream;base64,AAABAAIAAQADAAIAAAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAAAAAAAAAgD8AAAAAAACAPwAAgD8AAAAA",
      "byteLength" : 60
    }
  ],
  "bufferViews" : [
    {
      "buffer" : 0,
      "byteOffset" : 0,
      "byteLength" : 12,
      "target" : 34963
    },
    {
      "buffer" : 0,
      "byteOffset" : 12,
      "byteLength" : 48,
      "target" : 34962
    }
  ],
  "accessors" : [
    {
      "bufferView" : 0,
      "byteOffset" : 0,
      "componentType" : 5123,
      "count" : 6,
      "type" : "SCALAR",
      "max" : [ 3 ],
      "min" : [ 0 ]
    },
    {
      "bufferView" : 1,
      "byteOffset" : 0,
      "componentType" : 5126,
      "count" : 4,
      "type" : "VEC3",
      "max" : [ 1.0, 1.0, 0.0 ],
      "min" : [ 0.0, 0.0, 0.0 ]
    }
  ],

  "asset" : {
    "version" : "2.0"
  }
}
```

The geometry in this asset is a simple unit square. It is rotated by -45 degrees around the x-axis, to emphasize the effect of the different cameras. Image 17a shows three options for rendering this asset. The first examples use the cameras from the asset. The last example shows how the scene looks from an external, user-defined viewpoint.

这个文件中的几何体是一个简单的单位正方形。它围绕x轴旋转-45度，以强调不同相机的效果。图17a显示了渲染这个文件的三个选项。第一个例子使用gltf文件中的相机。最后一个示例显示了如何从外部的用户定义的视点来渲染场景。

<p align="center">
<img src="images/cameras.png" /><br>
<a name="cameras-png"></a>Image 17a: The effect of rendering the scene with different cameras.
</p>


## Camera definitions

The new top-level element of this glTF asset is the `cameras` array, which contains the  [`camera`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-camera) objects:

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

When a camera object has been defined, it may be attached to a `node`. This is accomplished by assigning the index of the camera to the `camera` property of a node. In the given example, two new nodes have been added to the scene graph, one for each camera:

当相机对象已被定义时，它可以被附加到一个节点。这是通过将相机的索引分配给节点的相机属性来完成的。在给定的例子中，两个新节点分别指向一个相机从而添加到场景图中：

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

The differences between perspective and orthographic cameras and their properties, the effect of attaching the cameras to the nodes, and the management of multiple cameras will be explained in detail in the [Cameras](gltfTutorial_018_Cameras.md) section.

透视和正交相机及其属性之间的区别，将相机连接到节点的效果以及多台相机的管理将在[Cameras]部分进行详细说明。




Previous: [Advanced Material](gltfTutorial_014_AdvancedMaterial.md) | [Table of Contents](README.md) | Next: [Cameras](gltfTutorial_016_Cameras.md)

Previous: [Morph Targets](gltfTutorial_018_MorphTargets.md) | [Table of Contents](README.md) | Next: [Skins](gltfTutorial_020_Skins.md)

# A Simple Skin  一个简单的皮肤

glTF supports *vertex skinning*, which allows the geometry (vertices) of a mesh to be deformed based on the pose of a skeleton. This is essential in order to give animated geometry, for example of virtual characters, a realistic appearance. The core for the definition of vertex skinning in a glTF asset is the [`skin`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-skin), but vertex skinning in general implies several interdependencies between the elements of a glTF asset that have been presented so far.

glTF支持顶点蒙皮，这允许网格的几何（顶点）根据骨架的姿态变形。 这对于给动画几何体，例如虚拟人物，一个现实的外观是至关重要的。 glTF文件中定义顶点蒙皮的核心是[`skin`]，但顶点蒙皮通常意味着到目前为止呈现的glTF文件元素之间存在若干相互依赖关系。

The following is a glTF asset that shows basic vertex skinning for a simple geometry. The elements of this asset will be summarized quickly in this section, referring to the previous sections where appropriate, and pointing out the new elements that have been added for the vertex skinning functionality. The details and background information for vertex skinning will be given in the next section.

以下是展示简单几何对象的基本顶点蒙皮的glTF资源。 这部分资源的内容将在本部分中快速总结，并在适当的地方参考前面的部分，并指出为顶点蒙皮功能添加的新元素。 顶点蒙皮的细节和背景信息将在下一节中给出。

```javascript
{
  "scenes" : [ {
    "nodes" : [ 0 ]
  } ],
  
  "nodes" : [ {
    "skin" : 0,
    "mesh" : 0,
    "children" : [ 1 ]
  }, {
    "children" : [ 2 ],
    "translation" : [ 0.0, 1.0, 0.0 ]
  }, {
    "rotation" : [ 0.0, 0.0, 0.0, 1.0 ]
  } ],
  
  "meshes" : [ {
    "primitives" : [ {
      "attributes" : {
        "POSITION" : 1,
        "JOINTS_0" : 2,
        "WEIGHTS_0" : 3
      },
      "indices" : 0
    } ]
  } ],

  "skins" : [ {
    "inverseBindMatrices" : 4,
    "joints" : [ 1, 2 ]
  } ],
  
  "animations" : [ {
    "channels" : [ {
      "sampler" : 0,
      "target" : {
        "node" : 2,
        "path" : "rotation"
      }
    } ],
    "samplers" : [ {
      "input" : 5,
      "interpolation" : "LINEAR",
      "output" : 6
    } ]
  } ],
  
  "buffers" : [ {
    "uri" : "data:application/gltf-buffer;base64,AAABAAMAAAADAAIAAgADAAUAAgAFAAQABAAFAAcABAAHAAYABgAHAAkABgAJAAgAAAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAAAAAAAAAAD8AAAAAAACAPwAAAD8AAAAAAAAAAAAAgD8AAAAAAACAPwAAgD8AAAAAAAAAAAAAwD8AAAAAAACAPwAAwD8AAAAAAAAAAAAAAEAAAAAAAACAPwAAAEAAAAAA",
    "byteLength" : 168
  }, {
    "uri" : "data:application/gltf-buffer;base64,AAABAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAAAAAgD8AAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAABAPwAAgD4AAAAAAAAAAAAAQD8AAIA+AAAAAAAAAAAAAAA/AAAAPwAAAAAAAAAAAAAAPwAAAD8AAAAAAAAAAAAAgD4AAEA/AAAAAAAAAAAAAIA+AABAPwAAAAAAAAAAAAAAAAAAgD8AAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAA=",
    "byteLength" : 320
  }, {
    "uri" : "data:application/gltf-buffer;base64,AACAPwAAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAAAAAAAAgD8AAAAAAAAAvwAAgL8AAAAAAACAPwAAgD8AAAAAAAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAL8AAIC/AAAAAAAAgD8=",
    "byteLength" : 128
  }, {
    "uri" : "data:application/gltf-buffer;base64,AAAAAAAAAD8AAIA/AADAPwAAAEAAACBAAABAQAAAYEAAAIBAAACQQAAAoEAAALBAAAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAkxjEPkSLbD8AAAAAAAAAAPT9ND/0/TQ/AAAAAAAAAAD0/TQ/9P00PwAAAAAAAAAAkxjEPkSLbD8AAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAkxjEvkSLbD8AAAAAAAAAAPT9NL/0/TQ/AAAAAAAAAAD0/TS/9P00PwAAAAAAAAAAkxjEvkSLbD8AAAAAAAAAAAAAAAAAAIA/",
    "byteLength" : 240
  } ],
  
  "bufferViews" : [ {
    "buffer" : 0,
    "byteOffset" : 0,
    "byteLength" : 48,
    "target" : 34963
  }, {
    "buffer" : 0,
    "byteOffset" : 48,
    "byteLength" : 120,
    "target" : 34962
  }, {
    "buffer" : 1,
    "byteOffset" : 0,
    "byteLength" : 320,
    "byteStride" : 16
  }, {
    "buffer" : 2,
    "byteOffset" : 0,
    "byteLength" : 128
  }, {
    "buffer" : 3,
    "byteOffset" : 0,
    "byteLength" : 240
  } ],

  "accessors" : [ {
    "bufferView" : 0,
    "byteOffset" : 0,
    "componentType" : 5123,
    "count" : 24,
    "type" : "SCALAR",
    "max" : [ 9 ],
    "min" : [ 0 ]
  }, {
    "bufferView" : 1,
    "byteOffset" : 0,
    "componentType" : 5126,
    "count" : 10,
    "type" : "VEC3",
    "max" : [ 1.0, 2.0, 0.0 ],
    "min" : [ 0.0, 0.0, 0.0 ]
  }, {
    "bufferView" : 2,
    "byteOffset" : 0,
    "componentType" : 5123,
    "count" : 10,
    "type" : "VEC4",
    "max" : [ 0, 1, 0, 0 ],
    "min" : [ 0, 1, 0, 0 ]
  }, {
    "bufferView" : 2,
    "byteOffset" : 160,
    "componentType" : 5126,
    "count" : 10,
    "type" : "VEC4",
    "max" : [ 1.0, 1.0, 0.0, 0.0 ],
    "min" : [ 0.0, 0.0, 0.0, 0.0 ]
  }, {
    "bufferView" : 3,
    "byteOffset" : 0,
    "componentType" : 5126,
    "count" : 2,
    "type" : "MAT4",
    "max" : [ 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, -0.5, -1.0, 0.0, 1.0 ],
    "min" : [ 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, -0.5, -1.0, 0.0, 1.0 ]
  }, {
    "bufferView" : 4,
    "byteOffset" : 0,
    "componentType" : 5126,
    "count" : 12,
    "type" : "SCALAR",
    "max" : [ 5.5 ],
    "min" : [ 0.0 ]
  }, {
    "bufferView" : 4,
    "byteOffset" : 48,
    "componentType" : 5126,
    "count" : 12,
    "type" : "VEC4",
    "max" : [ 0.0, 0.0, 0.707, 1.0 ],
    "min" : [ 0.0, 0.0, -0.707, 0.707 ]
  } ],
  
  "asset" : {
    "version" : "2.0"
  }
}
```



The result of rendering this asset is shown in Image 19a. 渲染此文件的结果如图19a所示。

<p align="center">
<img src="images/simpleSkin.gif" /><br>
<a name="simpleSkin-gif"></a>Image 19a: A scene with simple vertex skinning.
</p>


## Elements of the simple skin example  简单蒙皮示例的元素

The elements of the given example are briefly summarized here:  展示的例子的元素在这里简要地总结一下：

- The `scenes` and `nodes` elements have been explained in the [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) section. For the vertex skinning, new nodes have been added: the nodes at index 1 and 2 define a new node hierarchy for the *skeleton*. These nodes can be considered the joints between the "bones" that will eventually cause the deformation of the mesh.

“scenes和nodes”在[Scenes and Nodes]部分已经解释了。对于顶点蒙皮，添加了新节点：索引1和索引2处的节点为* skeleton *骨架定义了一个新的节点层次结构。这些节点可以作为最终导致网格变形的“骨骼”之间的关节。
- The new top-level dictionary `skins` contains a single skin in the given example. The properties of this skin object will be explained later.

在给定示例中，“skins”数组包含单个皮肤。这个皮肤对象的属性将在后面解释。
- The concepts of `animations` has been explained in the [Animations](gltfTutorial_007_Animations.md) section. In the given example, the animation refers to the *skeleton* nodes so that the effect of the vertex skinning is actually visible during the animation.

动画的概念已经在[Animations]部分中进行了解释。在给定的例子中，动画引用* skeleton *节点，以便在动画期间顶点蒙皮的效果实际上是可见的。
- The [Meshes](gltfTutorial_009_Meshes.md) section already explained the contents of the `meshes` and `mesh.primitive` objects. In this example, new mesh primitive attributes have been added, which are required for vertex skinning, namely the `"JOINTS_0"` and `"WEIGHTS_0"` attributes.

 [Meshes]部分已经解释了“meshes”和“mesh.primitive”对象的内容。在这个例子中，添加了新的网格基元属性，这些属性是顶点蒙皮所必需的，即“JOINTS_0”和“WEIGHTS_0”属性。
- There are several new `buffers`, `bufferViews`, and `accessors`. Their basic properties have been described in the [Buffers, BufferViews, and Accessors](gltfTutorial_005_BufferBufferViewsAccessors.md) section. In the given example, they contain the additional data required for vertex skinning.

有几个新的`buffers`，`bufferViews`和`accessors`。它们的基本属性已在[Buffers，BufferViews和Accessors]部分中进行了描述。在给定的例子中，它们包含顶点蒙皮所需的附加数据。

Details about how these elements are interconnected to achieve the vertex skinning will be explained in the [Skins](gltfTutorial_020_Skins.md) section.

有关如何将这些元素互连以实现顶点蒙皮的详细信息将在[Skins]部分中进行说明。


Previous: [Morph Targets](gltfTutorial_018_MorphTargets.md) | [Table of Contents](README.md) | Next: [Skins](gltfTutorial_020_Skins.md)

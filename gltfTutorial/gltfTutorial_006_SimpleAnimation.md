Previous: [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) | [Table of Contents](README.md) | Next: [Animations](gltfTutorial_007_Animations.md)


# A Simple Animation 一个简单的动画

As shown in the [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) section, each node can have a local transform. This transform can be given either by the `matrix` property of the node or by using the `translation`, `rotation`, and `scale` (TRS) properties.
<br>如“场景和节点”部分所示，每个节点都可以具有局部变换。 此变换可以通过节点的矩阵属性或通过使用平移，旋转和缩放（TRS）属性来给出

When the transform is given by the TRS properties, an [`animation`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-animation) can be used to describe how the `translation`, `rotation`, or `scale` of a node changes over time.<br>当TRS属性给出变换时，可以使用动画来描述节点的平移，旋转或缩放如何随时间变化

The following is the [minimal glTF file](gltfTutorial_003_MinimalGltfFile.md) that was shown previously, but extended with an animation. This section will explain the changes and extensions that have been made to add this animation.<br>以下是之前显示的最小glTF文件，但是使用动画进行了扩展。 本节将介绍为添加此动画而进行的更改和扩展。


```javascript
{
  "scenes" : [
    {
      "nodes" : [ 0 ]
    }
  ],
  
  "nodes" : [
    {
      "mesh" : 0,
      "rotation" : [ 0.0, 0.0, 0.0, 1.0 ]
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
  
  "animations": [
    {
      "samplers" : [
        {
          "input" : 2,
          "interpolation" : "LINEAR",
          "output" : 3
        }
      ],
      "channels" : [ {
        "sampler" : 0,
        "target" : {
          "node" : 0,
          "path" : "rotation"
        }
      } ]
    }
  ],

  "buffers" : [
    {
      "uri" : "data:application/octet-stream;base64,AAABAAIAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAACAPwAAAAA=",
      "byteLength" : 44
    },
    {
      "uri" : "data:application/octet-stream;base64,AAAAAAAAgD4AAAA/AABAPwAAgD8AAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAD0/TQ/9P00PwAAAAAAAAAAAACAPwAAAAAAAAAAAAAAAPT9ND/0/TS/AAAAAAAAAAAAAAAAAACAPw==",
      "byteLength" : 100
    }
  ],
  "bufferViews" : [
    {
      "buffer" : 0,
      "byteOffset" : 0,
      "byteLength" : 6,
      "target" : 34963
    },
    {
      "buffer" : 0,
      "byteOffset" : 8,
      "byteLength" : 36,
      "target" : 34962
    },
    {
      "buffer" : 1,
      "byteOffset" : 0,
      "byteLength" : 100
    }
  ],
  "accessors" : [
    {
      "bufferView" : 0,
      "byteOffset" : 0,
      "componentType" : 5123,
      "count" : 3,
      "type" : "SCALAR",
      "max" : [ 2 ],
      "min" : [ 0 ]
    },
    {
      "bufferView" : 1,
      "byteOffset" : 0,
      "componentType" : 5126,
      "count" : 3,
      "type" : "VEC3",
      "max" : [ 1.0, 1.0, 0.0 ],
      "min" : [ 0.0, 0.0, 0.0 ]
    },
    {
      "bufferView" : 2,
      "byteOffset" : 0,
      "componentType" : 5126,
      "count" : 5,
      "type" : "SCALAR",
      "max" : [ 1.0 ],
      "min" : [ 0.0 ]
    },
    {
      "bufferView" : 2,
      "byteOffset" : 20,
      "componentType" : 5126,
      "count" : 5,
      "type" : "VEC4",
      "max" : [ 0.0, 0.0, 1.0, 1.0 ],
      "min" : [ 0.0, 0.0, 0.0, -0.707 ]
    }
  ],
  
  "asset" : {
    "version" : "2.0"
  }
  
}
```

<p align="center">
<img src="images/animatedTriangle.gif" /><br>
<a name="animatedTriangle-gif"></a>Image 6a: A single, animated triangle.
</p>


## The `rotation` property of the `node` 节点的旋转属性

The only node in the example now has a `rotation` property. This is an array containing the four floating point values of the [quaternion](https://en.wikipedia.org/wiki/Quaternion) that describes the rotation:  <br>示例中唯一的节点现在具有rotation属性。 这是一个数组，包含描述旋转的四元数的四个浮点值：

```javascript
  "nodes" : [
    {
      "mesh" : 0,
      "rotation" : [ 0.0, 0.0, 0.0, 1.0 ]
    }
  ],
```

The given value is the quaternion describing a "rotation about 0 degrees," so the triangle will be shown in its initial orientation.<br>给定值是描述“大约0度旋转”的四元数，因此三角形将以其初始方向显示


## The animation data 动画数据

Three elements have been added to the top-level arrays of the glTF JSON to encode the animation data:<br>glTF JSON的顶级数组中添加了三个元素来编码动画数据：

- A new `buffer` containing the raw animation data;包含原始动画数据
- A new `bufferView` that refers to the buffer;引用缓冲区buffer
- Two new `accessor` objects that add structural information to the animation data.两个新的访问器对象，用于向动画数据添加结构信息

### The `buffer` and the `bufferView` for the raw animation data  缓冲区和bufferView用于原始动画数据

A new `buffer` has been added, which contains the raw animation data. This buffer also uses a [data URI](gltfTutorial_002_BasicGltfStructure.md#binary-data-in-data-uris) to encode the 100 bytes that the animation data consists of:<br>添加了一个新缓冲区，其中包含原始动画数据。 此缓冲区还使用数据URI对动画数据包含的100个字节进行编码

```javascript
  "buffers" : [
    ...
    {
      "uri" : "data:application/octet-stream;base64,AAAAAAAAgD4AAAA/AABAPwAAgD8AAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAD0/TQ/9P00PwAAAAAAAAAAAACAPwAAAAAAAAAAAAAAAPT9ND/0/TS/AAAAAAAAAAAAAAAAAACAPw==",
      "byteLength" : 100
    }
  ],

  "bufferViews" : [
    ...
    {
      "buffer" : 1,
      "byteOffset" : 0,
      "byteLength" : 100
    }
  ],
```

There is also a new `bufferView`, which here simply refers to the new `buffer` with index 1, which contains the whole animation buffer data. Further structural information is added with the `accessor` objects described below.<br>还有一个新的bufferView，这里简单地引用索引为1的新缓冲区，其中包含整个动画缓冲区数据。使用下面描述的访问者对象添加进一步的结构信息

Note that one could also have appended the animation data to the existing buffer that already contained the geometry data of the triangle. In this case, the new buffer view would have referred to the `buffer` with index 0, and used an appropriate `byteOffset` to refer to the part of the buffer that then contained the animation data.<br>请注意，还可以将动画数据附加到已包含三角形几何数据的现有缓冲区。在这种情况下，新缓冲区视图将引用索引为0的缓冲区，并使用适当的byteOffset来引用随后包含动画数据的缓冲区部分

In the example that is shown here, the animation data is added as a new buffer to keep the geometry data and the animation data separated.<br>在此处显示的示例中，动画数据被添加为新缓冲区，以保持几何数据和动画数据分离


### The `accessor` objects for the animation data  访问者对象为动画数据

Two new `accessor` objects have been added, which describe how to interpret the animation data. The first accessor describes the *times* of the animation key frames. There are five elements (as indicated by the `count` of 5), and each one is a scalar `float` value (which is 20 bytes in total). The second accessor says that after the first 20 bytes, there are five elements, each being a 4D vector with `float` components. These are the *rotations* that correspond to the five key frames of the animation, given as quaternions.<br>添加了两个新的访问者对象，描述了如何解释动画数据。第一个访问器描述动画关键帧的时间。有五个元素（由5的计数表示），每个元素都是一个标量浮点值（总共20个字节）。第二个访问器表示在前20个字节之后，有五个元素，每个元素都是一个带浮点组件的4D向量。这些是与动画的五个关键帧对应的旋转，以四元数形式给出

```javascript
  "accessors" : [
    ...
    {
      "bufferView" : 2,
      "byteOffset" : 0,
      "componentType" : 5126,
      "count" : 5,
      "type" : "SCALAR",
      "max" : [ 1.0 ],
      "min" : [ 0.0 ]
    },
    {
      "bufferView" : 2,
      "byteOffset" : 20,
      "componentType" : 5126,
      "count" : 5,
      "type" : "VEC4",
      "max" : [ 0.0, 0.0, 1.0, 1.0 ],
      "min" : [ 0.0, 0.0, 0.0, -0.707 ]
    }
  ],

```

The actual data that is provided by the *times* accessor and the *rotations* accessor, using the data from the buffer in the example, is shown in this table:<br>时间访问器和旋转访问器使用示例中缓冲区中的数据提供的实际数据如下表所示

|*times* accessor |*rotations* accessor|Meaning|
|---|---|---|
|0.0| (0.0, 0.0, 0.0, 1.0 )| At 0.0 seconds, the triangle has a rotation of 0 degrees |
|0.25| (0.0, 0.0, 0.707, 0.707)| At 0.25 seconds, it has a rotation of 90 degrees around the z-axis
|0.5| (0.0, 0.0, 1.0, 0.0)|  At 0.5 seconds, it has a rotation of 180 degrees around the z-axis |
|0.75| (0.0, 0.0, 0.707, -0.707)| At 0.75 seconds, it has a rotation of 270 (= -90) degrees around the z-axis |
|1.0| (0.0, 0.0, 0.0, 1.0)| At 1.0 seconds, it has a rotation of 360 (= 0) degrees around the z-axis |

So this animation describes a rotation of 360 degrees around the z-axis that lasts 1 second.<br>因此，此动画描述了围绕z轴旋转360度，持续1秒


## The `animation`   动画

Finally, this is the part where the actual animation is added. The top-level `animations` array contains a single `animation` object. It consists of two elements:<br>最后，这是添加实际动画的部分。顶级动画数组包含单个动画对象。它由两个元素组成

- The `samplers`, which describe the sources of animation data;  <br>采样器，描述动画数据的来源<br>
- The `channels`, which can be imagined as connecting a "source" of the animation data to a "target."  <br>通道可以被想象为将动画数据的“源”连接到“目标”

In the given example, there is one sampler. Each sampler defines an `input` and an `output` property. They both refer to accessor objects. Here, these are the *times* accessor (with index 2) and the *rotations* accessor (with index 3) that have been described above. Additionally, the sampler defines an `interpolation` type, which is `"LINEAR"` in this example.

There is also one `channel` in the example. This channel refers to the only sampler (with index 0) as the source of the animation data. The target of the animation is encoded in the `channel.target` object: it contains an `id` that refers to the node whose property should be animated. The actual node property is named in the `path`. So the channel target in the given example says that the `"rotation"` property of the node with index 0 should be animated.


```javascript
  "animations": [
    {
      "samplers" : [
        {
          "input" : 2,
          "interpolation" : "LINEAR",
          "output" : 3
        }
      ],
      "channels" : [ {
        "sampler" : 0,
        "target" : {
          "node" : 0,
          "path" : "rotation"
        }
      } ]
    }
  ],
```

Combining all this information, the given animation object says the following:

> During the animation, the animated values are obtained from the *rotations* accessor. They are interpolated linearly, based on the current simulation time and the key frame times that are provided by the *times* accessor. The interpolated values are then written into the `"rotation"` property of the node with index 0.

A more detailed description and actual examples for the interpolation and the computations that are involved here can be found in the [Animations](gltfTutorial_007_Animations.md) section.



Previous: [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) | [Table of Contents](README.md) | Next: [Animations](gltfTutorial_007_Animations.md)

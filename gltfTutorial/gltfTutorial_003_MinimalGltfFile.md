Previous: [Basic glTF Structure](gltfTutorial_002_BasicGltfStructure.md) | [Table of Contents](README.md) | Next: [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md)


# A Minimal glTF File  最小的glTF文件

The following is a minimal but complete glTF asset, containing a single, indexed triangle. You can copy and paste it into a `gltf` file, and every glTF-based application should be able to load and render it. This section will explain the basic concepts of glTF based on this example.

以下是一个简单但完整的glTF文件，包含一个单一的索引三角形。你可以复制并粘贴到`gltf`文件中，每个基于glTF的应用程序应该能够加载和渲染它。本节将根据此示例解释glTF的基本概念。

```javascript
{
  "scenes" : [
    {
      "nodes" : [ 0 ]
    }
  ],
  
  "nodes" : [
    {
      "mesh" : 0
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
      "uri" : "data:application/octet-stream;base64,AAABAAIAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAACAPwAAAAA=",
      "byteLength" : 44
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
    }
  ],
  
  "asset" : {
    "version" : "2.0"
  }
}
```

<p align="center">
<img src="images/triangle.png" /><br>
<a name="triangle-png"></a>Image 3a: A single triangle.
</p>


## The `scene` and `nodes` structure “场景”和“节点”结构

The [`scene`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-scene) is the entry point for the description of the scene that is stored in the glTF. When parsing a glTF JSON file, the traversal of the scene structure will start here. Each scene contains an array called `nodes`, which contains the indices of [`node`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-node) objects. These nodes are the root nodes of a scene graph hierarchy.

[`scene`]是存储在glTF中的场景描述的入口点。解析glTF JSON文件时，场景结构的遍历将从这里开始。每个场景都包含一个名为`nodes`的数组，其中包含[`node`]对象的索引。这些节点是场景图层次结构的根节点。

The example here consists of a single scene. It refers to the only node in this example, which is the node with the index 0. This node, in turn, refers to the only mesh, which has the index 0:

这里的例子由一个场景组成。它指的是本例中唯一的节点，即具有索引0的节点。该节点又指唯一具有索引0的网格：


```javascript
  "scenes" : [
    {
      "nodes" : [ 0 ]
    }
  ],
  
  "nodes" : [
    {
      "mesh" : 0
    }
  ],
```

More details about scenes and nodes and their properties will be given in the [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) section.

有关场景和节点及其属性的更多详细信息将在[场景和节点]部分中给出


## The `meshes`

A [`mesh`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-mesh) represents an actual geometric object that appears in the scene. The mesh itself usually does not have any properties, but only contains an array of [`mesh.primitive`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-primitive) objects, which serve as building blocks for larger models. Each mesh primitive contains a description of the geometry data that the mesh consists of.

[`mesh`]表示出现在场景中的实际几何对象。网格本身通常没有任何属性，但只包含一个[`mesh.primitive`]对象的数组， 作为大型模型的构建模块。每个网格基元包含网格组成的几何数据的描述。

The example consists of a single mesh, and has a single `mesh.primitive` object. The mesh primitive has an array of `attributes`. These are the attributes of the vertices of the mesh geometry, and in this case, this is only the `POSITION` attribute, describing the positions of the vertices. The mesh primitive describes an *indexed* geometry, which is indicated by the `indices` property. By default, it is assumed to describe a set of triangles, so that three consecutive indices are the indices of the vertices of one triangle.

这个例子由单个网格组成，并且有一个`mesh.primitive`对象。网格基元有一个“attributes”数组。这些是网格几何体顶点的属性，在当前例子中，仅仅包含了描述顶点位置的“POSITION”属性。网格基元描述一个* indexed *几何，它由'indices`属性表示。默认情况下，假定它描述一组三角形，因此三个连续的索引是一个三角形顶点的索引。

The actual geometry data of the mesh primitive is given by the `attributes` and the `indices`. These both refer to `accessor` objects, which will be explained below.

mesh基元的实际几何数据由`attributes`和`indices`给出。这两个都指的是`accessor`对象，这将在下面解释。

```javascript
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
```

A more detailed description of meshes and mesh primitives can be found in the [meshes](gltfTutorial_009_Meshes.md) section.

网格和网格基元的更详细的描述可以在[mesh]部分找到。


## The `buffer`, `bufferView`, and `accessor` concepts

The `buffer`, `bufferView`, and `accessor` objects provide information about the geometry data that the mesh primitives consist of. They are introduced here quickly, based on the specific example. A more detailed description of these concepts will be given in the [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) section.

`buffer`，`bufferView`和`accessor`对象提供了有关网格基元组成的几何数据的信息。基于具体的例子，他们很快就会在这里介绍。这些概念的更详细的描述将在[Buffers，BufferViews和Accessors]部分给出。

### Buffers

A [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) defines a block of raw, unstructured data with no inherent meaning. It contains an `uri`, which can either point to an external file that contains the data, or it can be a [data URI](gltfTutorial_002_BasicGltfStructure.md#binary-data-in-data-uris) that encodes the binary data directly in the JSON file.

[`buffer`]定义了一块原始的，非结构化的数据，没有固有的含义。它包含一个`uri`，它可以指向包含数据的外部文件，也可以指向直接对二进制数据进行编码的[数据URI]JSON文件。

In the example file, the second approach is used: there is a single buffer, containing 44 bytes, and the data of a this buffer is encoded as a data URI:

在示例文件中，使用第二种方法：有一个缓冲区，包含44个字节，并且此缓冲区的数据被编码为数据URI：

```javascript
  "buffers" : [
    {
      "uri" : "data:application/octet-stream;base64,AAABAAIAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAACAPwAAAAA=",
      "byteLength" : 44
    }
  ],
```

This data contains the indices of the triangle, and the vertex positions of the triangle. But in order to actually use this data as the geometry data of a mesh primitive, additional information about the *structure* of this data is required. This information about the structure is encoded in the `bufferView` and `accessor` objects.

这些数据包含三角形的索引和三角形的顶点位置信息。但为了将这些数据实际用作网格基元的几何数据，需要有关此数据的*structure*的附加信息。这个关于结构的信息被编码到`bufferView`和`accessor`对象中。

### Buffer views

A [`bufferView`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-bufferView) describes a "chunk" or a "slice" of the whole, raw buffer data. In the given example, there are two buffer views. They both refer to the same buffer. The first buffer view refers to the part of the buffer that contains the data of the indices: it has a `byteOffset` of 0 referring to the whole buffer data, and a `byteLength` of 6. The second buffer view refers to the part of the buffer that contains the vertex positions. It starts at a `byteOffset` of 8, and has a `byteLength` of 36; that is, it extends to the end of the whole buffer.

[`bufferView`]描述了整个原始缓冲区数据的“块”或“切片”。在给出的例子中，有两个缓冲区视图。它们都指向相同的缓冲区。第一个缓冲区视图指的是包含索引数据的缓冲区部分：它包含参数"byteOffset" : 0,以及"byteLength" : 6。第二个缓冲区视图指的是部分包含顶点位置的缓冲区。它从"byteOffset" : 8,开始，具有"byteLength" : 36,36个字节长度。也就是说，它延伸到整个缓冲区的末尾。

```javascript
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
    }
  ],
```


### Accessors

The second step of structuring the data is accomplished with [`accessor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-accessor) objects. They define how the data of a `bufferView` has to be interpreted by providing information about the data types and the layout.

构建数据的第二步是使用[`accessor`]对象完成的。他们定义了如何通过数据类型和布局的信息来解释“bufferView”的数据。

In the example, there are two accessor objects.在这个例子中，有两个accessor对象。

The first accessor describes the indices of the geometry data. It refers to the `bufferView` with index 0, which is the part of the `buffer` that contains the raw data for the indices. Additionally, it specifies the `count` and `type` of the elements and their `componentType`. In this case, there are 3 scalar elements, and their component type is given by a constant that stands for the `unsigned short` type.

第一个访问器描述几何数据的索引。它引用索引为0的`bufferView`，它是包含原始索引数据的`buffer`的一部分。此外，它还指定元素的“count”和“type”及其“componentType”。在这种情况下，有3个标量元素，它们的组件类型由一个表示`unsigned short`类型的常量给出。

The second accessor describes the vertex positions. It contains a reference to the relevant part of the buffer data, via the `bufferView` with index 1, and its `count`, `type`, and `componentType` properties say that there are three elements of 3D vectors, each having `float` components.

第二个访问器描述顶点位置。它包含一个对缓冲区数据的相关部分的引用，通过索引为1的“bufferView”，它的count，type和componentType属性表示有三个3D矢量元素，每个元素都有`浮动“组件。


```javascript
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
    }
  ],
```

As described above, a `mesh.primitive` may now refer to these accessors, using their indices:

如上所述，`mesh.primitive`现在可以使用它们的索引来引用这些访问器：

```javascript
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
```

When this `mesh.primitive` has to be rendered, the renderer can resolve the underlying buffer views and buffers and will send the required parts of the buffer to the renderer, together with the information about the data types and layout. A more detailed description of how the accessor data is obtained and processed by the renderer is given in the [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) section and the [Materials and Techniques](gltfTutorial_013_MaterialsTechniques.md) section.

当这个`mesh.primitive`必须被渲染时，渲染器可以解析底层的缓冲区视图和缓冲区，并将所需的缓冲区部分连同关于数据类型和布局的信息一起发送到渲染器。 [缓冲区，缓冲区视图和访问器]一节和[材料和技术]一节部分给出了渲染器如何获取和处理访问器数据的更详细说明。




## The `asset` description

In glTF 1.0, this property is still optional, but in subsequent glTF versions, the JSON file is required to contain an `asset` property that contains the `version` number. The example here says that the asset complies to glTF version 2.0:

在glTF 1.0中，该属性仍然是可选的，但在随后的glTF版本中，JSON文件必须包含`version`编号的`asset`属性。这里的例子说这个文件符合glTF 2.0标准：

```javascript
  "asset" : {
    "version" : "2.0"
  }
```

The `asset` property may contain additional metadata that is described in the [`asset` specification](https://github.com/KhronosGroup/glTF/blob/master/specification/README.md#reference-asset).




Previous: [Basic glTF Structure](gltfTutorial_002_BasicGltfStructure.md) | [Table of Contents](README.md) | Next: [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md)

Previous: [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) | [Table of Contents](README.md) | Next: [Simple Animation](gltfTutorial_006_SimpleAnimation.md)


# Buffers, BufferViews, and Accessors

An example of `buffer`, `bufferView`, and `accessor` objects was already given in the [Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md) section. This section will explain these concepts in more detail.

`buffer`, `bufferView`, 和 `accessor`对象的示例已经在[Minimal glTF File]文件部分中给出。本节将更详细地解释这些概念。

## Buffers

A [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) represents a block of raw binary data, without an inherent structure or meaning. This data is referred to by a buffer using its `uri`. This URI may either point to an external file, or be a [data URI](gltfTutorial_002_BasicGltfStructure.md#binary-data-in-buffers) that encodes the binary data directly in the JSON file. The [minimal glTF file](gltfTutorial_003_MinimalGltfFile.md) contained an example of a `buffer`, with 44 bytes of data, encoded in a data URI:

[`buffer`]代表原始二进制数据块，没有固有的结构或含义。这个数据被一个缓冲区使用它的uri来引用。该URI可以指向外部文件，也可以是直接在JSON文件中编码二进制数据的[data URI]。[minimal glTF file]文件包含一个buffer的例子，它有44个字节的数据，用数据URI编码：

```javascript
  "buffers" : [
    {
      "uri" : "data:application/octet-stream;base64,AAABAAIAAAAAAAAAAAAAAAAAAAAAAIA/AAAAAAAAAAAAAAAAAACAPwAAAAA=",
      "byteLength" : 44
    }
  ],
```


<p align="center">
<img src="images/buffer.png" /><br>
<a name="buffer-png"></a>Image 5a: The buffer data, consisting of 44 bytes.
</p>

Parts of the data of a `buffer` may have to be passed to the renderer as vertex attributes, or as indices, or the data may contain skinning information or animation key frames. In order to be able to use this data, additional information about the structure and type of this data is required.

buffer数据可能作为顶点属性或索引传递给渲染器，或者可能包含蒙皮信息或动画关键帧信息。为了能够使用这些数据，需要有关这些数据的结构和类型的附加信息。


## BufferViews

The first step of structuring the data from a `buffer` is with [`bufferView`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-bufferView) objects. A `bufferView` represents a "slice" of the data of one buffer. This slice is defined using an offset and a length, in bytes. The [minimal glTF file](gltfTutorial_003_MinimalGltfFile.md) defined two `bufferView` objects:

构造来自buffer的数据的第一步是使用bufferView对象。一个bufferView表示一个缓冲区数据的“切片”。该片段使用偏移量和长度（以字节为单位）进行定义。[minimal glTF file]文件定义了两个bufferView对象：

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

The first `bufferView` refers to the first 6 bytes of the buffer data. The second one refers to 36 bytes of the buffer, with an offset of 8 bytes, as shown in this image:

第一个bufferView指的是缓冲区数据的前6个字节。第二个引用缓冲区的36个字节，偏移量为8个字节，如图所示：

<p align="center">
<img src="images/bufferBufferView.png" /><br>
<a name="bufferBufferView-png"></a>Image 5b: The buffer views, referring to parts of the buffer.
</p>

The bytes that are shown in light gray are padding bytes that are required for properly aligning the accessors, as described below. 

以浅灰色显示的字节是正确对齐访问器所需的填充字节，如下所述。

Each `bufferView` additionally contains a `target` property. This property may later be used by the renderer to classify the type or nature of the data that the buffer view refers to. The `target` can be a constant indicating that the data is used for vertex attributes (`34962`, standing for `ARRAY_BUFFER`), or that the data is used for vertex indices (`34963`, standing for `ELEMENT_ARRAY_BUFFER`).

每个bufferView还包含一个目标属性。渲染器可以稍后使用此属性来分类缓冲器视图引用的数据的类型或性质。target可以是一个常量，表示数据用于顶点属性（34962，代表ARRAY_BUFFER），或者数据用于顶点索引（34963，代表ELEMENT_ARRAY_BUFFER）。

At this point, the `buffer` data has been divided into multiple parts, and each part is described by one `bufferView`. But in order to really use this data in a renderer, additional information about the type and layout of the data is required.

此时，buffer数据被分成多个部分，每个部分由一个bufferView描述。但为了真正在渲染器中使用这些数据，需要有关数据类型和布局的附加信息。


## Accessors

An [`accessor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-accessor) object refers to a `bufferView` and contains properties that define the type and layout of the data of this `bufferView`.

[`accessor`]对象引用一个bufferView，并包含定义此bufferView数据类型和布局的属性。

### Data type

The type of an accessor's data is encoded in the `type` and the `componentType` properties. The value of the `type` property is a string that specifies whether the data elements are scalars, vectors, or matrices. For example, the value may be `"SCALAR"` for scalar values, `"VEC3"` for 3D vectors, or `"MAT4"` for 4&times;4 matrices.

访问者数据的类型用type和componentType属性进行编码。 type属性的值是一个字符串，用于指定数据元素是标量，矢量还是矩阵。例如，该值可以是标量值的“SCALAR”，3D矢量的“VEC3”或4×4矩阵的“MAT4”。

The `componentType` specifies the type of the components of these data elements. This is a GL constant that may, for example, be `5126` (`FLOAT`) or `5123` (`UNSIGNED_SHORT`), to indicate that the elements have `float` or `unsigned short` components, respectively.

componentType指定这些数据元素的组件类型。这是一个GL常量，例如，它可以是5126（FLOAT）或5123（UNSIGNED_SHORT），分别表示元素的值是浮点型或无符号短整型。

Different combinations of these properties may be used to describe arbitrary data types. For example, the [minimal glTF file](gltfTutorial_003_MinimalGltfFile.md) contained two accessors:

这些属性的不同组合可以用来描述任意的数据类型。例如，[minimal glTF file]文件包含两个访问器：

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

The first accessor refers to the `bufferView` with index 0, which defines the part of the `buffer` data that contains the indices. Its `type` is `"SCALAR"`, and its `componentType` is `5123` (`UNSIGNED_SHORT`). This means that the indices are stored as scalar `unsigned short` values.

第一个访问器引用索引为0的bufferView，它定义包含索引的buffer缓冲区数据部分。它的type是“SCALAR”，它的componentType是5123（UNSIGNED_SHORT）。这意味着索引值是无符号短整型的标量。

The second accessor refers to the `bufferView` with index 1, which defines the part of the `buffer` data that contains the vertex attributes - particularly, the vertex positions. Its `type` is `"VEC3"`, and its `componentType` is  `5126` (`FLOAT`). So this accessor describes 3D vectors with floating point components.

第二个访问器引用索引为1的bufferView，它定义包含顶点属性的buffer缓冲区数据部分 - 特别是顶点位置。它的type是“VEC3”，其componentType是5126（FLOAT）。所以这个访问器描述了带有浮点组件的3D向量。


### Data layout  数据布局

Additional properties of an `accessor` further specify the layout of the data. The `count` property of an accessor indicates how many data elements it consists of. In the example above, the count has been `3` for both accessors, standing for the three indices and the three vertices of the triangle, respectively. Each accessor also has a `byteOffset` property. For the example above, it has been `0` for both accessors, because there was only one `accessor` for each `bufferView`. But when multiple accessors refer to the same `bufferView`, then the `byteOffset` describes where the data of the accessor starts, relative to the `bufferView` that it refers to.

`accessor`的其他属性进一步指定数据的布局。访问器的`count`属性表示它由多少个数据元素组成。在上面的例子中，两个访问器的count均为3，分别代表三个索引和三角形的三个顶点。每个访问器也有一个`byteOffset`属性。对于上面的例子，由于每个`accessor`引用不同的`bufferView`，所以两个访问器的`byteOffset`都是'0'。但是，当多个访问者引用相同的`bufferView`时，`byteOffset`描述访问者的数据开始的位置，相对于它引用的`bufferView`。


### Data alignment 数据对齐

The data that is referred to by an `accessor` may be sent to the graphics card for rendering, or be used at the host side as animation or skinning data. Therefore, the data of an `accessor` has to be aligned based on the *type* of the data. For example, when the `componentType` of an `accessor` is `5126` (`FLOAT`), then the data must be aligned at 4-byte boundaries, because a single `float` value consists of four bytes. This alignment requirement of an `accessor` refers to its `bufferView` and the underlying `buffer`. Particularly, the alignment requirements are as follows:

`accessor`引用的数据可以发送到显卡进行渲染，或者在主机端用作动画或蒙皮数据。因此，'accessor'的数据必须根据数据的*type*进行对齐。例如，当`accessor`的`componentType`是`5126`（`FLOAT`）时，那么数据必须在4字节的边界对齐，因为一个`float'值由4个字节组成。 `accessor`的这种对齐要求是指它的`bufferView`和底层的`buffer`。对齐要求如下：

- The `byteOffset` of an `accessor` must be divisible by the size of its `componentType`.

一个`accessor`的`byteOffset`必须被它的`componentType`的大小整除
- The sum of the `byteOffset` of an accessor and the `byteOffset` of the `bufferView` that it refers to must be divisible by the size of its `componentType`.

访问者的'byteOffset'和它引用的'bufferView`的byteOffset之和必须可以被其“componentType”的大小整除。

In the example above, the `byteOffset` of the `bufferView` with index 1 (which refers to the vertex attributes) was chosen to be `8`, in order to align the data of the accessor for the vertex positions to 4-byte boundaries. The bytes `6` and `7` of the `buffer` are thus *padding* bytes that do not carry relevant data. 

在上面的例子中，为了将顶点位置的访问器的数据与4字节边界，索引为1（指向顶点属性）的'bufferView'的'byteOffset'的属性值设置为'8'。因此，“buffer”中的“6”和“7”字节不包含相关数据。

Image 5c illustrates how the raw data of a `buffer` is structured using `bufferView` objects and is augmented with data type information using `accessor` objects.

图5c说明了如何使用`bufferView`对象构造`buffer`的原始数据，并使用`accessor`对象增加了数据类型信息。

<p align="center">
<img src="images/bufferBufferViewAccessor.png" /><br>
<a name="bufferBufferViewAccessor-png"></a>Image 5c: The accessors defining how to interpret the data of the buffer views.
</p>


### Data interleaving 数据交错

The data of the attributes that are stored in a single `bufferView` may be stored as an *Array-Of-Structures*. A single `bufferView` may, for example, contain the data for vertex positions and for vertex normals in an interleaved fashion. In this case, the `byteOffset` of an accessor defines the start of the first relevant data element for the respective attribute, and the `bufferView` defines an additional `byteStride` property. This is the number of bytes between the start of one element of its accessors, and the start of the next one. An example of how interleaved position and normal attributes are stored inside a `bufferView` is shown in Image 5d.

存储在单个“bufferView”中的属性数据可以存储为*Array-Of-Structures*。例如，单个`bufferView`可以以交错的方式包含顶点位置和顶点法线的数据。在这种情况下，访问器的`byteOffset`定义了相应属性的第一个相关数据元素的开始，`bufferView`定义了一个额外的`byteStride`属性。这是它的访问器的一个元素的开始和下一个元素的开始之间的字节数。图5d显示了一个如何在“bufferView”内存储交错位置和常规属性的例子。

<p align="center">
<img src="images/aos.png" /><br>
<a name="aos-png"></a>Image 5d: Interleaved acessors in one buffer view.
</p>


### Data contents 数据内容

An `accessor` also contains `min` and `max` properties that summarize the contents of their data. They are the component-wise minimum and maximum values of all data elements contained in the accessor. In the case of vertex positions, the `min` and `max` properties thus define the *bounding box* of an object. This can be useful for prioritizing downloads, or for visibility detection. In general, this information is also useful for storing and processing *quantized* data that is dequantized at runtime, by the renderer, but details of this quantization are beyond the scope of this tutorial.

`accessor`还包含汇总其数据内容的`min`和`max`属性。它们是访问器中包含的所有数据元素的组件方式最小值和最大值。在顶点位置的情况下，`min`和`max`属性因此定义了对象的*bounding box（包围盒）*。这对于预加载或可视性检测非常有用。通常，这些信息对于存储和处理由渲染器在运行时反量化的量化数据也很有用，但这种量化的细节超出了本教程的范围。



## Sparse accessors 稀疏访问器


With version 2.0, the concept of *sparse accessors* was introduced in glTF. This is a special representation of data that allows very compact storage of multiple data blocks that have only a few different entries. For example, when there is geometry data that contains vertex positions, this geometry data may be used for multiple objects. This may be achieved by referring to the same `accessor` from both objects. If the vertex positions for both objects are mostly the same and differ for only a few vertices, then it is not necessary to store the whole geometry data twice. Instead, it is possible to store the data only once, and use a sparse accessor to store only the vertex positions that differ for the second object. 

随着2.0版本，*sparse accessors*的概念在glTF中引入。这是数据的一种特殊表示形式，它可以非常紧凑地存储只有几个不同条目的多个数据块。例如，当存在包含顶点位置的几何数据时，该几何数据可用于多个对象。这可以通过引用来自两个对象的相同“访问者”来实现。如果两个对象的顶点位置大部分相同，并且只有几个顶点不同，则不需要将整个几何数据存储两次。相反，可以只存储一次数据，并使用一个稀疏存取器来存储只有第二个对象不同的顶点位置。

The following is a complete glTF asset, in embedded representation, that shows an example of sparse accessors:

```javascript
{
  "scenes" : [ {
    "nodes" : [ 0 ]
  } ],
  
  "nodes" : [ {
    "mesh" : 0
  } ],
  
  "meshes" : [ {
    "primitives" : [ {
      "attributes" : {
        "POSITION" : 1
      },
      "indices" : 0
    } ]
  } ],
  
  "buffers" : [ {
    "uri" : "data:application/gltf-buffer;base64,AAAIAAcAAAABAAgAAQAJAAgAAQACAAkAAgAKAAkAAgADAAoAAwALAAoAAwAEAAsABAAMAAsABAAFAAwABQANAAwABQAGAA0AAAAAAAAAAAAAAAAAAACAPwAAAAAAAAAAAAAAQAAAAAAAAAAAAABAQAAAAAAAAAAAAACAQAAAAAAAAAAAAACgQAAAAAAAAAAAAADAQAAAAAAAAAAAAAAAAAAAgD8AAAAAAACAPwAAgD8AAAAAAAAAQAAAgD8AAAAAAABAQAAAgD8AAAAAAACAQAAAgD8AAAAAAACgQAAAgD8AAAAAAADAQAAAgD8AAAAACAAKAAwAAAAAAIA/AAAAQAAAAAAAAEBAAABAQAAAAAAAAKBAAACAQAAAAAA=",
    "byteLength" : 284
  } ],
  
  "bufferViews" : [ {
    "buffer" : 0,
    "byteOffset" : 0,
    "byteLength" : 72,
    "target" : 34963
  }, {
    "buffer" : 0,
    "byteOffset" : 72,
    "byteLength" : 168
  }, {
    "buffer" : 0,
    "byteOffset" : 240,
    "byteLength" : 6
  }, {
    "buffer" : 0,
    "byteOffset" : 248,
    "byteLength" : 36
  } ],
  
  "accessors" : [ {
    "bufferView" : 0,
    "byteOffset" : 0,
    "componentType" : 5123,
    "count" : 36,
    "type" : "SCALAR",
    "max" : [ 13 ],
    "min" : [ 0 ]
  }, {
    "bufferView" : 1,
    "byteOffset" : 0,
    "componentType" : 5126,
    "count" : 14,
    "type" : "VEC3",
    "max" : [ 6.0, 4.0, 0.0 ],
    "min" : [ 0.0, 0.0, 0.0 ],
    "sparse" : {
      "count" : 3,
      "indices" : {
        "bufferView" : 2,
        "byteOffset" : 0,
        "componentType" : 5123
      },
      "values" : {
        "bufferView" : 3,
        "byteOffset" : 0
      }
    }
  } ],
  
  "asset" : {
    "version" : "2.0"
  }
}
```

The result of rendering this asset is shown in Image 5e:

<p align="center">
<img src="images/simpleSparseAccessor.png" /><br>
<a name="simpleSparseAccessor-png"></a>Image 5e: The result of rendering the simple sparse accessor asset.
</p>

The example contains two accessors: one for the indices of the mesh, and one for the vertex positions. The one that refers to the vertex positions defines an additional `accessor.sparse` property, which contains the information about the sparse data substitution that should be applied:

该示例包含两个访问器：一个用于网格的索引，另一个用于顶点位置。引用顶点位置的accessor定义了一个额外的`accessor.sparse`属性，其中包含了需要使用的稀疏数据信息：


```javascript
  "accessors" : [ 
  ...
  {
    "bufferView" : 1,
    "byteOffset" : 0,
    "componentType" : 5126,
    "count" : 14,
    "type" : "VEC3",
    "max" : [ 6.0, 4.0, 0.0 ],
    "min" : [ 0.0, 0.0, 0.0 ],
    "sparse" : {
      "count" : 3,
      "indices" : {
        "bufferView" : 2,
        "byteOffset" : 0,
        "componentType" : 5123
      },
      "values" : {
        "bufferView" : 3,
        "byteOffset" : 0
      }
    }
  } ],
```

This `sparse` object itself defines the `count` of elements that will be affected by the substitution. The `sparse.indices` property refers to a `bufferView` that contains the indices of the elements which will be replaced. The `sparse.values` refers to a `bufferView` that contains the actual data. 

这个`稀疏'对象定义了将被替换的元素的“数量”。 `sparse.indices`属性指的是一个`bufferView`，它包含了将被替换的元素的索引。 `sparse.values`指的是一个包含实际数据的`bufferView`。

In the example, the original geometry data is stored in the `bufferView` with index 1. It describes a rectangular array of vertices. The `sparse.indices` refer to the `bufferView` with index 2, which contains the indices `[8, 10, 12]`. The `sparse.values` refers to the `bufferView` with index 3, which contains new vertex positions, namely, `[(1,2,0), (3,3,0), (5,4,0)]`. The effect of applying the corresponding substitution is shown in Image 5f.

在这个例子中，原始几何数据存储在索引为1的`bufferView`中。它描述了一个矩形的顶点数组。 `sparse.indices`引用索引为2的`bufferView`，它包含索引`[8,10,12]`。 'sparse.values'是指索引为3的`bufferView`，它包含新的顶点位置，即'[（1,2,0），（3,3,0），（5,4,0）] `。图5f显示了应用相应替换的效果。


<p align="center">
<img src="images/simpleSparseAccessorDescription.png" /><br>
<a name="simpleSparseAccessorDescription-png"></a>Image 5f: The substitution that is done with the sparse accessor.
</p>





Previous: [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) | [Table of Contents](README.md) | Next: [Simple Animation](gltfTutorial_006_SimpleAnimation.md)

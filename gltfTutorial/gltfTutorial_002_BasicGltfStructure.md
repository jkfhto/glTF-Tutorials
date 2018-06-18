Previous: [Introduction](gltfTutorial_001_Introduction.md) | [Table of Contents](README.md) | Next: [A Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md)


# The Basic Structure of glTF  glTF的基本结构

The core of glTF is a JSON file. This file describes the whole contents of the 3D scene. It consists of a description of the scene structure itself, which is given by a hierarchy of nodes that define a scene graph. The 3D objects that appear in the scene are defined using meshes that are attached to the nodes. Materials define the appearance of the objects. Animations describe how the 3D objects are transformed (e.g., rotated to translated) over time, and skins define how the geometry of the objects is deformed based on a skeleton pose. Cameras describe the view configuration for the renderer.

glTF的核心是一个JSON文件。该文件描述了3D场景的全部内容。它由场景结构本身的描述组成，由定义场景图的节点层次结构给出。出现在场景中的3D对象使用连接到节点的网格来定义。材料定义了对象的外观。动画描述3D对象如何随时间变换（例如，旋转平移），并且皮肤基于骨架姿态定义对象的几何形状如何变形。相机描述了渲染器的视图配置。

## The JSON structure  JSON结构

The scene objects are stored in arrays in the JSON file. They can be accessed using the index of the respective object in the array:

场景对象存储在JSON文件中的数组中。可以使用数组中相应对象的索引来访问它们：

```javascript
"meshes" : 
[
    { ... }
    { ... }
    ...
],
```

These indices are also used to define the *relationships* between the objects. The example above defines multiple meshes, and a node may refer to one of these meshes, using the mesh index, to indicate that the mesh should be attached to this node:

这些索引也用于定义对象之间的*关系*。上面的例子定义了多个网格，并且节点可以使用网格索引来引用这些网格中的一个，以指示网格应该附加到该节点：

```javascript
"nodes:" 
[
    { "mesh": 0, ... },
    { "mesh": 5, ... },
    ...
}
```

The following image (adapted from the [glTF concepts section](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#concepts)) gives an overview of the top-level elements of the JSON part of a glTF asset:

下面的图片概述了glTF中JSON的结构组成：

<p align="center">
<img src="images/gltfJsonStructure.png" /><br>
<a name="gltfJsonStructure-png"></a>Image 2a: The glTF JSON structure
</p>


These elements are summarized here quickly, to give an overview, with links to the respective sections of the glTF specification. More detailed explanations of the relationships between these elements will be given in the following sections.

这些元素在这里快速汇总，给出一个概述，并链接到glTF规范的各个部分。下面将详细解释这些元素之间的关系。

- The [`scene`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-scene) is the entry point for the description of the scene that is stored in the glTF. It refers to the `node`s that define the scene graph. [`scene`]是存储在glTF中的场景描述的入口点。它指的是定义场景图的`节点`。
- The [`node`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-node) is one node in the scene graph hierarchy. It can contain a transformation (e.g., rotation or translation), and it may refer to further (child) nodes. Additionally, it may refer to `mesh` or `camera` instances that are "attached" to the node, or to a `skin` that describes a mesh deformation.[`node`]是场景图层次结构中的一个节点。它可以包含一个转换（例如，旋转或平移），它可以引用更多（子）节点。此外，它可能指的是“连接”到节点的“网格”或“照相机”实例，或者描述网格变形的“蒙皮”。
- The [`camera`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-camera) defines the view configuration for rendering the scene.[`camera`]定义了渲染场景的视图配置。
- A [`mesh`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-mesh) describes a geometric object that appears in the scene. It refers to `accessor` objects that are used for accessing the actual geometry data, and to `material`s that define the appearance of the object when it is rendered.[`mesh`]描述了出现在场景中的几何对象。它指的是用于访问实际几何数据的'访问器（accessor）'对象，以及用于定义对象在呈现时的外观的'材质'（material）。
- The [`skin`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-skin) defines parameters that are required for vertex skinning, which allows the deformation of a mesh based on the pose of a virtual character. The values of these parameters are obtained from an `accessor`. [`skin`]定义顶点蒙皮所需的参数，该参数允许基于虚拟人物姿势上的网格的变形。这些参数的值是从`accessor`获得的。
- An [`animation`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-animation) describes how transformations of certain nodes (e.g., rotation or translation) change over time. [`animation`]描述了某些节点（例如旋转或平移）的转换如何随时间而改变。
- The [`accessor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-accessor) is used as an abstract source of arbitrary data. It is used by the `mesh`, `skin`, and `animation`, and provides the geometry data, the skinning parameters and the time-dependent animation values. It refers to a [`bufferView`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-bufferView), which is a part of a [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) that contains the actual raw binary data.
 [`accessor`]被用作任意数据的抽象来源。它被`mesh`，`skin`和`animation`使用，并提供几何数据，蒙皮参数和时间相关的动画值。它指的是[`bufferView`]，   它是[`buffer`]的一部分，它包含实际的原始二进制数据
- The [`material`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-material) contains the parameters that define the appearance of an object. It usually refers to `texture` objects that will be applied to the rendered geometry.
[`material`]包含定义对象外观的参数。它通常指的是渲染几何物体的“纹理”对象。
- The [`texture`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-texture) is defined by a [`sampler`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-sampler) and an [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image). The `sampler` defines how the texture `image` should be placed on the object.   [`texture`]由[`sampler`]和[`image`]定义。 `sampler`定义纹理`image`应该如何映射在物体上。




## References to external data  参考外部数据

The binary data, like geometry and textures of the 3D objects, are usually not contained in the JSON file. Instead, they are stored in dedicated files, and the JSON part only contains links to these files. This allows the binary data to be stored in a form that is very compact and can efficiently be transferred over the web. Additionally, the data can be stored in a format that can be used directly in the renderer, without having to parse, decode, or preprocess the data.    

二进制数据，如3D对象的几何图形和纹理，通常不包含在JSON文件中。相反，它们存储在专用文件中，JSON部分仅包含指向这些文件的链接。这允许二进制数据以非常紧凑的形式存储，并且可以通过网络高效地传输。另外，数据可以以可以直接在渲染器中使用的格式存储，而无需解析，解码或预处理数据。

<p align="center">
<img src="images/gltfStructure.png" /><br>
<a name="gltfStructure-png"></a>Image 2b: The glTF structure
</p>

As shown in the image above, there are two types of objects that may contain such links to external resources, namely `buffers` and `images`. These objects will later be explained in more detail.

如上图所示，有两种类型的对象可能包含到外部资源的链接，即“buffers”和“images”。稍后将更详细地解释这些对象。



## Reading and managing external data  读取和管理外部数据

Reading and processing a glTF asset starts with parsing the JSON structure. After the structure has been parsed, the [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) and [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image) objects are available in the top-level `buffers` and `images` arrays, respectively. Each of these objects may refer to blocks of binary data. For further processing, this data is read into memory. Usually, the data will be be stored in an array so that they may be looked up using the same index that is used for referring to the `buffer` or `image` object that they belong to. 

读取和处理glTF文件首先需要解析JSON结构。结构解析后，[`buffer`]和[`image`]对象分别位于顶层`buffers`和`images`数组中。这些对象中的每一个都可能涉及二进制数据块。为了进一步处理，这些数据被读入内存。通常，数据将被存储在一个数组中，以便可以使用与用于引用它们所属的“缓冲区”或“图像”对象相同的索引来查找数据。


## Binary data in `buffers` “buffers”中的二进制数据

A [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) contains a URI that points to a file containing the raw, binary buffer data:

[`buffer`]包含一个指向原始二进制缓冲区数据文件的URI：

```javascript
"buffer01": {
    "byteLength": 12352,
    "type": "arraybuffer",
    "uri": "buffer01.bin"
}
```

This binary data is just a raw block of memory that is read from the URI of the `buffer`, with no inherent meaning or structure. The [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) section will show how this raw data is extended with information about data types and the data layout. With this information, one part of the data may, for example, be interpreted as animation data, and another part may be interpreted as geometry data. Storing the data in a binary form allows it to be transferred over the web much more efficiently than in the JSON format, and the binary data can be passed directly to the renderer without having to decode or pre-process it. 

这个二进制数据只是从buffer的URI读取的原始内存块，没有固有的含义或结构。 [缓冲区，缓冲区视图和访问器]部分将显示如何使用有关数据类型和数据布局的信息扩展此原始数据。利用这些信息，例如，数据的一部分可以被解释为动画数据，而另一部分可以被解释为几何数据。以二进制形式存储数据允许通过网络以比JSON格式更高效地传输数据，并且二进制数据可以直接传递给渲染器而无需解码或预处理它。



## Image data in `images` “images”中的Image数据

An [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image) may refer to an external image file that can be used as the texture of a rendered object:

```javascript
"image01": {
    "uri": "image01.png"
}
```

The reference is given as a URI that usually points to a PNG or JPG file. These formats significantly reduce the size of the files so that they may efficiently be transferred over the web. In some cases, the `image` objects may not refer to an external file, but to data that is stored in a `buffer`. The details of this indirection will be explained in the [Textures, Images, and Samplers](gltfTutorial_016_TexturesImagesSamplers.md) section. 

该参考文献是作为指向PNG或JPG文件的URI提供的。这些格式显著减小了文件的大小，以便它们可以通过网络高效传输。在某些情况下，`image`对象可能不会引用外部文件，而是引用存储在“buffer”中的数据。具体细节将在[纹理，图像和采样器]部分进行说明。




## Binary data in data URIs

Usually, the URIs that are contained in the `buffer` and `image` objects will point to a file that contains the actual data. As an alternative, the data may be *embedded* into the JSON, in binary format, by using a [data URI](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs).、

通常，`buffer`和`image`对象中包含的URI将指向一个包含实际数据的文件。或者，可以使用[data URI]采用二进制格式将数据*嵌入到JSON中。


Previous: [Introduction](gltfTutorial_001_Introduction.md) | [Table of Contents](README.md) | Next: [A Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md)

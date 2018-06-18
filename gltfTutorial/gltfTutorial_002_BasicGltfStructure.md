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
- A [`mesh`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-mesh) describes a geometric object that appears in the scene. It refers to `accessor` objects that are used for accessing the actual geometry data, and to `material`s that define the appearance of the object when it is rendered.
- The [`skin`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-skin) defines parameters that are required for vertex skinning, which allows the deformation of a mesh based on the pose of a virtual character. The values of these parameters are obtained from an `accessor`.
- An [`animation`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-animation) describes how transformations of certain nodes (e.g., rotation or translation) change over time.
- The [`accessor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-accessor) is used as an abstract source of arbitrary data. It is used by the `mesh`, `skin`, and `animation`, and provides the geometry data, the skinning parameters and the time-dependent animation values. It refers to a [`bufferView`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-bufferView), which is a part of a [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) that contains the actual raw binary data.
- The [`material`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-material) contains the parameters that define the appearance of an object. It usually refers to `texture` objects that will be applied to the rendered geometry.
- The [`texture`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-texture) is defined by a [`sampler`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-sampler) and an [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image). The `sampler` defines how the texture `image` should be placed on the object.   




## References to external data

The binary data, like geometry and textures of the 3D objects, are usually not contained in the JSON file. Instead, they are stored in dedicated files, and the JSON part only contains links to these files. This allows the binary data to be stored in a form that is very compact and can efficiently be transferred over the web. Additionally, the data can be stored in a format that can be used directly in the renderer, without having to parse, decode, or preprocess the data.    

<p align="center">
<img src="images/gltfStructure.png" /><br>
<a name="gltfStructure-png"></a>Image 2b: The glTF structure
</p>

As shown in the image above, there are two types of objects that may contain such links to external resources, namely `buffers` and `images`. These objects will later be explained in more detail.



## Reading and managing external data

Reading and processing a glTF asset starts with parsing the JSON structure. After the structure has been parsed, the [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) and [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image) objects are available in the top-level `buffers` and `images` arrays, respectively. Each of these objects may refer to blocks of binary data. For further processing, this data is read into memory. Usually, the data will be be stored in an array so that they may be looked up using the same index that is used for referring to the `buffer` or `image` object that they belong to. 


## Binary data in `buffers`

A [`buffer`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-buffer) contains a URI that points to a file containing the raw, binary buffer data:

```javascript
"buffer01": {
    "byteLength": 12352,
    "type": "arraybuffer",
    "uri": "buffer01.bin"
}
```

This binary data is just a raw block of memory that is read from the URI of the `buffer`, with no inherent meaning or structure. The [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) section will show how this raw data is extended with information about data types and the data layout. With this information, one part of the data may, for example, be interpreted as animation data, and another part may be interpreted as geometry data. Storing the data in a binary form allows it to be transferred over the web much more efficiently than in the JSON format, and the binary data can be passed directly to the renderer without having to decode or pre-process it. 



## Image data in `images`

An [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image) may refer to an external image file that can be used as the texture of a rendered object:

```javascript
"image01": {
    "uri": "image01.png"
}
```

The reference is given as a URI that usually points to a PNG or JPG file. These formats significantly reduce the size of the files so that they may efficiently be transferred over the web. In some cases, the `image` objects may not refer to an external file, but to data that is stored in a `buffer`. The details of this indirection will be explained in the [Textures, Images, and Samplers](gltfTutorial_016_TexturesImagesSamplers.md) section. 




## Binary data in data URIs

Usually, the URIs that are contained in the `buffer` and `image` objects will point to a file that contains the actual data. As an alternative, the data may be *embedded* into the JSON, in binary format, by using a [data URI](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs).


Previous: [Introduction](gltfTutorial_001_Introduction.md) | [Table of Contents](README.md) | Next: [A Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md)

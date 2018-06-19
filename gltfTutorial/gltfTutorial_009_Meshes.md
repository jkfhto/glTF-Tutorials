Previous: [Simple Meshes](gltfTutorial_008_SimpleMeshes.md) | [Table of Contents](README.md) | Next: [Materials](gltfTutorial_010_Materials.md)

# Meshes

The [Simple Meshes](gltfTutorial_008_SimpleMeshes.md) example from the previous section showed a basic example of a [`mesh`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-mesh) with a [`mesh.primitive`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-primitive) object that contained several attributes. This section will explain the meaning and usage of mesh primitives, how meshes may be attached to nodes of the scene graph, and how they can be rendered with different materials.

上一节中的[Simple Meshes]示例展示了包含多个属性的mesh.primitive对象构成的一个网格对象的基本示例。本节将解释mesh primitives的含义和用法，网格如何附加到场景图的节点中，以及如何用不同的材质渲染它们。


## Mesh primitives  网格基元

Each `mesh` contains an array of `mesh.primitive` objects. These mesh primitive objects are smaller parts or building blocks of a larger object. A mesh primitive summarizes all information about how the respective part of the object will be rendered.

每个mesh包含一个mesh.primitive对象的数组。这些网格基元对象是较大对象的一部分组成结构或构建块。网格基元汇总了有关如何渲染对象的各个部分的所有信息。


### Mesh primitive attributes 网格基元属性

A mesh primitive defines the geometry data of the object using its `attributes` dictionary. This geometry data is given by references to `accessor` objects that contain the data of vertex attributes. The details of the `accessor` concept are explained in the [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md) section.

网格基元使用属性值来定义对象的几何数据。这个几何数据通过引用包含顶点属性数据的accessor对象来给出。accessor概念的细节在[Buffers, BufferViews, and Accessors]部分进行了解释。

In the given example, there are two entries in the `attributes` dictionary. The entries refer to the `positionsAccessor` and the `normalsAccessor`:

在给定的例子中，attributes属性中有两个条目。这些条目涉及positionsAccessor访问者和normalsAccessor访问者：

```javascript
  "meshes" : [
    {
      "primitives" : [ {
        "attributes" : {
          "POSITION" : 1,
          "NORMAL" : 2
        },
        "indices" : 0
      } ]
    }
  ],
```

Together, the elements of these accessors define the attributes that belong to the individual vertices, as shown in Image 9a.

这些accessors的元素一起定义了属于各个顶点的属性，如图9a所示。

<p align="center">
<img src="images/meshPrimitiveAttributes.png" /><br>
<a name="meshPrimitiveAttributes-png"></a>Image 9a: Mesh primitive accessors containing the data of vertices.
</p>


### Indexed and non-indexed geometry 索引和非索引几何体

The geometry data of a `mesh.primitive` may be either *indexed* geometry or geometry without indices. In the given example, the `mesh.primitive` contains *indexed* geometry. This is indicated by the `indices` property, which refers to the accessor with index 0, defining the data for the indices. For non-indexed geometry, this property is omitted.

mesh.primitive的几何数据可以是索引几何或没有索引的几何。在给定的例子中，mesh.primitive包含索引几何。这由索引属性表示，索引属性引用索引为0的访问者，来定义索引的数据。对于非索引几何体，该属性被省略。


### Mesh primitive mode   网格基元模式

By default, the geometry data is assumed to describe a triangle mesh. For the case of *indexed* geometry, this means that three consecutive elements of the `indices` accessor are assumed to contain the indices of a single triangle. For non-indexed geometry, three elements of the vertex attribute accessors are assumed to contain the attributes of the three vertices of a triangle.

默认情况下，几何数据被用来描述三角形网格。对于索引几何的情况，这意味着索引访问器的三个连续元素被假定为包含单个三角形的索引。对于非索引几何，顶点属性访问器的三个元素包含三角形的三个顶点的属性。

Other rendering modes are possible: the geometry data may also describe individual points, lines, or triangle strips. This is indicated by the `mode` that may be stored in the mesh primitive. Its value is a constant that indicates how the geometry data has to be interpreted. The mode may, for example, be `0` when the geometry consists of points, or `4` when it consists of triangles. These constants correspond to the GL constants `POINTS` or `TRIANGLES`, respectively. See the [`primitive.mode` specification](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#primitivemode) for a list of available modes.

其他可能的渲染模式：几何数据也可以描述单独的点，线或三角形条。这可以通过存储在mesh primitive中的mode来指示。它的值是一个常数，表示几何数据如何解释。例如，当几何体由点组成时，模式可以是0，或者当它由三角形组成时可以是4。这些常数分别对应于GL中的`POINTS`或者`TRIANGLES`。有关可用模式的列表，请参阅[`primitive.mode` specification]规范。

### Mesh primitive material 网格基元材质

The mesh primitive may also refer to the `material` that should be used for rendering, using the index of this material. In the given example, no `material` is defined, causing the objects to be rendered with a default material that just defines the objects to have a uniform 50% gray color. A detailed explanation of materials and the related concepts will be given in the [Materials](gltfTutorial_010_Materials.md) section.

网格基元也可以使用材质的索引来引用用于渲染的材质。在给定的例子中，没有定义材质，导致对象使用默认材质进行渲染，该材质只是将对象定义为具有统一的50％灰色。有关materials的相关概念和详细说明将在[Materials]部分给出。


## Meshes attached to nodes  网格附加到节点

In the example from the [Simple Meshes](gltfTutorial_008_SimpleMeshes.md) section, there is a single `scene`, which contains two nodes, and both nodes refer to the same `mesh` instance, which has the index 0:

在[Simple Meshes]部分的示例中，有一个scene，其中包含两个节点，并且这两个节点都指向同一个网格实例，其索引为0：

```javascript
  "scenes" : [
    {
      "nodes" : [ 0, 1]
    }
  ],
  "nodes" : [
    {
      "mesh" : 0
    },
    {
      "mesh" : 0,
      "translation" : [ 1.0, 0.0, 0.0 ]
    }
  ],
  
  "meshes" : [
    { ... } 
  ],
```

The second node has a `translation` property. As shown in the [Scenes and Nodes](gltfTutorial_004_ScenesNodes.md) section, this will be used to compute the local transform matrix of this node. In this case, the matrix will cause a translation of 1.0 along the x-axis. The product of all local transforms of the nodes will yield the [global transform](gltfTutorial_004_ScenesNodes.md#global-transforms-of-nodes). And all elements that are attached to the nodes will be rendered with this global transform.

第二个节点具有translation属性。如[Scenes and Nodes]部分所示，这将用于计算此节点的局部变换矩阵。在这种情况下，矩阵将导致沿x轴平移1.0。节点的局部变换将影响全局变换的结果。并且所有连接到该节点的元素都将使用此全局变换进行渲染。

So in this example, the mesh will be rendered twice because it is attached to two nodes: once with the global transform of the first node, which is the identity transform, and once with the global transform of the second node, which is a translation of 1.0 along the x-axis.

所以在这个例子中，网格将被渲染两次，因为它被连接到两个节点上：一次是第一个节点的全局变换，这是身份变换，一次是第二个节点的全局变换，这是一个沿着x轴平移1.0的变换。



Previous: [Simple Meshes](gltfTutorial_008_SimpleMeshes.md) | [Table of Contents](README.md) | Next: [Materials](gltfTutorial_010_Materials.md)

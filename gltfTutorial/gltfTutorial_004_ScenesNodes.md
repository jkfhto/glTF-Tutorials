Previous: [A Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md) | [Table of Contents](README.md) | Next: [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md)

# Scenes and Nodes 场景和节点

## Scenes 场景

There may be multiple scenes stored in one glTF file, but in many cases, there will be only a single scene, which then also is the default scene. Each scene contains an array of `nodes`, which are the indices of the root nodes of the scene graphs. Again, there may be multiple root nodes, forming different hierarchies, but in many cases, the scene will have a single root node. The most simple possible scene description has already been shown in the previous section, consisting of a single scene with a single node:

一个glTF文件中可能会存储多个场景，但在很多情况下，只会有一个场景，这也是默认场景。每个场景包含一组节点，这些节点是场景图形的根节点的索引。同样，可能有多个根节点，形成不同的层次结构，但在很多情况下，场景将具有单个根节点。最简单可能的场景描述已经在前一节中展示过，它由单个节点组成：

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


## Nodes forming the scene graph 形成场景图的节点

Each [`node`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-node) can contain an array called `children` that contains the indices of its child nodes. So each node is one element of a hierarchy of nodes, and together they define the structure of the scene as a scene graph.  

每个节点可以包含一个名为children的数组，其中包含其子节点的索引。所以每个节点都是节点层次结构中的一个元素，它们一起将场景的结构定义为场景图。

<p align="center">
<img src="images/sceneGraph.png" /><br>
<a name="sceneGraph-png"></a>Image 4a: The scene graph representation stored in the glTF JSON.
</p>

Each of the nodes that are given in the `scene` can be traversed, recursively visiting all their children, to process all elements that are attached to the nodes. The simplified pseudocode for this traversal may look like the following:

场景中给出的每个节点都可以遍历，递归地访问它们的所有子节点，以处理连接到节点的所有元素。这种遍历的简化伪代码可能如下所示：

```
traverse(node) {
    // Process the meshes, cameras, etc., that are
    // attached to this node - discussed later
    processElements(node);

    // Recursively process all children
    for each (child in node.children) {
        traverse(child);
    }
}
```

In practice, some additional information will be required for the traversal: the processing of some elements that are attached to nodes will require information about *which* node they are attached to. Additionally, the information about the transforms of the nodes has to be accumulated during the traversal.

在实践中，遍历需要一些额外的信息：处理连接到节点的一些元素将需要关于它们连接到哪个节点的信息。此外，有关节点变换的信息必须在遍历期间累积。


### Local and global transforms  局部和世界变换

Each node can have a transform. Such a transform will define a translation, rotation, and/or scale. This transform will be applied to all elements attached to the node itself and to all its child nodes. The hierarchy of nodes thus allows one to structure the translations, rotations, and scalings that are applied to the scene elements.

每个节点都可以有一个变换。这种转换将定义平移，旋转和/或缩放。此转换将应用于节点本身及其所有子节点的所有元素。节点层次结构允许人们构造应用于场景元素的平移，旋转和缩放。


#### Local transforms of nodes  节点的局部变换

There are different possible representations for the local transform of a node. The transform can be given directly by the `matrix` property of the node. This is an array of 16 floating point numbers that describe the matrix in column-major order. For example, the following matrix describes a scaling about (2,1,0.5), a rotation about 30 degrees around the x-axis, and a translation about (10,20,30):

节点的局部变换有不同的表示形式。变换可以直接由节点的矩阵属性给出。这是一个由16个浮点数组成的数组。例如，下面的矩阵描述了一个（2,1,0.5）的缩放，围绕x轴旋转30度以及关于（10,20,30）的平移变换：

```javascript
"node0": {
    "matrix": [
        2.0,    0.0,    0.0,    0.0,
        0.0,    0.866,  0.5,    0.0,
        0.0,   -0.25,   0.433,  0.0,
       10.0,   20.0,   30.0,    1.0
    ]
}    
```

The matrix defined here is as shown in Image 4b.

<p align="center">
<img src="images/matrix.png" /><br>
<a name="matrix-png"></a>Image 4b: An example matrix.
</p>


The transform of a node can also be given using the `translation`, `rotation`, and `scale` properties of a node, which is sometimes abbreviated as *TRS*:  

节点的变换也可以使用节点的平移，旋转和缩放属性给出，有时缩写为TRS

```javascript
"node0": {
    "translation": [ 10.0, 20.0, 30.0 ],
    "rotation": [ 0.259, 0.0, 0.0, 0.966 ],
    "scale": [ 2.0, 1.0, 0.5 ]
}
```

Each of these properties can be used to create a matrix, and the product of these matrices then is the local transform of the node:

每个属性都可以用来创建一个矩阵，然后这些矩阵的乘积就是节点的局部变换：

- The `translation` just contains the translation in x-, y-, and z-direction. For example, from a translation of `[ 10.0, 20.0, 30.0 ]`, one can create a translation matrix that contains this translation as its last column, as shown in Image 4c.

<p align="center">
<img src="images/translationMatrix.png" /><br>
<a name="translationMatrix-png"></a>Image 4c: A translation matrix.
</p>


- The `rotation` is given as a [quaternion](https://en.wikipedia.org/wiki/Quaternion). The mathematical background of quaternions is beyond the scope of this tutorial. For now, the most important information is that a quaternion is a compact representation of a rotation about an arbitrary angle and around an arbitrary axis. For example, the quaternion `[ 0.259, 0.0, 0.0, 0.966 ]` describes a rotation about 30 degrees, around the x-axis. So this quaternion can be converted into a rotation matrix, as shown in Image 4d.

<p align="center">
<img src="images/rotationMatrix.png" /><br>
<a name="rotationMatrix-png"></a>Image 4d: A rotation matrix.
</p>


- The `scale` contains the scaling factors along the x-, y-, and z-axes. The corresponding matrix can be created by using these scaling factors as the entries on the diagonal of the matrix. For example, the scale matrix for the scaling factors `[ 2.0, 1.0, 0.5 ]` is shown in Image 4e.

<p align="center">
<img src="images/scaleMatrix.png" /><br>
<a name="scaleMatrix-png"></a>Image 4e: A scale matrix.
</p>

When computing the final, local transform matrix of the node, these matrices are multiplied together. It is important to perform the multiplication of these matrices in the right order. The local transform matrix always has to be computed as `M = T * R * S`, where `T` is the matrix for the `translation` part, `R` is the matrix for the `rotation` part, and `S` is the matrix for the `scale` part. So the pseudocode for the computation is

当计算节点的最终局部变换矩阵时，这些矩阵相乘在一起。以正确的顺序执行这些矩阵的乘法很重要。局部变换矩阵总是必须被计算为'M = T * R * S'，其中'T'是'平移'部分的矩阵，'R'是'旋转'部分的矩阵，以及'S `是`缩放`部分的矩阵。所以计算的伪代码是

```
translationMatrix = createTranslationMatrix(node.translation);
rotationMatrix = createRotationMatrix(node.rotation);
scaleMatrix = createScaleMatrix(node.scale);
localTransform = translationMatrix * rotationMatrix * scaleMatrix;
```

For the example matrices given above, the final, local transform matrix of the node will be as shown in Image 4f.

对于上面给出的示例矩阵，节点的最终局部变换矩阵将如图4f所示。

<p align="center">
<img src="images/productMatrix.png" /><br>
<a name="produtMatrix-png"></a>Image 4f: The final local transform matrix computed from the TRS properties.
</p>

This matrix will cause the vertices of the meshes to be scaled, then rotated, and then translated according to the `scale`, `rotation`, and `translation` properties that have been given in the node.

这个矩阵将导致网格的顶点根据节点中给出的`scale`，`rotation`和`translation`属性进行缩放，然后旋转，然后平移。

When any of the three properties is not given, the identity matrix will be used. Similarly, when a node contains neither a `matrix` property nor TRS-properties, then its local transform will be the identity matrix.

当没有给出这三个属性中的任何一个时，将使用单位矩阵。类似地，当节点既不包含“矩阵”属性也不包含TRS属性时，则其本地变换将是单位矩阵。



#### Global transforms of nodes

Regardless of the representation in the JSON file, the local transform of a node can be stored as a 4&times;4 matrix. The *global* transform of a node is given by the product of all local transforms on the path from the root to the respective node:

无论JSON文件中的表示如何，节点的局部变换都可以用4×4矩阵存储。节点的* global *变换由从根节点到相应节点的路径上的所有本地变换的乘积给出：

    Structure:           local transform      global transform
    root                 R                    R
     +- nodeA            A                    R*A
         +- nodeB        B                    R*A*B
         +- nodeC        C                    R*A*C

It is important to point out that after the file was loaded these global transforms can *not* be computed only once. Later, it will be shown how *animations* may modify the local transforms of individual nodes. And these modifications will affect the global transforms of all descendant nodes. Therefore, when the global transform of a node is required, it has to be computed directly from the current local transforms of all nodes. Alternatively, and as a potential performance improvement, an implementation could cache the global transforms, detect changes in the local transforms of ancestor nodes, and update the global transforms only when necessary. The different implementation options for this will depend on the programming language and the requirements for the client application, and thus are beyond the scope of this tutorial.

需要指出的是，文件加载后，这些全局变换不能只计算一次。稍后，将会显示*animations*如何修改单个节点的局部变换。这些修改将影响所有后代节点的全局变换。因此，当需要节点的全局变换时，必须直接从所有节点的当前局部变换中计算出来。或者，作为潜在的性能改进，实现可以缓存全局变换，检测祖先节点的本地变换中的变化，并仅在必要时更新全局变换。不同的实现选项取决于编程语言和客户端应用程序的要求，因此超出了本教程的范围。



Previous: [A Minimal glTF File](gltfTutorial_003_MinimalGltfFile.md) | [Table of Contents](README.md) | Next: [Buffers, BufferViews, and Accessors](gltfTutorial_005_BuffersBufferViewsAccessors.md)

Previous: [Simple Skin](gltfTutorial_019_SimpleSkin.md) | [Table of Contents](README.md)


# Skins

The process of vertex skinning is a bit complex. It brings together nearly all elements that are contained in a glTF asset. This section will explain the basics of vertex skinning, based on the example in the [Simple Skin](gltfTutorial_019_SimpleSkin.md) section.

顶点蒙皮的实现过程有点复杂。它汇集了glTF文件中的几乎所有元素。本节将根据[Simple Skin]部分中的示例解释顶点蒙皮的基础知识。


## The geometry data  几何数据

The geometry of the vertex skinning example is an indexed triangle mesh, consisting of 8 triangles and 10 vertices. They form a rectangle in the xy-plane, with the lower left point at the origin (0,0,0), and the upper right point at (1,2,0). So the positions of the vertices are

顶点蒙皮示例的几何图形是一个索引的三角形网格，由8个三角形和10个顶点组成。它们在xy平面上形成一个矩形，其左下角指向原点（0,0,0），右上角指向（1,2,0）。所以顶点的位置是

    0.0, 0.0, 0.0,
    1.0, 0.0, 0.0,
    0.0, 0.5, 0.0,
    1.0, 0.5, 0.0,
    0.0, 1.0, 0.0,
    1.0, 1.0, 0.0,
    0.0, 1.5, 0.0,
    1.0, 1.5, 0.0,
    0.0, 2.0, 0.0,
    1.0, 2.0, 0.0

and the indices of the triangles are 三角形的索引是

    0, 1, 3,
    0, 3, 2,
    2, 3, 5,
    2, 5, 4,
    4, 5, 7,
    4, 7, 6,
    6, 7, 9,
    6, 9, 8,

The raw data is stored in the first `buffer`. The indices and vertex positions are defined by the `bufferView` objects at index 0 and 1, and the corresponding `accessor` objects at index 0 and 1 offer typed access to these buffer views. Image 20a shows this geometry with outline rendering to better show the structure.

原始数据存储在第一个buffer中。索引和顶点位置由索引0和1处的bufferView对象定义，索引0和1处的bufferView对象对应的accessor对象提供对这些缓冲区视图的类型访问权限。图像20a显示为了更好地显示结构对这个几何轮廓进行渲染。

<p align="center">
<img src="images/simpleSkinOutline01.png" /><br>
<a name="simpleSkinOutline01-png"></a>Image 20a: The geometry for the skinning example, with outline rendering, in its initial configuration.
</p>

This geometry data is contained in the mesh primitive of the only mesh, which is attached to the main node of the scene. The mesh primitive contains additional attributes, namely the `"JOINTS_0"` and `"WEIGHTS_0"` attributes. The purpose of these attributes will be explained below.

该几何数据包含在唯一网格的网格基元中，该网格连接到场景的主节点。网格基元包含附加属性，即“JOINTS_0”和“WEIGHTS_0”属性。这些属性的目的将在下面解释。


## The skeleton structure  骨架结构

In the given example, there are two nodes that define the skeleton. They are referred to as "skeleton nodes", or "joint nodes", because they can be imagined as the joints between the bones of the skeleton. The `skin` refers to these nodes, by listing their indices in its `joints` property.

在给定的例子中，有两个节点定义骨架。它们被称为“骨架节点”或“关节节点”，因为它们可以想象成骨架骨骼之间的关节。皮肤通过在其joints属性中指定索引来引用这些节点。

```javascript
  "nodes" : [ 
   ...
   {
    "children" : [ 2 ],
    "translation" : [ 0.0, 1.0, 0.0 ]
   }, 
   {
    "rotation" : [ 0.0, 0.0, 0.0, 1.0 ]
   }
  ],

```

The first joint node has a `translation` property, defining a translation about 1.0 along the y-axis. The second joint node has a `rotation` property that initially describes a rotation about 0 degrees (thus, no rotation at all). This rotation will later be changed by the animation to let the skeleton bend left and right and show the effect of the vertex skinning.

第一个关节节点具有平移特性，沿y轴进行1.0的平移。第二个关节节点指定了0度旋转的旋转属性（因此根本不旋转）。此旋转稍后将由动画更改，以使骨架向左和向右弯曲并显示顶点蒙皮的效果。

## The skin


The `skin` is the core element of the vertex skinning. In the example, there is a single skin:

skin是顶点蒙皮的核心元素。在这个例子中，有一个单一的皮肤：

```javascript
  "skins" : [ 
   {
    "inverseBindMatrices" : 4,
    "joints" : [ 1, 2 ]
   }
  ],

```

The skin contains an array called `joints`, which lists the indices of the nodes that define the skeleton hierarchy. Additionally, the skin contains a reference to an accessor in the property `inverseBindMatrices`. This accessor provides one matrix for each joint. Each of these matrices transforms the geometry into the space of the respective joint. This means that each matrix is the *inverse* of the global transform of the respective joint, in its initial configuration. In the given example, this inverse of the initial global transform is the same for both joint nodes:

skin包含一个名为“joints”的数组，其中列出了定义骨架层次结构的节点的索引。此外，skin包含对属性inverseBindMatrices中的访问器的引用。该访问器为每个关节提供一个矩阵。这些矩阵可以将几何形状转换到相应关节对应的空间中。这意味着每个矩阵在其初始配置中是相应关节的全局变换的逆矩阵。在给定的例子中，对于两个联合节点，初始全局变换的这个逆是相同的：

    1.0   0.0   0.0    0.0   
    0.0   1.0   0.0   -1.0   
    0.0   0.0   1.0    0.0   
    0.0   0.0   0.0    1.0  

This matrix translates the mesh about -1 along the y-axis, as shown Image 20b.

该矩阵将网格沿着y轴平移-1，如图20b所示。

<p align="center">
<img src="images/skinInverseBindMatrix.png" /><br>
<a name="skinInverseBindMatrix-png"></a>Image 20b: The transformation of the geometry with the inverse bind matrix of joint 1.
</p>

This transformation may look counterintuitive at first glance. But the goal of this transformation is to "undo" the transformation that is done by the initial global transform of the respective joint node so that the influences of the joint to the mesh vertices may be computed based on their actual global transform. 

乍看之下，这种转变可能看起来不符合直觉。但是这种变换的目标是“撤销”由相应节点的初始全局变换完成的变换，从而可以基于它们的实际全局变换来计算节点对网格顶点的影响。


## Vertex skinning implementation  顶点蒙皮实现

Users of existing rendering libraries will hardly ever have to manually process the vertex skinning data contained in a glTF asset: the actual skinning computations usually take place in the vertex shader, which is a low-level implementation detail of the respective library. However, knowing how the vertex skinning data is supposed to be processed may help to create proper, valid models with vertex skinning. So this section will give a short summary of how the vertex skinning is applied, using some pseudocode and examples in GLSL.

现有渲染库的用户几乎不需要手动处理包含在glTF资源中的顶点蒙皮数据：实际的蒙皮计算通常发生在顶点着色器中，这是相应库的低级实现细节。但是，知道顶点蒙皮数据应该如何处理可能有助于创建具有正确，有效的顶点蒙皮的模型。因此，本节将简要介绍如何使用GLSL中的一些伪代码和示例应用顶点蒙皮。

### The joint matrices 关节矩阵

The vertex positions of a skinned mesh are eventually computed by the vertex shader. During these computations, the vertex shader has to take into account the current pose of the skeleton in order to compute the proper vertex position. This information is passed to the vertex shader as an array of matrices, namely as the *joint matrices*. This is an array - that is, a `uniform` variable - that contains one 4&times;4 matrix for each joint of the skeleton. In the shader, these matrices are combined to compute the actual skinning matrix for each vertex:

顶点着色器最终计算蒙皮网格的顶点位置。在这些计算过程中，顶点着色器必须考虑骨架的当前姿态以计算适当的顶点位置。该信息作为矩阵数组传递给顶点着色器，即作为关节矩阵。这是一个数组 - 也就是一个统一变量 - 对于骨架的每个关节都包含一个4×4矩阵。在着色器中，将这些矩阵组合起来计算每个顶点的实际蒙皮矩阵：


```glsl
...
uniform mat4 u_jointMat[2];

...
void main(void)
{
    mat4 skinMat =
        a_weight.x * u_jointMat[int(a_joint.x)] +
        a_weight.y * u_jointMat[int(a_joint.y)] +
        a_weight.z * u_jointMat[int(a_joint.z)] +
        a_weight.w * u_jointMat[int(a_joint.w)];
    ....
}
```

The joint matrix for each joint has to perform the following transformations to the vertices:

每个关节的关节矩阵必须对顶点执行以下转换：

- The vertices have to be prepared to be transformed with the *current* global transform of the joint node. Therefore, they are transformed with the `inverseBindMatrix` of the joint node. This is the inverse of the global transform of the joint node *in its original state*, when no animations have been applied yet.

顶点必须准备好用关节节点的当前全局变换进行变换。因此，它们用关节节点的inverseBindMatrix进行转换。这是在尚未应用动画时，关节节点处于其原始状态下全局变换的逆矩阵。
- The vertices have to be transformed with the *current* global transform of the joint node. Together with the transformation from the `inverseBindMatrix`, this will cause the vertices to be transformed only based on the current transform of the node, in the coordinate space of the current joint node.

顶点必须用关节节点的当前全局变换进行变换。连同来自inverseBindMatrix的变换，这将导致顶点仅在当前节点的坐标空间中基于节点的当前变换进行变换。
- The vertices have to be transformed with *inverse* of the global transform of the node that the mesh is attached to, because this transform is already done using the model-view-matrix, and thus has to be cancelled out from the skinning computation.

顶点必须用网格所附节点的全局变换的逆来变换，因为这个变换已经使用模型 - 视图 - 矩阵完成了，因此必须从蒙皮计算中消除。

So the pseudocode for computing the joint matrix of joint `j` may look as follows:

因此，计算关节j的关节矩阵的伪代码可能看起来如下：

    jointMatrix(j) =
      globalTransformOfNodeThatTheMeshIsAttachedTo^-1 *
      globalTransformOfJointNode(j) *
      inverseBindMatrixForJoint(j);
      
Note: Vertex skinning in other contexts often involves a matrix that is called "Bind Shape Matrix". This matrix is supposed to transform the geometry of the skinned mesh into the coordinate space of the joints. In glTF, this matrix is omitted, and it is assumed that this transform is either premultiplied with the mesh data, or postmultiplied to the inverse bind matrices. 

注意：在其他上下文中的顶点蒙皮通常涉及一个称为“Bind Shape Matrix”的矩阵。这个矩阵使蒙皮网格几何对象在关节坐标空间中进行几何变换。在glTF中，这个矩阵使被省略的，并且假定这个变换预先与网格数据相乘，或者被后乘到逆矩阵。

Image 20c shows the transformations that are done to the geometry in the [Simple Skin](gltfTutorial_019_SimpleSkin.md) example, using the joint matrix of joint 1. The image shows the transformation for an intermediate state of the animation, namely, when the rotation of the joint node has already been modified by the animation, to describe a rotation about 45 degrees around the z-axis.

图20c示展示了使用关节1的关节矩阵对[Simple Skin]示例中的几何进行的变换。图像展示了动画的中间状态的变换，即，当关节节点的旋转已经由动画修改，描述围绕z轴旋转45度。

<p align="center">
<img src="images/skinJointMatrices.png" /><br>
<a name="skinJointMatrices-png"></a>Image 20c: The transformation of the geometry done for joint 1.
</p>

The last panel of Image 20c shows how the geometry would look like if it were *only* transformed with the joint matrix of joint 1. This state of the geometry is never really visible: The *actual* geometry that is computed in the vertex shader will *combine* the geometries as they are created from the different joint matrices, based on the joints- and weights that are explained below.

Image 20c的最后一个面板显示了几何体如果仅用关节1的关节矩阵进行变换时的外观。几何体的这种状态永远不会真正可见：在顶点着色器中计算的实际几何体将联合其他几何体因为它们是基于关节和权重根据不同的关节矩阵创建的。


### The skinning joints and weights  关节蒙皮和权重

As mentioned above, the mesh primitive contains new attributes that are required for the vertex skinning. Particularly, these are the `"JOINTS_0"` and the `"WEIGHTS_0"` attributes. Each attribute refers to an accessor that provides one data element for each vertex of the mesh.

如上所述，网格基元包含顶点蒙皮所需的新属性。特别是“JOINTS_0”和“WEIGHTS_0”属性。每个属性指的是为网格的每个顶点提供一个数据元素的访问器。

The `"JOINTS_0"` attribute refers to an accessor that contains the indices of the joints that should have an influence on the vertex during the skinning process. For simplicity and efficiency, these indices are usually stored as 4D vectors, limiting the number of joints that may influence a vertex to 4. In the given example, the joints information is very simple:

“JOINTS_0”属性指的是一个访问器，该访问器包含了蒙皮动画过程中对顶点有影响的关节索引。为了简单和高效，这些索引通常存储为4D向量，将可能影响顶点的关节数量限制为4.在给定示例中，关节信息非常简单：

    Vertex 0:  0, 1, 0, 0,
    Vertex 1:  0, 1, 0, 0,
    Vertex 2:  0, 1, 0, 0,
    Vertex 3:  0, 1, 0, 0,
    Vertex 4:  0, 1, 0, 0,
    Vertex 5:  0, 1, 0, 0,
    Vertex 6:  0, 1, 0, 0,
    Vertex 7:  0, 1, 0, 0,
    Vertex 8:  0, 1, 0, 0,
    Vertex 9:  0, 1, 0, 0,

This means that every vertex should be influenced by joint 0 and joint 1. (The last 2 components of each vector are ignored here. If there were multiple joints, then one entry of this accessor could, for example, contain

    3, 1, 8, 4,

meaning that the corresponding vertex should be influenced by the joints 3, 1, 8, and 4.)

这意味着每个顶点应该受到关节0和关节1的影响（每个向量的最后2个分量在这里被忽略了，如果有多个关节，那么这个访问器的一个条目可以包含3，1，8，4，
意味着相应的顶点应该受到关节3，1，8和4的影响。）

The `"WEIGHTS_0"` attribute refers to an accessor that provides information about how strongly each joint should influence each vertex. In the given example, the weights are as follows:

“WEIGHTS_0”属性指的是一个访问器，该访问器定义了每个关节节点对顶点产生影响的权重值。在给出的例子中，权重如下：

    Vertex 0:  1.00,  0.00,  0.0, 0.0,
    Vertex 1:  1.00,  0.00,  0.0, 0.0,
    Vertex 2:  0.75,  0.25,  0.0, 0.0,
    Vertex 3:  0.75,  0.25,  0.0, 0.0,
    Vertex 4:  0.50,  0.50,  0.0, 0.0,
    Vertex 5:  0.50,  0.50,  0.0, 0.0,
    Vertex 6:  0.25,  0.75,  0.0, 0.0,
    Vertex 7:  0.25,  0.75,  0.0, 0.0,
    Vertex 8:  0.00,  1.00,  0.0, 0.0,
    Vertex 9:  0.00,  1.00,  0.0, 0.0,

Again, the last two components of each entry are not relevant, because there are only two joints.

同样，每个条目的最后两个组件不相关，因为只有两个关节。

Combining the `"JOINTS_0"` and `"WEIGHTS_0"` attributes yields exact information about the influence that each joint has on each vertex. For example, the given data means that vertex 6 should be influenced to 25% by joint 0 and to 75% by joint 1.

结合“JOINTS_0”和“WEIGHTS_0”属性可以得到每个关节对每个顶点的影响的确切信息。例如，给定的数据意味着顶点6应该被关节0影响25％，被关节1影响75％。

In the vertex shader, this information is used to create a linear combination of the joint matrices. This matrix is called the *skin matrix* of the respective vertex. Therefore, the data of the `"JOINTS_0"` and `"WEIGHTS_0"` attributes are passed to the shader. In this example, they are given as the `a_joint` and `a_weight` attribute variable, respectively:

在顶点着色器中，该信息用于创建关节矩阵的线性组合。这个矩阵被称为相应顶点的皮肤矩阵。因此，“JOINTS_0”和“WEIGHTS_0”属性的数据将传递给着色器。在这个例子中，它们分别作为a_joint和a_weight属性变量给出：


```glsl
...
attribute vec4 a_joint;
attribute vec4 a_weight;

uniform mat4 u_jointMat[2];

...
void main(void)
{
    mat4 skinMat =
        a_weight.x * u_jointMat[int(a_joint.x)] +
        a_weight.y * u_jointMat[int(a_joint.y)] +
        a_weight.z * u_jointMat[int(a_joint.z)] +
        a_weight.w * u_jointMat[int(a_joint.w)];
    vec4 pos = u_modelViewMatrix * skinMat * vec4(a_position,1.0);
    gl_Position = u_projectionMatrix * pos;
}
```
The skin matrix is then used to transform the original position of the vertex before it is transformed with the model-view-matrix. The result of this transformation can be imagined as a weighted transformation of the vertices with the respective joint matrices, as shown in Image 20d.

然后在顶点的原始位置用模型视图矩阵变换之前使用蒙皮矩阵对其进行变换。如图20d所示，这种变换的结果可以想象成具有各自关节矩阵的顶点的加权变换。

<p align="center">
<img src="images/skinSkinMatrix.png" /><br>
<a name="skinSkinMatrix-png"></a>Image 20d: Computation of the skin matrix.
</p>

The result of applying this skin matrix to the vertices for the given example is shown in Image 20e.

<p align="center">
<img src="images/simpleSkinOutline02.png" /><br>
<a name="simpleSkinOutline02-png"></a>Image 20e: The geometry for the skinning example, with outline rendering, during the animation.
</p>







Previous: [Simple Skin](gltfTutorial_019_SimpleSkin.md) | [Table of Contents](README.md)

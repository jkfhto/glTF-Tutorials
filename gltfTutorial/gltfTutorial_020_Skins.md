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

<p align="center">
<img src="images/skinInverseBindMatrix.png" /><br>
<a name="skinInverseBindMatrix-png"></a>Image 20b: The transformation of the geometry with the inverse bind matrix of joint 1.
</p>

This transformation may look counterintuitive at first glance. But the goal of this transformation is to "undo" the transformation that is done by the initial global transform of the respective joint node so that the influences of the joint to the mesh vertices may be computed based on their actual global transform. 


## Vertex skinning implementation

Users of existing rendering libraries will hardly ever have to manually process the vertex skinning data contained in a glTF asset: the actual skinning computations usually take place in the vertex shader, which is a low-level implementation detail of the respective library. However, knowing how the vertex skinning data is supposed to be processed may help to create proper, valid models with vertex skinning. So this section will give a short summary of how the vertex skinning is applied, using some pseudocode and examples in GLSL.

### The joint matrices

The vertex positions of a skinned mesh are eventually computed by the vertex shader. During these computations, the vertex shader has to take into account the current pose of the skeleton in order to compute the proper vertex position. This information is passed to the vertex shader as an array of matrices, namely as the *joint matrices*. This is an array - that is, a `uniform` variable - that contains one 4&times;4 matrix for each joint of the skeleton. In the shader, these matrices are combined to compute the actual skinning matrix for each vertex:


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

- The vertices have to be prepared to be transformed with the *current* global transform of the joint node. Therefore, they are transformed with the `inverseBindMatrix` of the joint node. This is the inverse of the global transform of the joint node *in its original state*, when no animations have been applied yet.
- The vertices have to be transformed with the *current* global transform of the joint node. Together with the transformation from the `inverseBindMatrix`, this will cause the vertices to be transformed only based on the current transform of the node, in the coordinate space of the current joint node.
- The vertices have to be transformed with *inverse* of the global transform of the node that the mesh is attached to, because this transform is already done using the model-view-matrix, and thus has to be cancelled out from the skinning computation.

So the pseudocode for computing the joint matrix of joint `j` may look as follows:

    jointMatrix(j) =
      globalTransformOfNodeThatTheMeshIsAttachedTo^-1 *
      globalTransformOfJointNode(j) *
      inverseBindMatrixForJoint(j);
      
Note: Vertex skinning in other contexts often involves a matrix that is called "Bind Shape Matrix". This matrix is supposed to transform the geometry of the skinned mesh into the coordinate space of the joints. In glTF, this matrix is omitted, and it is assumed that this transform is either premultiplied with the mesh data, or postmultiplied to the inverse bind matrices. 

Image 20c shows the transformations that are done to the geometry in the [Simple Skin](gltfTutorial_019_SimpleSkin.md) example, using the joint matrix of joint 1. The image shows the transformation for an intermediate state of the animation, namely, when the rotation of the joint node has already been modified by the animation, to describe a rotation about 45 degrees around the z-axis.

<p align="center">
<img src="images/skinJointMatrices.png" /><br>
<a name="skinJointMatrices-png"></a>Image 20c: The transformation of the geometry done for joint 1.
</p>

The last panel of Image 20c shows how the geometry would look like if it were *only* transformed with the joint matrix of joint 1. This state of the geometry is never really visible: The *actual* geometry that is computed in the vertex shader will *combine* the geometries as they are created from the different joint matrices, based on the joints- and weights that are explained below.


### The skinning joints and weights

As mentioned above, the mesh primitive contains new attributes that are required for the vertex skinning. Particularly, these are the `"JOINTS_0"` and the `"WEIGHTS_0"` attributes. Each attribute refers to an accessor that provides one data element for each vertex of the mesh.

The `"JOINTS_0"` attribute refers to an accessor that contains the indices of the joints that should have an influence on the vertex during the skinning process. For simplicity and efficiency, these indices are usually stored as 4D vectors, limiting the number of joints that may influence a vertex to 4. In the given example, the joints information is very simple:

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

The `"WEIGHTS_0"` attribute refers to an accessor that provides information about how strongly each joint should influence each vertex. In the given example, the weights are as follows:

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

Combining the `"JOINTS_0"` and `"WEIGHTS_0"` attributes yields exact information about the influence that each joint has on each vertex. For example, the given data means that vertex 6 should be influenced to 25% by joint 0 and to 75% by joint 1.

In the vertex shader, this information is used to create a linear combination of the joint matrices. This matrix is called the *skin matrix* of the respective vertex. Therefore, the data of the `"JOINTS_0"` and `"WEIGHTS_0"` attributes are passed to the shader. In this example, they are given as the `a_joint` and `a_weight` attribute variable, respectively:

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

Previous: [Materials](gltfTutorial_010_Materials.md) | [Table of Contents](README.md) | Next: [Textures, Images, Samplers](gltfTutorial_012_TexturesImagesSamplers.md)

# A Simple Material  简单的材质

The examples of glTF assets that have been given in the previous sections contained a basic scene structure and simple geometric objects. But they did not contain information about the appearance of the objects. When no such information is given, viewers are encouraged to render the objects with a "default" material. And as shown in the screenshot of the [minimal glTF file](gltfTutorial_003_MinimalGltfFile.md), depending on the light conditions in the scene, this default material causes the object to be rendered with a uniformly white or light gray color.

前面部分给出的glTF文件的例子包含一个基本的场景结构和简单的几何对象。但是他们没有包含关于对象外观的信息。如果没有提供这些信息，则使用“默认”材质渲染对象。并且如[minimal glTF file]文件的屏幕截图所示，根据场景中的光线条件，此默认材质会使对象呈现均匀的白色或浅灰色。

This section will start with an example of a very simple material and explain the effect of the different material properties.

本节将从一个非常简单的材质的例子开始，并解释不同材质属性的影响。

This is a minimal glTF asset with a simple material:

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
        "indices" : 0,
        "material" : 0
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

  "materials" : [
    {
      "pbrMetallicRoughness": {
        "baseColorFactor": [ 1.000, 0.766, 0.336, 1.0 ],
        "metallicFactor": 0.5,
        "roughnessFactor": 0.1
      }
    }
  ],
  "asset" : {
    "version" : "2.0"
  }
}
```      

When rendered, this asset will show the triangle with a new material, as shown in Image 11a.

渲染时，该文件将显示带有新材质的三角形，如图11a所示。

<p align="center">
<img src="images/simpleMaterial.png" /><br>
<a name="simpleMaterial-png"></a>Image 11a: A triangle with a simple material.
</p>


## Material definition  材质定义


A new top-level array has been added to the glTF JSON to define this material: The `materials` array contains a single element that defines the material and its properties:

一个新的数组已经被添加到glTF JSON中以定义这个材质：materials数组包含一个定义材质及其属性的元素：

```javascript
  "materials" : [
    {
      "pbrMetallicRoughness": {
        "baseColorFactor": [ 1.000, 0.766, 0.336, 1.0 ],
        "metallicFactor": 0.5,
        "roughnessFactor": 0.1
      }
    }
  ],
```

The actual definition of the [`material`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-material) here only consists of the [`pbrMetallicRoughness`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-pbrmetallicroughness) object, which defines the basic properties of a material in the *metallic-roughness-model*. (All other material properties will therefore have default values, which will be explained later.) The `baseColorFactor` contains the red, green, blue, and alpha components of the main color of the material - here, a bright orange color. The `metallicFactor` of 0.5 indicates that the material should have reflection characteristics between that of a metal and a non-metal material. The `roughnessFactor` causes the material to not be perfectly mirror-like, but instead scatter the reflected light a bit.

此处[`material`]的实际定义仅由pbrMetallicRoughness对象组成，该对象定义*metallic-roughness-model*中材料的基本属性。 （因此所有其他材质属性都将具有默认值，这些将在稍后解释。）baseColorFactor表示材质主要颜色的红色，绿色，蓝色和alpha分量 - 此处为明亮的橙色。 0.5的metallicFactor金属因子表明材料应具有金属和非金属材料之间的反射特性。roughnessFactor粗糙因子使得材质不是完全镜像的，反射光会进行散射。

## Assigning the material to objects 将材质分配给对象

The material is assigned to the triangle, namely to the `mesh.primitive`, by referring to the material using its index:

材质使用其索引进行引用，并通过mesh.primitive分配到三角形：

```javascript
  "meshes" : [
    {
      "primitives" : [ {
        "attributes" : {
          "POSITION" : 1
        },
        "indices" : 0,
        "material" : 0
      } ]
    }
```

The next section will give a short introduction to how textures are defined in a glTF asset. The use of textures will then allow the definition of more complex and realistic materials.

下一节将简要介绍如何在glTF文件中定义纹理。纹理的使用将允许定义更复杂和更真实的材质。

Previous: [Materials](gltfTutorial_010_Materials.md) | [Table of Contents](README.md) | Next: [Textures, Images, Samplers](gltfTutorial_012_TexturesImagesSamplers.md)

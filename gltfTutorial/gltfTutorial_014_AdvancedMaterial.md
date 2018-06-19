Previous: [Simple Texture](gltfTutorial_013_SimpleTexture.md) | [Table of Contents](README.md) | Next: [Simple Cameras](gltfTutorial_015_SimpleCameras.md)

# An Advanced Material

The [Simple Texture](gltfTutorial_013_SimpleTexture.md) example in the previous section showed a material for which the "base color" was defined using a texture. But in addition to the base color, there are other properties of a material that may be defined via textures. These properties have already been summarized in the [Materials](gltfTutorial_010_Materials.md) section:

上一节中的[Simple Texture]示例展示了使用纹理为其定义“基本颜色”的材质。但除了基本颜色之外，还可以通过纹理定义材质的其他属性。这些属性已经在[Materials]部分进行了总结：

- The *base color*,  基本颜色
- The *metallic* value,  金属值，
- The *roughness* of the surface,  表面的粗糙度
- The *emissive* properties,  emissive性质
- An *occlusion* texture, and  遮挡纹理
- A *normal map*.  法线贴图


The effects of these properties cannot properly be demonstrated with trivial textures. Therefore, they will be shown here using one of the official Khronos PBR sample models, namely, the [WaterBottle](https://github.com/KhronosGroup/glTF-Sample-Models/tree/master/2.0/WaterBottle) model. Image 14a shows an overview of the textures that are involved in this model, and the final rendered object:

这些属性的影响不能用简单的纹理来精确地表现出来。因此，他们将使用Khronos官方 PBR样本模型之一的WaterBottle模型来显示。图14a显示了该模型中涉及的纹理以及最终渲染对象的概览：

<p align="center">
<img src="images/materials.png" /><br>
<a name="cameras-png"></a>Image 14a: An example of a material where the surface properties are defined via textures.
</p>

Explaining the implementation of physically based rendering is beyond the scope of this tutorial. The official Khronos [WebGL PBR repository](https://github.com/KhronosGroup/glTF-WebGL-PBR) contains a reference implementation of a PBR renderer based on WebGL, and provides implementation hints and background information. The following images mainly aim at demonstrating the effects of the different material property textures, under different lighting conditions.

解释基于物理渲染的实现超出了本教程的范围。Khronos官方文档 [WebGL PBR repository]包含基于WebGL的PBR渲染器的参考实现，并提供实现提示和背景信息。以下图像主要旨在演示不同光照条件下不同材质属性纹理的效果。

Image 14b shows the effect of the roughness texture: the main part of the bottle has a low roughness, causing it to appear shiny, compared to the cap, which has a rough surface structure.

图14b显示粗糙纹理的效果：与具有粗糙表面结构的瓶盖相比，瓶的主要部分具有低粗糙度，使其看起来有光泽。

<p align="center">
<img src="images/advancedMaterial_roughness.png" /><br>
<a name="advancedMaterial_roughness-png"></a>Image 14b: The influence of the roughness texture.
</p>

Image 14c highlights the effect of the metallic texture: the bottle reflects the light from the surrounding environment map.

图像14c突出了金属质感的效果：瓶子反射周围环境贴图中的光线。

<p align="center">
<img src="images/advancedMaterial_metallic.png" /><br>
<a name="advancedMaterial_metallic-png"></a>Image 14c: The influence of the metallic texture.
</p>

Image 14d shows the emissive part of the texture: regardless of the dark environment setting, the text, which is contained in the emissive texture, is clearly visible.

图像14d显示了纹理的发射部分：无论黑暗的环境如何设置，包含在发射纹理中的文本都清晰可见

<p align="center">
<img src="images/advancedMaterial_emissive.png" /><br>
<a name="advancedMaterial_emissive-png"></a>Image 14d: The emissive part of the texture.
</p>

Image 14e shows the part of the bottl cap for which a normal map is defined: the text appears to be embossed into the cap. This makes it possible to model finer geometric details on the surface, even though the model itself only has a very coarse geometric resolution.

图14e显示了定义法线贴图的瓶盖的部分：文字似乎压印在瓶盖中。这使得可以在表面上建立更精细的几何细节，即使模型本身只具有非常粗糙的几何分辨率。

<p align="center">
<img src="images/advancedMaterial_normal.png" /><br>
<a name="advancedMaterial_normal-png"></a>Image 14r: The effect of a normal map.
</p>

Together, these textures and maps allow modeling a wide range of real-world materials. Thanks to the common underlying PBR model - namely, the metallic-roughness model - the objects can be rendered consistently by different renderer implementations.

这些纹理和贴图一起使用可以模拟各种真实世界的材质。由于都基于PBR模型 - 即金属粗糙度模型 - 不同的渲染器可以实现一致地渲染效果。



Previous: [Simple Texture](gltfTutorial_013_SimpleTexture.md) | [Table of Contents](README.md) | Next: [Simple Cameras](gltfTutorial_015_SimpleCameras.md)

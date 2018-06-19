Previous: [Simple Material](gltfTutorial_011_SimpleMaterial.md) | [Table of Contents](README.md) | Next: [Simple Texture](gltfTutorial_013_SimpleTexture.md)

# Textures, Images, and Samplers  纹理，图像和采样器

Textures are an important aspect of giving objects a realistic appearance. They make it possible to define the main color of the objects, as well as other characteristics that are used in the material definition in order to precisely describe what the rendered object should look like.

纹理是赋予对象真实外观的重要手段。它们可以定义对象的主要颜色以及各个位置的具体材质特征，以精确描述渲染对象的外观。

A glTF asset may define multiple [`texture`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-texture) objects, which can be used as the textures of geometric objects during rendering, and which can be used to encode different material properties. Depending on the graphics API, there may be many features and settings that influence the process of texture mapping. Many of these details are beyond the scope of this tutorial. There are dedicated tutorials that explain the exact meaning of all the texture mapping parameters and settings; for example, on [webglfundamentals.org](http://webglfundamentals.org/webgl/lessons/webgl-3d-textures.html),  [open.gl](https://open.gl/textures), and others. This section will only summarize how the information about textures is encoded in a glTF asset.

glTF文件可以定义多个[`texture`]纹理对象，这些对象可以在渲染过程中用作几何对象的纹理，并且可以用于编码不同的材质属性。根据图形API，可能会有许多影响纹理映射过程的功能和设置。许多这些细节超出了本教程的范围。有专门的教程解释所有纹理映射参数和设置的确切含义;例如，在webglfundamentals.org，open.gl和其他网站上。本节仅概述纹理信息在glTF文件中的编码方式。

There are three top-level arrays for the definition of textures in the glTF JSON. The `textures`, `samplers`, and `images` dictionaries contain  [`texture`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-texture),  [`sampler`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-sampler), and [`image`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/#reference-image) objects, respectively. The following is an excerpt from the [Simple Texture](gltfTutorial_013_SimpleTexture.md) example, which will be presented in the next section:

在glTF JSON中，有三个用于定义纹理的数组。纹理，采样器和图像数组分别包含纹理，采样器和图像对象。以下是简单纹理示例的摘录，将在下一节中介绍：

```javascript
"textures": {
  {
    "source": 0,
    "sampler": 0
  }
},
"images": {
  {
    "uri": "testTexture.png"
  }
},
"samplers": {
  {
     "magFilter": 9729,
     "minFilter": 9987,
     "wrapS": 33648,
     "wrapT": 33648
   }
},
```

The `texture` itself uses indices to refer to one `sampler` and one `image`. The most important element here is the reference to the `image`. It contains a URI that links to the actual image file that will be used for the texture. Information about how to read this image data can be found in the section about [image data in `images`](gltfTutorial_002_BasicGltfStructure.md#image-data-in-images).

纹理本身使用索引来指向一个sampler采样器和一个image图片。这里最重要的元素是对image图像的引用。它包含一个URI，链接到将用于纹理的实际图像文件。有关如何读取图像数据的信息可以在[image data in `images`]部分找到。

The next section will show how such a texture definition may be used inside a material. 

下一节将展示如何在材质内使用这种纹理定义。

Previous: [Simple Material](gltfTutorial_011_SimpleMaterial.md) | [Table of Contents](README.md) | Next: [Simple Texture](gltfTutorial_013_SimpleTexture.md)

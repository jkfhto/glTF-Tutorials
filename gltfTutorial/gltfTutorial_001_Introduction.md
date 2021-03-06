[Table of Contents](README.md) | Next: [Basic glTF Structure](gltfTutorial_002_BasicGltfStructure.md)





# Introduction to glTF using WebGL

An increasing number of applications and services are based on 3D content. Online shops offer product configurators with a 3D preview. Museums digitize their artifacts with 3D scans and allow visitors to explore their collections in virtual galleries. City planners use 3D city models for planning and information visualization. Educators create interactive, animated 3D models of the human body. Many of these applications run directly in the web browser, which is possible because all modern browsers support efficient rendering with WebGL.

越来越多的应用程序和服务都基于3D内容。网上商店提供带3D预览的产品配置器。博物馆利用3D扫描数字化他们的工件，并允许访客在虚拟画廊中探索他们的收藏。城市规划人员使用3D城市模型进行规划和信息可视化。教育者可以创建交互式的人体动画3D模型。许多这些应用程序直接在Web浏览器中运行，这是可能的，因为所有现代浏览器都支持使用WebGL进行高效渲染。

<p align="center">
<img src="images/applications.png" /><br>
<a name="applications-png"></a>Image 1a: Screenshots of various websites and applications showing 3D models.
</p>

Demand for 3D content in various applications is constantly increasing. In many cases, the 3D content has to be transferred over the web, and it has to be rendered efficiently on the client side. But until now, there has been a gap between the 3D content creation and efficient rendering of that 3D content in the runtime applications.

在各种应用中对3D内容的需求在不断增加。在很多情况下，3D内容必须通过网络传输，并且必须在客户端高效渲染。但是到目前为止，在运行时应用程序中，3D内容创建和3D内容的高效渲染之间存在差距。


## 3D content pipelines

3D content that is rendered in client applications comes from different sources and is stored in different file formats. The [list of 3D graphics file formats on Wikipedia](https://en.wikipedia.org/wiki/List_of_file_formats#3D_graphics) shows an overwhelming number, with more than 70 different file formats for 3D data, serving different purposes and application cases.  

在客户端应用程序中呈现的3D内容来自不同的来源，并以不同的文件格式存储。维基百科上的显示拥有超过70种不同的3D数据文件格式，可用于不同的目的和应用案例。

For example, raw 3D data may be obtained with a 3D scanner. These scanners usually provide the geometry data of a single object, which is stored in [OBJ](https://en.wikipedia.org/wiki/Wavefront_.obj_file), [PLY](https://en.wikipedia.org/wiki/PLY_(file_format)), or [STL](https://en.wikipedia.org/wiki/STL_(file_format)) files. These file formats do not contain information about the scene structure or how the objects should be rendered.

例如，可以用3D扫描仪获得原始3D数据。这些扫描仪通常提供单个对象的几何数据，该数据存储在[OBJ]，[PLY]或[STL]格式的文件中。这些文件格式不包含场景结构的信息或者应该如何呈现对象的信息。.

More sophisticated 3D scenes can be created with authoring tools. These tools allow one to edit the structure of the scene, the light setup, cameras, animations, and, of course, the 3D geometry of the objects that appear in the scene. Applications store this information in their own, custom file formats. For example, [Blender](https://www.blender.org/) stores the scenes in `.blend` files, [LightWave3D](https://www.lightwave3d.com/) uses the `.lws` file format, [3ds Max](http://www.autodesk.com/3dsmax) uses the `.max` file format, and [Maya](http://www.autodesk.com/maya) uses `.ma` files.

使用创作工具可以创建更复杂的3D场景。这些工具允许编辑场景的结构，灯光设置，摄像机，动画，当然还有场景中出现的物体的3D几何。应用程序以自己的自定义文件格式存储这些信息。例如，[Blender]将场景存储在`.blend`文件中，[LightWave3D]使用`.lws`文件格式，[3ds Max]使用`.max`文件格式，[Maya]使用`.ma`文件。


In order to render such 3D content, the runtime application must be able to read different input file formats. The scene structure has to be parsed, and the 3D geometry data has to be converted into the format required by the graphics API. The 3D data has to be transferred to the graphics card memory, and then the rendering process can be described with sequences of graphics API calls. Thus, each runtime application has to create importers, loaders, or converters for all file formats that it will support, as shown in [Image 1b](#contentPipeline-png).

为了渲染这样的3D内容，应用程序必须能够读取不同的输入文件格式。必须解析场景结构，并且必须将3D几何数据转换为图形API所需的格式。 3D数据必须传输到图形卡存储器，然后可以使用图形API调用序列来描述渲染过程。因此，每个运行时应用程序必须为它将支持的所有文件格式创建导入程序，加载程序或转换程序，如下图所示：

<p align="center">
<img src="images/contentPipeline.png" /><br>
<a name="contentPipeline-png"></a>Image 1b: The 3D content pipeline today.
</p>


## glTF: A transmission format for 3D scenes 3D场景的传输格式

The goal of glTF is to define a standard for representing 3D content, in a form that is suitable for use in runtime applications. The existing file formats are not appropriate for this use case: some of do not contain any scene information, but only geometry data; others have been designed for exchanging data between authoring applications, and their main goal is to retain as much information about the 3D scene as possible, resulting in files that are usually large, complex, and hard to parse. Additionally, the geometry data may have to be preprocessed so that it can be rendered with the client application.

glTF的目标是定义一个展示3D内容的标准，以适合在应用程序中使用。现有的文件格式不适合这种用例：其中一些不包含任何场景信息，但仅包含几何数据;其他设计用于在应用程序之间交换数据，其主要目标是尽可能多地保留关于3D场景的信息，从而导致文件通常较大，很复杂且难以解析。此外，几何数据可能必须进行预处理，以便可以使用客户端应用程序进行渲染。

None of the existing file formats were designed for the use case of efficiently transferring 3D scenes over the web and rendering them as efficiently as possible. But glTF is not "yet another file format." It is the definition of a *transmission* format for 3D scenes:

没有一种现有的文件格式被设计用于通过网络高效地传送3D场景并尽可能高效地渲染它们。但是glTF不是“另一种文件格式”。它是3D场景的*传输格式的定义：

- The scene structure is described with JSON, which is very compact and can easily be parsed.
场景结构用JSON描述，非常紧凑，可以轻松解析。
- The 3D data of the objects are stored in a form that can be directly used by the common graphics APIs, so there is no overhead for decoding or pre-processing the 3D data.
对象的3D数据以可以由公共图形API直接使用的形式存储，因此不存在解码或预处理3D数据的开销。

Different content creation tools may now provide 3D content in the glTF format. And an increasing number of client applications are able to consume and render glTF. Some of these applications are shown in [Image 1a](#applications-png). So glTF may help to bridge the gap between content creation and rendering, as shown in [Image 1c](#contentPipelineWithGltf-png).

现在不同的内容创建工具可以提供glTF格式的3D内容。越来越多的客户端应用程序能够使用并呈现glTF。其中一些应用程序显示在[Image 1a]中。所以glTF可能有助于弥补内容创建和渲染之间的差距，如[Image 1c]所示。

<p align="center">
<img src="images/contentPipelineWithGltf.png" /><br>
<a name="contentPipelineWithGltf-png"></a>Image 1c: The 3D content pipeline with glTF.
</p>

An increasing number of content creation tools will be able to provide glTF directly. Alternatively, other file formats can be used to create glTF assets, using one of the open-source conversion utilities listed in the [Khronos glTF repository](https://github.com/KhronosGroup/glTF#converters). For example, nearly all authoring applications can export their scenes in the [COLLADA](https://www.khronos.org/collada/) format. So the [COLLADA2GLTF](https://github.com/KhronosGroup/COLLADA2GLTF) tool can be used to convert scenes and models from these authoring applications to glTF. `OBJ` files may be converted to glTF using [obj2gltf](https://github.com/AnalyticalGraphicsInc/obj2gltf). For other file formats, custom converters can be used to create glTF assets, thus making the 3D content available for a broad range of runtime applications.

越来越多的内容创作工具将能够直接提供glTF。或者，可以使用其他文件格式创建glTF，使用[Khronos glTF存储库]中列出的开放源代码转换实用程序。例如，几乎所有创作应用程序都可以使用[COLLADA]格式导出场景。因此，[COLLADA2GLTF]工具可用于将场景和模型从这些创作应用程序转换为glTF。 `OBJ`文件可以使用[obj2gltf]转换为glTF。对于其他文件格式，可以使用自定义转换器创建glTF资源，从而使3D内容可用于应用程序。


[Table of Contents](README.md) | Next: [Basic glTF Structure](gltfTutorial_002_BasicGltfStructure.md)

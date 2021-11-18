# 使用 Makie.jl 做数据可视化 {#sec:DataVisualizationMakie}

> Maki-e 来源于日语， 它指的是一种在漆面上撒金粉和银粉的技术。
> 数据就是我们这个时代的金和银，让我们在屏幕上制作美丽的数据图吧！
>
> _Simon Danisch,  `Makie.jl` 创始人_

[Makie.jl](http://makie.juliaplots.org/stable/index.html) 是高性能，可扩展且跨平台的 Julia 语言绘图系统。
我们认为，它是最漂亮和最通用的绘图包。

与其他绘图包一样，该库的代码分为多个包。
`Makie.jl` 是绘图前端，它定义了所有创建绘图对象需要的函数。
虽然这些对象存储了绘图所需的全部信息，但还未转换为图片。
因此，我们需要一个 Makie 后端。
默认情况下，每一个后端都将 `Makie.jl` 中的API都重新导出了，因此只需要安装和加载所需的后端包即可。

目前主要有三个后端实现了 Makie 中定义的所有抽象类型的渲染功能。
第一个后端能够绘制 2D 非交互式的出版物质量级矢量图：`CairoMakie.jl`。
另一个后端是交互式 2D 和 3D 绘图库 `GLFW.jl`（支持 GPU），`GLMakie.jl`。
第三个后端是基于 WebGL 的交互式 2D 和 3D 绘图库 `WGLMakie.jl`，它运行在浏览器中。[查阅 Makie 文档了解更多](http://makie.juliaplots.org/stable/documentation/backends_and_output/)。

本书将只介绍一些 `CairoMakie.jl` 和 `GLMakie.jl` 的例子。

使用任一绘图后端的方法是 `using` 该后端并调用 `activate!` 函数。
示例如下：

```
using GLMakie
GLMakie.activate!()
```

现在可以开始绘制出版质量级的图。
但是，在绘图之前，应知道如何保存。
`save` 图片 `fig` 的最简单方法是 `save("filename.png", fig)`。
`CairoMakie.jl` 也支持保存为其他格式，如 `svg` 和 `pdf`。
通过传递指定的参数可以轻松地改变图片的分辨率。
对于矢量格式，指定的参数为 `pt_per_unit`。例如：

```
save("filename.pdf", fig; pt_per_unit=2)
```

或

```
save("filename.pdf", fig; pt_per_unit=0.5)
```

对于 `png`，则指定 `px_per_unit`。
查阅 [后端 & 输出](https://makie.juliaplots.org/stable/documentation/backends_and_output/) 可获得更多详细信息。

另一重要问题是如何可视化输出数据图。
在使用 `CairoMakie.jl` 时，Julia REPL 不支持显示图片， 所以你还需要 IDE（Integrated Development Environment，集成开发环境），例如支持 `png` 或 `svg` 作为输出的 VSCode，Jupyter 或 Pluto。
另一个包 `GLMakie.jl` 则能够创建交互式窗口，或在调用 `Makie.inline!(true)` 时在行间显示位图。

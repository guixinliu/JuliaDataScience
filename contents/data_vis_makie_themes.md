## 主题 {#sec:themes}

有多种方式可以改变图的整体外观。你可以使用 [预定义主题](http://makie.juliaplots.org/stable/documentation/theming/predefined_themes/index.html) 或自定义的主题。例如，通过 `with_theme(your_plot_function, theme_dark())` 使用预定义的暗色主题。另外，也可以使用 `Theme(kwargs)` 构建你自己的主题或使用 `update_theme!(kwargs)` 更新当前激活的主题。

还可以使用 `set_theme!(theme; kwargs...)` 将当前主题改为 `theme`， 并且通过 `kwargs` 覆盖或增加一些属性。使用不带参数的 `set_theme!()` 即可恢复到之前主题的设置。在下面的例子中，我们准备了具有不同样式的测试绘图函数，以便于观察每个主题的大多数属性。

```jl
sco(
"""
using Random: seed!
seed!(123)
y = cumsum(randn(6, 6), dims=2)
"""
)
```

本例随机生成了一个大小为 `(20,20)` 的矩阵，以便于绘制一张热力图（heatmap）。
同时本例也指定了 $x$ 和 $y$ 的范围。

```jl
sco(
"""
using Random: seed!
seed!(13)
xv = yv = LinRange(-3, 0.5, 20)
matrix = randn(20, 20)
matrix[1:6, 1:6] # first 6 rows and columns
"""
)
```

因此，新绘图函数如下所示：

```jl
@sc demo_themes(y, xv, yv, matrix)
```

注意，`series` 函数的作用是同时绘制多条附带标签的直线图和散点图。另外还绘制了附带 colorbar 的 heatmap。如图所示，有两种暗色主题，一种是 `theme_dark()` ，另一种是 `theme_black()`。

```jl
s = """
    CairoMakie.activate!() # hide
    filenames = ["theme_dark", "theme_black"] # hide
    objects = [ # hide
    # Don't indent here because it indent the output incorrectly. # hide
    with_theme(theme_dark()) do
        demo_themes(y, xv, yv, matrix)
    end
    with_theme(theme_black()) do
        demo_themes(y, xv, yv, matrix)
    end
    ] # hide
    link_attributes = "width=60%" # hide
    Options(obj, filename, link_attributes) = Options(obj; filename, link_attributes) # hide
    Options.(objects, filenames, link_attributes) # hide
    """
sco(s)
```

另外有三种白色主题，`theme_ggplot2()`，`theme_minimal()` 和 `theme_light()`。这些主题对于更标准的出版图很有用。

```jl
s = """
    CairoMakie.activate!() # hide
    filenames = ["theme_ggplot2", # hide
        "theme_minimal", "theme_light"] # hide
    objects = [ # hide
    # Don't indent here because it indent the output incorrectly. # hide
    with_theme(theme_ggplot2()) do
        demo_themes(y, xv, yv, matrix)
    end
    with_theme(theme_minimal()) do
        demo_themes(y, xv, yv, matrix)
    end
    with_theme(theme_light()) do
        demo_themes(y, xv, yv, matrix)
    end
    ] # hide
    link_attributes = "width=60%" # hide
    Options(obj, filename, link_attributes) = Options(obj; filename, link_attributes) # hide
    Options.(objects, filenames, link_attributes) # hide
    """
sco(s)
```

另一种方案是通过使用 `with_theme(your_plot, your_theme())` 创建自定义 `Theme` 。
例如，以下主题可以作为出版质量图的初级模板：

```jl
@sc publication_theme()
```

为简单起见，在接下来的例子中使用它绘制 `scatterlines` 和 `heatmap`。

```jl
@sc plot_with_legend_and_colorbar()
```

然后使用前面定义的 `Theme`，其输出如 (@fig:plot_with_legend_and_colorbar) 所示。

```jl
s = """
    CairoMakie.activate!() # hide
    with_theme(plot_with_legend_and_colorbar, publication_theme())
    label = "plot_with_legend_and_colorbar" # hide
    caption = "Themed plot with Legend and Colorbar." # hide
    link_attributes = "width=60%" # hide
    Options(current_figure(); filename=label, label, caption, link_attributes) # hide
    """
sco(s)
```

如果需要在 `set_theme!(your_theme)`后更改一些设置，那么可以使用 `update_theme!(resolution=(500, 400), fontsize=18)`。
另一种方法是给 `with_theme` 函数传递额外的参数：

```jl
s = """
    CairoMakie.activate!() # hide
    fig = (resolution=(600, 400), figure_padding=1, backgroundcolor=:grey90)
    ax = (; aspect=DataAspect(), xlabel=L"x", ylabel=L"y")
    cbar = (; height=Relative(4 / 5))
    with_theme(publication_theme(); fig..., Axis=ax, Colorbar=cbar) do
        plot_with_legend_and_colorbar()
    end
    label = "plot_theme_extra_args" # hide
    caption = "Theme with extra args." # hide
    link_attributes = "width=60%" # hide
    Options(current_figure(); filename=label, caption, label, link_attributes) # hide
    """
sco(s)
```

现在，接下来将讨论如何使用 LaTeX 字符串和自定义主题进行绘图。

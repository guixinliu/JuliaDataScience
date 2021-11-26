## 颜色和颜色图（Colormap）{#sec:makie_colors}

在展示结果时，其中重要的一步是为图选择一组合适的颜色或 colorbar。
`Makie.jl` 支持使用 [Colors.jl](https://github.com/JuliaGraphics/Colors.jl) ，因此你可以使用 [named colors](https://juliagraphics.github.io/Colors.jl/latest/namedcolors/) 而不是传递 `RGB` 或 `RGBA` 值。
另外，也可以使用 [ColorSchemes.jl](https://github.com/JuliaGraphics/ColorSchemes.jl) 和 [PerceptualColourMaps.jl](https://github.com/peterkovesi/PerceptualColourMaps.jl) 中的颜色图。
值得了解的是，可以使用 `Reverse(:colormap_name)` 反转颜色图 ，也可以通过 `color=(:red,0.5)` and `colormap=(:viridis, 0.5)` 获得透明的颜色或颜色图。

下文介绍不同的用例。 接下来使用新的颜色和颜色栏（Colorbar）调色盘来创建自定义主题。

默认情况下， `Makie.jl` 已经预定义一组颜色，这是为了自动循环该组颜色。
之前的图因此并未设置任何特定颜色。
覆盖这些默认颜色的方法是，在绘图函数中调用 `color` 关键字并使用 `Symbol` 或 `String` 指定新的颜色。
该操作如下所示：

```jl
@sco JDS.set_colors_and_cycle()
```

通过`color` 关键字指定了上例前两条曲线的颜色。
其余使用默认的颜色集。
稍后将学习如何使用自定义颜色循环。

关于颜色图，我们已经非常熟悉用于热力图和散点图的 `colormap`。下面展示的是，颜色图也可以像颜色那样通过 `Symbol` 或 `String` 进行指定。
此外，也可以是 `RGB` 颜色的向量。
下面是第一个例子，通过 `Symbol`， `String` 和分类值的 `cgrad` 来指定颜色图。
输入 `?cgrad` 查看更多信息。

```jl
scolor = """
    CairoMakie.activate!() # hide
    figure = (; resolution=(600, 400), font="CMU Serif")
    axis = (; xlabel=L"x", ylabel=L"y", aspect=DataAspect())
    fig, ax, pltobj = heatmap(rand(20, 20); colorrange=(0, 1),
        colormap=Reverse(:viridis), axis=axis, figure=figure)
    Colorbar(fig[1, 2], pltobj, label = "Reverse colormap Sequential")
    fig
    label = "Reverse_colormap_sequential" # hide
    caption = "Reverse colormap sequential and colorrange." # hide
    link_attributes = "width=60%" # hide
    Options(fig; filename=label, label, caption, link_attributes) # hide
    """
sco(scolor)
```

当设置 `colorrange` 后，超出此范围的颜色值会被相应地设置为颜色图的第一种和最后一种颜色。
但是，有时最好自行指定两端的颜色。这可以通过 `highclip` 和 `lowclip` 实现：

```
using ColorSchemes
```

```jl
s = """
    CairoMakie.activate!() # hide
    figure = (; resolution=(600, 400), font="CMU Serif")
    axis = (; xlabel=L"x", ylabel=L"y", aspect=DataAspect())
    fig, ax, pltobj=heatmap(randn(20, 20); colorrange=(-2, 2),
        colormap="diverging_rainbow_bgymr_45_85_c67_n256",
        highclip=:black, lowclip=:white, axis=axis, figure=figure)
    Colorbar(fig[1, 2], pltobj, label = "Diverging colormap")
    fig
    label = "diverging_colormap" # hide
    caption = "Diverging Colormap with low and high clip." # hide
    link_attributes = "width=60%" # hide
    Options(fig; filename=label, label, caption, link_attributes) # hide
    """
sco(s)
```

另外 `RGB` 向量也是合法的选项。
在下面的例子中， 你可以传递一个自定义颜色图 _perse_ 或使用 `cgrad` 来创建分类值的 `Colorbar`。

```
using Colors, ColorSchemes
```

```jl
scat = """
    CairoMakie.activate!() # hide
    figure = (; resolution=(600, 400), font="CMU Serif")
    axis = (; xlabel=L"x", ylabel=L"y", aspect=DataAspect())
    cmap = ColorScheme(range(colorant"red", colorant"green", length=3))
    mygrays = ColorScheme([RGB{Float64}(i, i, i) for i in [0.0, 0.5, 1.0]])
    fig, ax, pltobj = heatmap(rand(-1:1, 20, 20);
        colormap=cgrad(mygrays, 3, categorical=true, rev=true), # cgrad and Symbol, mygrays,
        axis=axis, figure=figure)
    cbar = Colorbar(fig[1, 2], pltobj, label="Categories")
    cbar.ticks = ([-0.66, 0, 0.66], ["-1", "0", "1"])
    fig
    label = "categorical_colormap" # hide
    caption = "Categorical Colormap." # hide
    link_attributes = "width=60%" # hide
    Options(fig; filename=label, label, caption, link_attributes) # hide
    """
sco(scat)
```

最后，分类值的颜色栏标签默认不在每种颜色间居中。
添加自定义标签可修复此问题，即 `cbar.ticks = (positions, ticks)`。
最后一种情况是传递颜色的元组给 `colormap`，其中颜色可以通过 `Symbol`， `String` 或它们的混合指定。
然后将会得到这两组颜色间的插值颜色图。

另外，也支持十六进制编码的颜色作为输入。因此作为示范，下例将在热力图上放置一个半透明的标记。

```jl
s2color2 = """
    CairoMakie.activate!() # hide
    figure = (; resolution=(600, 400), font="CMU Serif")
    axis = (; xlabel=L"x", ylabel=L"y", aspect=DataAspect())
    fig, ax, pltobj = heatmap(rand(20, 20); colorrange=(0, 1),
        colormap=(:red, "black"), axis=axis, figure=figure)
    scatter!(ax, [11], [11], color=("#C0C0C0", 0.5), markersize=150)
    Colorbar(fig[1, 2], pltobj, label="2 colors")
    fig
    label = "colormap_two_colors" # hide
    caption = "Colormap from two colors." # hide
    link_attributes = "width=60%" # hide
    Options(fig; filename=label, label, caption, link_attributes) # hide
    """
sco(s2color2)
```

### 自定义颜色循环

可以通过新的颜色循环定义全局 `Theme` ，但通常 **不建议** 这样做。
更好的做法是定义新的主题并像上节那样使用它。
定义带有 `:color`， `:linestyle`， `:marker` 属性的新 `cycle` 和默认的 `colormap` 。
下面为之前的 `publication_theme` 增加一些新的属性。

```jl
@sc new_cycle_theme()
```

然后将它应用到绘图函数中，如下所示:

```jl
@sc scatters_and_lines()
```

```jl
s = """
    CairoMakie.activate!() # hide
    with_theme(scatters_and_lines, new_cycle_theme())
    label = "custom_cycle" # hide
    caption = "Custom theme with new cycle and colormap." # hide
    link_attributes = "width=60%" # hide
    Options(current_figure(); filename=label, caption, label, link_attributes) # hide
    """
sco(s)
```

此时，通过颜色，曲线样式，标记和颜色图，你已经能够 **完全控制** 绘图结果。
下一部分将讨论如何管理和控制 **布局**。

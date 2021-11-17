## CairoMakie.jl {#sec:cairomakie}

我们开始绘制的第一张图是标注了散点的直线：

```
using CairoMakie
CairoMakie.activate!()
```

```jl
s = """
    CairoMakie.activate!() # hide
    fig = scatterlines(1:10, 1:10)
    label = "firstplot" # hide
    caption = "First plot." # hide
    link_attributes = "width=60%" # hide
    Options(fig; filename=label, caption, label, link_attributes) # hide
    """
sco(s)
```

注意前面的图采用默认输出样式，因此需要使用轴名称和轴标签进一步调整。

同时注意每一个像 `scatterlines` 这样的绘图函数都创建了一个 `FigureAxisPlot` 集合，其中包含 `Figure`， `Axis` 和 `plot` 对象。
这些函数也被称为 `non-mutating` 方法。
另一方面， `mutating` 方法（例如 `scatterlines!`，注意多了 `!`) 仅返回一个 plot 对象，它可以被添加到给定的 `axis` 或 `current_figure()` 中。

下一个问题是如何改变颜色或标记的类型？
这可以通过 `attributes` 实现， 将在下一节讨论。

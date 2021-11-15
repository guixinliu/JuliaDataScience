## Groupby 和 Combine {#sec:groupby_combine}

在 R 编程语言中，@wickham2011split 推广了用于数据转换的 split-apply-combine 模式。
在该模式中，我们先将数据 **split** 成不同组，然后对每一组 **apply** 一个或多个函数，最后 **combine** 每组的结果。
`DataFrames.jl` 完全支持 split-apply-combine 模式 。
本节使用之前的学生成绩数据作为示例。
假设想获得每个学生的平均成绩：

```jl
@sco process=without_caption_label all_grades()
```

按照该模式，先将数据集按照学生名称 **split** 为不同组，其次对每组数据 **apply** 均值函数，然后 **combine** 每组的结果。

在 split 步骤中使用的函数为 `groupby`，并将函数的第二个参数列 ID 指定为数据集分割的条件。

```jl
s = "groupby(all_grades(), :name)"
sco(s; process=string, post=plainblock)
```

`mean` 函数来自 Julia 标准库中的 `Statistics` 模块:

```
using Statistics
```

应用此函数时，需调用 `combine` 函数：

```jl
s = """
    gdf = groupby(all_grades(), :name)
    combine(gdf, :grade => mean)
    """
sco(s; process=without_caption_label)
```

想象一下，如果没有 `groupby` 和 `combine` 函数，则需按照下文这样做。
我们必须循环遍历数据以将其分割为多组，然后循环遍历每组以应用函数， **以及**循环遍历每组以收集最终结果。  
因此，split-apply-combine 模式是值得掌握的技术。

### Multiple Source Columns {#sec:groupby_combine_multiple_source}

但如果我们想将一个函数应用到多列数据，该如何操作？

```jl
s = """
    group = [:A, :A, :B, :B]
    X = 1:4
    Y = 5:8
    df = DataFrame(; group, X, Y)
    """
sco(s; process=without_caption_label)
```

操作与之前类似：

```jl
s = """
    gdf = groupby(df, :group)
    combine(gdf, [:X, :Y] .=> mean; renamecols=false)
    """
sco(s; process=without_caption_label)
```

注意到，我们在右箭头 `=>` 前使用了 `.` 点运算符，这表示 `mean` 函数将应用到多个列 `[:X, :Y]`。

要在`combine`中使用组合函数，一种简单的方法是创建一个函数来执行预期的组合变换。
例如，对于一组数据，在先应用 `mean`后调用 `round` 对值取整（即 `Int` ）。

```jl
s = """
    gdf = groupby(df, :group)
    rounded_mean(data_col) = round(Int, mean(data_col))
    combine(gdf, [:X, :Y] .=> rounded_mean; renamecols=false)
    """
sco(s; process=without_caption_label)
```

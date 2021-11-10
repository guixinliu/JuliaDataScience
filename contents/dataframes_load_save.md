## 加载和保存文件 {#sec: load_save}

仅在 Julia 程序中使用数据而不能加载或保存数据是非常局限的。
因此，我们开始讨论如何存储文件到硬盘和从硬盘读取文件。
我们关注 CSV 和 Excel 文件格式，分别参见 @sec:csv 和 @sec:excel，因为这些是表格数据最常见的数据格式。

### CSV {#sec:csv}

**C**omma-**s**eparated-**v**alues (CSV) 文件是非常有效的表格存储方式。
CSV 文件相比其他数据存储文件有两点优势。首先，它准确实现了名称所对应的功能，即在存储值时使用逗号`,`分隔。此首字母缩写词也用作其文件扩展名。因此，请确保使用“.csv”扩展名（例如“myfile.csv”）保存文件。为了演示 CSV 文件的结构，我们安装 [`CSV.jl`](http://csv.juliadata.org/latest/) 包：

```
julia> ]

pkg> add CSV
```

并且通过以下方式导入：

```
using CSV
```

现在我们可以使用先前的数据：

```jl
sco("
grades_2020()
"; process=without_caption_label)
```

并在写入后从文件中读取：

```jl
@sc write_grades_csv()
```

```jl
sco("""
JDS.output_block_inside_tempdir() do # hide
path = write_grades_csv()
read(path, String)
end # hide
""")
```

在这里，我们还看到了 CSV 数据格式的第二个好处：可以使用简单的文本编辑器读取数据。
这与许多需要专有软件的替代数据格式不同，例如 Excel。

这很有效，但是如果我们的数据 ** 包含逗号 `,`** 作为值怎么办？
如果我们天真地用逗号写入数据，那么文件将很难转换回表格。
幸运的是，`CSV.jl` 会自动为我们处理。
考虑以下带逗号`,`的数据：

```jl
@sco grades_with_commas()
```

如果写入文件，我们得到：

```jl
sco("""
JDS.output_block_inside_tempdir() do # hide
function write_comma_csv()
    path = "grades-commas.csv"
    CSV.write(path, grades_with_commas())
end
path = write_comma_csv()
read(path, String)
end # hide
""")
```

因此，`CSV.jl` 在包含逗号的值周围添加引号 `"`。
解决此问题的另一种常见方法是将数据写入 **t**ab-**s**eparated **v**alues (TSV) 文件格式。
该格式假设数据不包含制表符，这在大多数情况下成立。

另请注意，也可以使用简单的文本编辑器读取 TSV 文件，这些文件使用“.tsv”扩展名。

```jl
sco("""
JDS.output_block_inside_tempdir() do # hide
function write_comma_tsv()
    path = "grades-comma.tsv"
    CSV.write(path, grades_with_commas(); delim='\\t')
end
read(write_comma_tsv(), String)
end # hide
""")
```
像 CSV 和 TSV 这样的文本文件格式还可以使用其他分割符，例如分号“;”，空格“\ ”，甚至是像“π”这样不寻常的字符。

```jl
sco("""
JDS.output_block_inside_tempdir() do # hide
function write_space_separated()
    path = "grades-space-separated.csv"
    CSV.write(path, grades_2020(); delim=' ')
end
read(write_space_separated(), String)
end # hide
""")
```

按照惯例，最好还是为文件给定特殊的分隔符，例如“;”用于“.csv”扩展名。

使用 `CSV.jl` 加载 CSV 文件的方式与此类似。
您可以使用 `CSV.read` 并指定您想要的输出格式。
我们指定为`DataFrame`。

```jl
sco("""
JDS.inside_tempdir() do # hide
path = write_grades_csv()
CSV.read(path, DataFrame)
end # hide
"""; process=without_caption_label)
```

方便地，`CSV.jl`会自动为我们推断列类型：

```jl
sco("""
JDS.inside_tempdir() do # hide
path = write_grades_csv()
df = CSV.read(path, DataFrame)
end # hide
"""; process=string, post=output_block)
```

它甚至适用于更复杂的数据：

```jl
sco("""
JDS.inside_tempdir() do # hide
my_data = \"\"\"
    a,b,c,d,e
    Kim,2018-02-03,3,4.0,2018-02-03T10:00
    \"\"\"
path = "my_data.csv"
write(path, my_data)
df = CSV.read(path, DataFrame)
end # hide
"""; process=string, post=output_block)
```

这些CSV基础应该涵盖大多数用例。
关于更多信息，请参阅[`CSV.jl` 文档](https://csv.juliadata.org/stable)尤其是[`CSV.File` 构建 docstring](https://csv.juliadata.org/stable/#CSV.File)。


### Excel {#sec:excel}

有多个 Julia 包可以读取 Excel 文件。
在本书中，我们将只讨论 [`XLSX.jl`](https://github.com/felipenoris/XLSX.jl)，因为它是 Julia 生态系统中处理 Excel 数据的最积极维护的包。
第二个好处是，`XLSX.jl` 是用纯 Julia 编写的，这使得我们可以轻松地检查和理解幕后发生的事情。

加载 `XLSX.jl` 的方式是

```
using XLSX:
    eachtablerow,
    readxlsx,
    writetable
```

为了写入文件，我们为数据和列名定义了一个辅助函数：

```jl
@sc write_xlsx("", DataFrame())
```

现在，我们可以轻松地将成绩写入 Excel 文件：

```jl
@sc write_grades_xlsx()
```

当成绩被读取回来时，我们将看到 `XLSX.jl` 将数据放在 `XLSXFile` 类型中，我们可以像访问 `Dict` 一样访问所需的 `sheet`：

```jl
sco("""
JDS.inside_tempdir() do # hide
path = write_grades_xlsx()
xf = readxlsx(path)
end # hide
""")
```

```jl
s = """
    JDS.inside_tempdir() do # hide
    xf = readxlsx(write_grades_xlsx())
    sheet = xf["Sheet1"]
    eachtablerow(sheet) |> DataFrame
    end # hide
    """
sco(s; process=without_caption_label)
```

请注意，我们只介绍了`XLSX.jl`的基础知识，但它还提供了更强大的用法和自定义功能。
有关更多信息和选项，请参阅[`XLSX.jl` 文档](https://felipenoris.github.io/XLSX.jl/stable/).

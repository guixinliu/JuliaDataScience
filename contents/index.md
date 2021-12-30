# Welcome {-}

```{=comment}
This file is only included on the website.
```

Welcome! 这是一本关于 **[Julia](https://julialang.org) 数据科学** 的开放获取书籍，同时源代码开源。
我们的目标读者是来自应用科学各个领域的研究人员。
当然，我们也希望能对工业界有用。
你可以使用键盘上的箭头键（左/右）浏览电子书。

此译本主要由 [@guixinliu](https://github.com/guixinliu) 完成， [@findmyway](https://github.com/findmyway) 提供了审校, 其源码公开在 [GitHub](https://github.com/JuliaCN/JuliaDataScience){target="_blank"}，如果你在阅读过程中有任何问题和建议，欢迎前往创建issue。 此外，你也可以下载[PDF版的中文译本](https://cn.julialang.org/JuliaDataScience/juliadatascience.pdf)方便离线阅读。

本书的英文版可以在其官网[在线阅读](https://juliadatascience.io/)或者获取[英文PDF版](https://juliadatascience.io/juliadatascience.pdf)离线阅读。此外，本书也同时发布在 [Amazon.com](https://www.amazon.com/dp/B09KMRKQ96/)。

如果你想得到有关英文版的更新通知，请考虑**注册更新**：

```{=html}
<form style="margin: 0 auto;" action="https://api.staticforms.xyz/submit" method="post">
    <input type="hidden" name="accessKey" value="2b78f325-fb4e-44e1-ad2f-4dc714ac402f">
    <input type="email" name="email">
    <input type="hidden" name="redirectTo" value="https://juliadatascience.io/thanks">
    <input type="submit" value="Submit" />
</form>
```

### 引用信息 {-}

可使用如下条目引用本书的内容：

```plaintext
Storopoli, Huijzer and Alonso (2021). Julia Data Science. https://juliadatascience.io. ISBN: 9798489859165.
```

或以 BibTeX 格式：

```plaintext
@book{storopolihuijzeralonso2021juliadatascience,
  title = {Julia Data Science},
  author = {Jose Storopoli and Rik Huijzer and Lazaro Alonso},
  url = {https://juliadatascience.io},
  year = {2021},
  isbn = {9798489859165}
}
```

### 封面 {-}

```jl
let
    fig = front_cover()
    # Use lazy loading to keep homepage speed high.
    link_attributes = """loading="lazy" width=80%"""
    # When changing this name, also change the link in README.md.
    # This doesn't work for some reason; I need to fix it.
    filename = "frontcover"
    Options(fig; filename, label=filename)
end
```


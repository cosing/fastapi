# Python 类型提示简介

**Python 3.6+ 版本**加入了对"类型提示"的支持。

这些**"类型提示"**是一种新的语法（在 Python 3.6 版本加入）用来声明一个变量的<abbr title="例如：str、int、float、bool">类型</abbr>。

通过声明变量的类型，编辑器和一些工具能给你提供更好的支持。

这只是一个关于 Python 类型提示的**快速入门 / 复习**。它仅涵盖与 **FastAPI** 一起使用所需的最少部分...实际上只有很少一点。

整个 **FastAPI** 都基于这些类型提示构建，它们带来了许多优点和好处。

但即使你不会用到 **FastAPI**，了解一下类型提示也会让你从中受益。

/// note

如果你已经精通 Python，并且了解关于类型提示的一切知识，直接跳到下一章节吧。

///

## 动机

让我们从一个简单的例子开始：

{* ../../docs_src/python_types/tutorial001.py *}


运行这段程序将输出：

```
John Doe
```

这个函数做了下面这些事情：

* 接收 `first_name` 和 `last_name` 参数。
* 通过 `title()` 将每个参数的第一个字母转换为大写形式。
* 中间用一个空格来<abbr title="将它们按顺序放置组合成一个整体。">拼接</abbr>它们。

{* ../../docs_src/python_types/tutorial001.py hl[2] *}


### 修改示例

这是一个非常简单的程序。

现在假设你将从头开始编写这段程序。

在某一时刻，你开始定义函数，并且准备好了参数...。

现在你需要调用一个"将第一个字母转换为大写形式的方法"。

等等，那个方法是什么来着？`upper`？还是 `uppercase`？`first_uppercase`？`capitalize`？

然后你尝试向程序员老手的朋友——编辑器自动补全寻求帮助。

输入函数的第一个参数 `first_name`，输入点号（`.`）然后敲下 `Ctrl+Space` 来触发代码补全。

但遗憾的是并没有起什么作用：

<img src="https://fastapi.tiangolo.com/img/python-types/image01.png">

### 添加类型

让我们来修改上面例子的一行代码。

我们将把下面这段代码中的函数参数从：

```Python
    first_name, last_name
```

改成：

```Python
    first_name: str, last_name: str
```

就是这样。

这些就是"类型提示"：

{* ../../docs_src/python_types/tutorial002.py hl[1] *}


这和声明默认值是不同的，例如：

```Python
    first_name="john", last_name="doe"
```

这两者不一样。

我们用的是冒号（`:`），不是等号（`=`）。

而且添加类型提示一般不会改变原来的运行结果。

现在假设我们又一次正在创建这个函数，这次添加了类型提示。

在同样的地方，通过 `Ctrl+Space` 触发自动补全，你会发现：

<img src="https://fastapi.tiangolo.com/img/python-types/image02.png">

这样，你可以滚动查看选项，直到你找到看起来眼熟的那个：

<img src="https://fastapi.tiangolo.com/img/python-types/image03.png">

## 更多动机

下面是一个已经有类型提示的函数：

{* ../../docs_src/python_types/tutorial003.py hl[1] *}


因为编辑器已经知道了这些变量的类型，所以不仅能对代码进行补全，还能检查其中的错误：

<img src="https://fastapi.tiangolo.com/img/python-types/image04.png">

现在你知道了必须先修复这个问题，通过 `str(age)` 把 `age` 转换成字符串：

{* ../../docs_src/python_types/tutorial004.py hl[2] *}


## 声明类型

你刚刚看到的就是声明类型提示的主要场景。用于函数的参数。

这也是你将在 **FastAPI** 中使用它们的主要场景。

### 简单类型

不只是 `str`，你能够声明所有的标准 Python 类型。

比如以下类型：

* `int`
* `float`
* `bool`
* `bytes`

{* ../../docs_src/python_types/tutorial005.py hl[1] *}


### 带类型参数的泛型类型 

有些容器数据结构可以包含其他的值，比如 `dict`、`list`、`set` 和 `tuple`。它们内部的值也会拥有自己的类型。

这些具有内部类型的类型被称为 “**泛型**” 类型。

你可以使用 Python 的 `typing` 标准库来声明这些类型以及子类型。它专门用来支持这些类型提示。

#### Python新版本的表现

使用 `typing` 语法与所有版本**兼容** ，从 Python 3.6 到最新版本，包括 Python 3.9、Python 3.10 等。

随着 Python 的发展，**新版本**的 Python 对这些类型注解提供了更好的支持，在许多情况下，你甚至不需要导入和使用 `typing` 模块来声明类型注解。

如果你的项目可以选择较新版本的 Python，你就能享受到它带来的额外便利。

所有文档中都有与每个 Python 版本兼容的示例（如果有差异的话）。

例如，**“Python 3.6+”** 表示它与 Python 3.6 或更高版本兼容（包括 3.7、3.8、3.9、3.10 等）。而 **“Python 3.9+”** 表示它与 Python 3.9 或更高版本兼容（包括 3.10 等）。

如果你可以使用 **最新版本的 Python**，请使用最新版本的示例，这些示例将具有**最好、最简单的语法**，例如，**“Python 3.10+”**。

#### 列表

例如，让我们来定义一个由 `str` 组成的 `list` 变量。

//// tab | Python 3.9+

Declare the variable, with the same colon (`:`) syntax.输入 `list` 作为类型。

由于列表是带有"子类型"的类型，所以我们把子类型放在方括号中：

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial006_py39.py!}
```

////

//// tab | Python 3.8+

从 `typing` 模块导入 `List`（注意是大写的 `L`）：

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial006.py!}
```

同样以冒号（`:`）来声明这个变量。

输入 `List` 作为类型。

由于列表是带有"子类型"的类型，所以我们把子类型放在方括号中：

```Python hl_lines="4"
{!> ../../docs_src/python_types/tutorial006.py!}
```

////

/// info

方括号中的内部类型被称为“类型参数”。

在本例中，`str` 是传递给 `List`（或 Python 3.9 及更高版本中的 `list`）的类型参数。

///

这表示："变量 `items` 是一个 `list`，并且这个列表里的每一个元素都是 `str`"。

/// tip

如果你使用的是 Python 3.9 或更高版本，则无需从 `typing` 导入 `List`，可以直接使用常规的 `list` 类型。

///

这样，即使在处理列表中的元素时，你的编辑器也可以提供支持。

没有类型，几乎是不可能实现下面这样：

<img src="https://fastapi.tiangolo.com/img/python-types/image05.png">

注意，变量 `item` 是列表 `items` 中的元素之一。

而且，编辑器仍然知道它是一个 `str`，并为此提供了支持。

#### 元组和集合

声明 `tuple` 和 `set` 的方法也是一样的：

//// tab | Python 3.9+

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial007_py39.py!}
```

////

//// tab | Python 3.8+

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial007.py!}
```

////

这表示：

* 变量 `items_t` 是一个 `tuple`，其中的前两个元素都是 `int` 类型, 最后一个元素是 `str` 类型。
* 变量 `items_s` 是一个 `set`，其中的每个元素都是 `bytes` 类型。

#### 字典

定义 `dict` 时，需要传入两个子类型，用逗号进行分隔。

第一个子类型声明 `dict` 的所有键。

第二个子类型声明 `dict` 的所有值：

//// tab | Python 3.9+

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial008_py39.py!}
```

////

//// tab | Python 3.8+

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial008.py!}
```

////

这表示：

* 变量 `prices` 是一个 `dict`：
    * 这个 `dict` 的所有键为 `str` 类型（可以看作是字典内每个元素的名称）。
    * 这个 `dict` 的所有值为 `float` 类型（可以看作是字典内每个元素的价格）。

#### 联合（Union）类型 { #union }

你可以声明一个变量可以是**若干类型**中的**任意一种**，例如，一个 `int` 或一个 `str`。

在 Python 3.6 及以上版本（包括 Python 3.10），你可以使用来自 `typing` 模块的 `Union` 类型，并在方括号内放入它可能接受的类型。

在 Python 3.10 中还有一个**新语法**，你可以用一个 <abbr title='也称为“按位或运算符”，但此处的含义不相关'>竖线（`|`）</abbr> 来分隔可能的类型。

//// tab | Python 3.10+

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial008b_py310.py!}
```

////

//// tab | Python 3.8+

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial008b.py!}
```

////

在这两种情况下，这都意味着 `item` 可以是一个 `int` 或一个 `str` 。

#### 可能的 `None`

特殊的，但一个变量可以是 `str` 或 `None` 时，我们可以使用 `Optional[str]` 而不是仅仅 `str`，可以让编辑器帮助你检测错误，避免你假设一个值总是 `str` 类型，而实际上它也可能是 `None`。

`Optional[Something]` 实际上是 `Union[Something, None]` 的简写，它们是等价的。

这也意味着在 Python 3.10 中，你可以使用 `Something | None`：

//// tab | Python 3.10+

```Python hl_lines="1"
{!> ../../docs_src/python_types/tutorial009_py310.py!}
```

////

//// tab | Python 3.8+

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial009.py!}
```

////

//// tab | Python 3.8+ alternative

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial009b.py!}
```

////

#### 使用 `Union` 还是 `Optional` ？

如果你使用的 Python 版本低于 3.10，这里有一个来自我**非常主观**角度的建议：

* 🚨 避免使用 `Optional[SomeType]`
* 而是 ✨ **使用 `Union[SomeType, None]`** ✨。

两者是等价的，底层实现也相同，但我推荐使用 `Union` 而不是 `Optional`，因为 "**optional**"（可选的）这个词似乎暗示该值是可选的，而它实际上的意思是“它可以是 `None`”，即使它并不是可选的而是仍然必需的参数。

我认为 `Union[SomeType, None]` 对于其含义的表达更为明确。

这只是关于用词和命名的问题。但这些词汇会影响你和你的队友对代码的理解方式。

举个例子，我们来看这个函数：

{* ../../docs_src/python_types/tutorial009c.py hl[1,4] *}

参数 `name` 被定义为 `Optional[str]`，但它**不是可选的**，你不能在不传该参数的情况下调用此函数：

```Python
say_hi()  # 哦，不，这会抛出错误！ 😱
```

`name` 参数**仍然是必需的**（不是*可选的*），因为它没有默认值。不过，`name` 接受 `None` 作为值：

```python
say_hi(name=None)  # 这行得通，None 是有效的 🎉
```

好消息是，一旦你使用了 Python 3.10，你就不必担心这个问题了，因为你将可以直接使用 `|` 来定义联合类型：

{* ../../docs_src/python_types/tutorial009c_py310.py hl[1,4] *}

这样你就不用再担心像 `Optional` 和 `Union` 这样的命名了。😎

#### 泛型总结

这些在方括号中接受类型参数的类型被称为 **泛型类型（Generic types）** 或 **泛型（Generics）**，例如：

//// tab | Python 3.10+

你可以使用同样的内置类型作为泛型（使用方括号并在内部指定类型）：

* `list`
* `tuple`
* `set`
* `dict`

并且也可以使用与 Python 3.8 相同的、来自 `typing` 模块的类型：

* `Union`
* `Optional` (与 Python 3.8 相同)
* ...以及其他类型。

在 Python 3.10 中，作为使用 `Union` 和 `Optional` 泛型的替代方案，你可以使用 <abbr title='也称为“按位或运算符”，但此处的含义不相关'>竖线（`|`）</abbr> 来声明类型的联合，这要更好、更简单得多。

////

//// tab | Python 3.9+

你可以使用同样的内置类型作为泛型（使用方括号并在内部指定类型）：

* `list`
* `tuple`
* `set`
* `dict`

并且也可以使用与 Python 3.8 相同的、来自 `typing` 模块的类型：

* `Union`
* `Optional`
* ...以及其他类型。

////

//// tab | Python 3.8+

* `List`
* `Tuple`
* `Set`
* `Dict`
* `Union`
* `Optional`
* ...以及其他类型。

////

### 类作为类型

你也可以将类声明为变量的类型。

假设你有一个名为 `Person` 的类，拥有 name 属性：

{* ../../docs_src/python_types/tutorial010.py hl[1:3] *}


接下来，你可以将一个变量声明为 `Person` 类型：

{* ../../docs_src/python_types/tutorial010.py hl[6] *}


然后，你将再次获得所有的编辑器支持：

<img src="https://fastapi.tiangolo.com/img/python-types/image06.png">

注意，这意味着`one_person` 是 `Person` 类的**实例**。而不是`Person` 类本身。 

## Pydantic 模型

<a href="https://docs.pydantic.dev/" class="external-link" target="_blank">Pydantic</a> 是一个用来执行数据校验的 Python 库。

你可以将数据的"结构"声明为具有属性的类。

每个属性都拥有类型。

接着你用一些值来创建这个类的实例，这些值会被校验，并被转换为适当的类型（在需要的情况下），返回一个包含所有数据的对象。

然后，你将获得这个对象的所有编辑器支持。

下面的例子来自 Pydantic 官方文档：

//// tab | Python 3.10+

```Python
{!> ../../docs_src/python_types/tutorial011_py310.py!}
```

////

//// tab | Python 3.9+

```Python
{!> ../../docs_src/python_types/tutorial011_py39.py!}
```

////

//// tab | Python 3.8+

```Python
{!> ../../docs_src/python_types/tutorial011.py!}
```

////


/// info

想进一步了解 <a href="https://docs.pydantic.dev/" class="external-link" target="_blank">Pydantic，请阅读其文档</a>.

///

整个 **FastAPI** 建立在 Pydantic 的基础之上。

实际上你将在 [教程 - 用户指南](tutorial/index.md){.internal-link target=_blank} 看到很多这种情况。

## 带有元数据注解的类型提示 { #type-hints-with-metadata-annotations }

Python 还有一个特性，允许使用 `Annotated` 在这些类型提示中添加**额外的 <abbr title="关于数据的数据，在这个例子中，是关于类型的信息，例如描述。">元数据</abbr>**。

//// tab | Python 3.9+

在 Python 3.9 中，`Annotated` 是标准库的一部分，因此你可以从 `typing` 导入它。

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial013_py39.py!}
```

////

//// tab | Python 3.8+

在低于 Python 3.9 的版本中，你需要从 `typing_extensions` 导入 `Annotated`。

它通常会随 **FastAPI** 一起安装。

```Python hl_lines="1  4"
{!> ../../docs_src/python_types/tutorial013.py!}
```

////

Python 本身不会对这个 `Annotated` 执行任何操作。对于编辑器和其他工具而言，其类型仍然是 `str`。

但是，你可以利用 `Annotated` 中的这个空间，为 **FastAPI** 提供关于你希望应用程序如何运行的额外元数据。

需要记住的重要一点是，传递给 `Annotated` 的**第一个*类型参数***是**实际类型**。其余的都只是供其他工具使用的元数据。

现在，你只需要知道 `Annotated` 存在，并且它是标准的 Python 特性即可。😎

稍后你就会看到它能有多**强大**。

/// tip

这是**标准 Python** 特性的事实意味着你仍将在你的编辑器、你用于分析和重构代码的工具等中获得**最佳的开发者体验**。✨

这也意味着你的代码将与许多其他 Python 工具和库具有很好的兼容性。🚀

///

## **FastAPI** 中的类型提示

**FastAPI** 利用这些类型提示来做下面几件事。

使用 **FastAPI** 时用类型提示声明参数可以获得：

* **编辑器支持**。
* **类型检查**。

...并且 **FastAPI** 还会用这些类型声明来：

* **定义参数要求**：声明对请求路径参数、查询参数、请求头、请求体、依赖等的要求。
* **转换数据**：将来自请求的数据转换为需要的类型。
* **校验数据**： 对于每一个请求：
    * 当数据校验失败时自动生成**错误信息**返回给客户端。
* 使用 OpenAPI **文档化** API：
    * 用于自动生成交互式文档的用户界面。

听上去有点抽象。不过不用担心。你将在 [教程 - 用户指南](tutorial/index.md){.internal-link target=_blank} 中看到所有的实战。

最重要的是，通过使用标准的 Python 类型，只需要在一个地方声明（而不是添加更多的类、装饰器等），**FastAPI** 会为你完成很多的工作。

/// info

如果你已经阅读了所有教程，回过头来想了解有关类型的更多信息，<a href="https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html" class="external-link" target="_blank">来自 `mypy` 的"速查表"</a>是不错的资源。

///

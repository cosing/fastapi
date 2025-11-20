# 查询参数和字符串校验

**FastAPI** 允许你为参数声明额外的信息和校验。

让我们以下面的应用程序为例：

{* ../../docs_src/query_params_str_validations/tutorial001_py310.py hl[7] *}

查询参数 `q` 的类型为 `str`，默认值为 `None`，因此它是可选的。

## 额外的校验

我们打算添加约束条件：即使 `q` 是可选的，但只要提供了该参数，则该参数值**不能超过50个字符的长度**。

### 导入 `Query` 和 `Annotated`

首先导入：

* `Query` 从 `fastapi`
* `Annotated` 从 `typing`

{* ../../docs_src/query_params_str_validations/tutorial002_an_py310.py hl[1,3] *}

/// info

FastAPI 在 0.95.0 版本中增加了对 `Annotated` 的支持（并开始推荐使用）。

如果您使用的是旧版本，则在使用 `Annotated` 时会遇到错误。

使用 `Annotated`之前，请确保 [将 FastAPI 版本升级](../deployment/versions.md#upgrading-the-fastapi-versions){.internal-link target=_blank} 到至少 0.95.1。

///

## 在 q 参数的类型中使用 `Annotated`

还记得我在 [Python 类型介绍](../python-types.md#type-hints-with-metadata-annotations){.internal-link target=_blank} 中告诉过你 `Annotated` 可以用于为参数添加元数据吗？

现在是时候在 FastAPI 中使用它了。🚀

我们有这样的类型注解：

//// tab | Python 3.10+

```Python
q: str | None = None
```

////

//// tab | Python 3.8+

```Python
q: Union[str, None] = None
```

////

我们将用 `Annotated` 包装它，所以它变成：

//// tab | Python 3.10+

```Python
q: Annotated[str | None] = None
```

////

//// tab | Python 3.8+

```Python
q: Annotated[Union[str, None]] = None
```

////

这两个版本的意思相同， `q` 是一个参数，可以是 `str` 或 `None` ，默认情况下为 `None` 。

现在让我们进入精彩部分吧！🎉

## 在 `q` 的 `Annotated`注解中添加 `Query`

现在我们有了这个 `Annotated` ，我们可以在其中添加更多信息（在本例中是一些额外的验证），在 `Annotated` 字段中添加 `Query` ，并将参数 `max_length` 设置为 `50` ：

{* ../../docs_src/query_params_str_validations/tutorial002_an_py310.py hl[9] *}

请注意，默认值仍然是 `None` ，因此该参数仍然是可选的。

但是现在，在 `Annotated` 中添加 `Query(max_length=50)` ，就等于告诉 FastAPI 我们希望它对这个值进行**额外的验证** ，即最大长度为 50 个字符。😎

/// tip

这里我们使用 `Query()` 是因为这是一个**查询参数** 。稍后我们将看到其他函数，例如 `Path()` 、 `Body()` 、 `Header()` 和 `Cookie()` ，它们也接受与 `Query()` 相同的参数。

///

FastAPI 现在将：

* * **校验**数据，确保最大长度为 50 个字符
* 当数据无效时，向客户端显示**清晰的错误信息**
* 在 OpenAPI 模式的*路径操作*中**记录**该参数（因此它将显示在**自动文档 UI**中）

## 备选方案（旧）：使用 `Query` 作为默认值

现在，将 `Query` 用作查询参数的默认值，并将它的 `max_length` 参数设置为 50：

{* ../../docs_src/query_params_str_validations/tutorial002.py hl[9] *}

由于我们必须用 `Query(default=None)` 替换默认值 `None`，`Query` 的第一个参数同样也是用于定义默认值。

所以：

```Python
q: Union[str, None] = Query(default=None)
```

...使得参数可选，等同于：

```Python
q: str = None
```

但是 `Query` 显式地将其声明为查询参数。

然后，我们可以将更多的参数传递给 `Query`。在本例中，适用于字符串的 `max_length` 参数：

```Python
q: Union[str, None] = Query(default=None, max_length=50)
```

将会校验数据，在数据无效时展示清晰的错误信息，并在 OpenAPI 模式的*路径操作*中记录该参​​数。

### 该用 `Query` 作为默认值还是 `Annotated` { #query-as-the-default-value-or-in-annotated }

请记住，在 `Annotated` 中使用 `Query` 时，不能使用 `Query` 的 `default` 参数。

请改用函数参数的实际默认值。否则，结果将不一致。

例如，以下行为是不允许的：

```Python
q: Annotated[str, Query(default="rick")] = "morty"
```

因为还不清楚默认值应该是 `"rick"` 还是 `"morty"` 。

所以，你最好使用：

```Python
q: Annotated[str, Query()] = "rick"
```

或者在较旧的代码库中，你会发现：

```Python
q: str = Query(default="rick")
```

### `Annotated`的优势

建议在函数参数中**使用 `Annotated` 参数**，而不是默认值，这样做有很多**好处** 。🤓

函数参数的默认值就是**实际的**默认值，这在 Python 中通常更直观。😌

即使不使用 FastAPI，你也可以在**其他地方调用**同一个函数，它也能**正常**工作 。如果存在**必需**参数（且没有默认值），你的**编辑器**会报错；如果你在运行函数时没有传递必需参数， **Python** 也会发出警告。

当你不使用 `Annotated` 而使用 **（旧的）默认值样式**时，如果在其他地方调用该函数时未使用 FastAPI，则必须记住将参数传递给该函数才能使其正常工作，否则返回的值将与预期不同（例如， `QueryInfo` 或类似类型而不是 `str` ）。你的编辑器不会报错，Python 也不会在运行该函数时报错，只有当函数内部的操作出错时才会报错。

由于 `Annotated` 可以包含多个元数据注释，您现在甚至可以将相同的功能与其他工具 一起使用（例如 <a href="https://typer.tiangolo.com/" class="external-link" target="_blank">Typer</a>）。🚀

## 添加更多校验

你还可以添加 `min_length` 参数：

{* ../../docs_src/query_params_str_validations/tutorial003_an_py310.py hl[10] *}

## 添加正则表达式

你可以定义一个参数值必须匹配的<abbr title="正则表达式或正则是定义字符串搜索模式的字符序列。">正则表达式</abbr> `pattern`：

{* ../../docs_src/query_params_str_validations/tutorial004_an_py310.py hl[11] *}

这个指定的正则表达式通过以下规则检查接收到的参数值：

* `^`：以该符号之后的字符开头，符号之前没有字符。
* `fixedquery`：值精确地等于 `fixedquery`。
* `$`：到此结束，在 `fixedquery` 之后没有更多字符。

如果你对所有的这些**「正则表达式」**概念感到迷茫，请不要担心。对于许多人来说这都是一个困难的主题。你仍然可以在无需正则表达式的情况下做很多事情。

但是，一旦你需要用到并去学习它们时，请了解你已经可以在 **FastAPI** 中直接使用它们。

### Pydantic v1 中的 `regex` 而不是 `pattern` { #pydantic-v1-regex-instead-of-pattern }

在 Pydantic v2 和 FastAPI 0.100.0 之前，参数名为 `regex` 而不是 `pattern`，但现在已弃用。

你可能仍然会看到一些使用它的代码：

//// tab | Pydantic v1

{* ../../docs_src/query_params_str_validations/tutorial004_regex_an_py310.py hl[11] *}

////

但请注意，这已弃用，应更新为使用新参数 `pattern`。🤓

## 默认值

你可以向 `Query` 的第一个参数传入 `None` 用作查询参数的默认值，以同样的方式你也可以传递其他默认值。

假设你想要声明查询参数 `q`，使其 `min_length` 为 `3`，并且默认值为 `fixedquery`：

{* ../../docs_src/query_params_str_validations/tutorial005_an_py39.py hl[9] *}

/// note

默认值可以是任何类型的，包括 None ，这使得该参数成为可选的（非必需的）。

///

## 声明为必需参数

当我们不需要声明额外的校验或元数据时，只需不声明默认值就可以使 `q` 参数成为必需参数，例如：

```Python
q: str
```

代替：

```Python
q: str | None = None
```

但是现在我们正在用 `Query` 声明它，例如：

```Python
q: Annotated[str | None, Query(min_length=3)] = None
```

因此，当你在使用 `Query` 且需要声明一个值是必需的时，只需不声明默认参数：

{* ../../docs_src/query_params_str_validations/tutorial006_an_py39.py hl[9] *}

### 使用`None`声明必需参数

您可以声明某个参数可以接受 `None` ，但仍然是必需的。这样会要求客户端发送一个值，即使该值为 `None` 。

为此，您可以声明 `None` 是一种有效类型，但无需声明默认值：

{* ../../docs_src/query_params_str_validations/tutorial006c_an_py310.py hl[9] *}

## 查询参数列表 / 多个值

当你使用 `Query` 显式地定义查询参数时，你还可以声明它去接收一组值，或换句话来说，接收多个值。

例如，要声明一个可在 URL 中出现多次的查询参数 `q`，你可以这样写：

{* ../../docs_src/query_params_str_validations/tutorial011_an_py310.py hl[9] *}

然后，输入如下网址：

```
http://localhost:8000/items/?q=foo&q=bar
```

你会在*路径操作函数*的*函数参数* `q` 中以一个 Python `list` 的形式接收到*查询参数* `q` 的多个值（`foo` 和 `bar`）。

因此，该 URL 的响应将会是：

```JSON
{
  "q": [
    "foo",
    "bar"
  ]
}
```

/// tip

要声明类型为 `list` 的查询参数，如上例所示，你需要显式地使用 `Query`，否则该参数将被解释为请求体。

///

交互式 API 文档将会相应地进行更新，以允许使用多个值：

<img src="https://fastapi.tiangolo.com/img/tutorial/query-params-str-validations/image02.png">

### 具有默认值的查询参数列表 / 多个值

你还可以定义在没有任何给定值时的默认 `list` 值：

{* ../../docs_src/query_params_str_validations/tutorial012.py hl[9] *}

如果你访问：

```
http://localhost:8000/items/
```

`q` 的默认值将为：`["foo", "bar"]`，你的响应会是：

```JSON
{
  "q": [
    "foo",
    "bar"
  ]
}
```

#### 使用 `list`

你也可以直接使用 `list` 代替 `List [str]`：

{* ../../docs_src/query_params_str_validations/tutorial013_an_py39.py hl[9] *}

/// note

请记住，在这种情况下 FastAPI 将不会检查列表的内容。

例如，`List[int]` 将检查（并记录到文档）列表的内容必须是整数。但是单独的 `list` 不会。

///

## 声明更多元数据

你可以添加更多有关该参数的信息。

这些信息将包含在生成的 OpenAPI 模式中，并由文档用户界面和外部工具所使用。

/// note

请记住，不同的工具对 OpenAPI 的支持程度可能不同。

其中一些可能不会展示所有已声明的额外信息，尽管在大多数情况下，缺少的这部分功能已经计划进行开发。

///

你可以添加 `title`：

{* ../../docs_src/query_params_str_validations/tutorial007_an_py310.py hl[10] *}

以及 `description`：

{* ../../docs_src/query_params_str_validations/tutorial008_an_py310.py hl[14] *}

## 别名参数

假设你想要查询参数为 `item-query`。

像下面这样：

```
http://127.0.0.1:8000/items/?item-query=foobaritems
```

但是 `item-query` 不是一个有效的 Python 变量名称。

最接近的有效名称是 `item_query`。

但是你仍然要求它在 URL 中必须是 `item-query`...

这时你可以用 `alias` 参数声明一个别名，该别名将用于在 URL 中查找查询参数值：

{* ../../docs_src/query_params_str_validations/tutorial009_an_py310.py hl[9] *}

## 弃用参数

现在假设你不再喜欢此参数。

你不得不将其保留一段时间，因为有些客户端正在使用它，但你希望文档清楚地将其展示为<abbr title ="已过时，建议不要使用它">已弃用</abbr>。

那么将参数 `deprecated=True` 传入 `Query`：

{* ../../docs_src/query_params_str_validations/tutorial010_an_py310.py hl[19] *}

文档将会像下面这样展示它：

<img src="https://fastapi.tiangolo.com/img/tutorial/query-params-str-validations/image01.png">

## 从 OpenAPI 中排除参数 { #exclude-parameters-from-openapi }

要从生成的 OpenAPI 模式（以及自动文档系统）中排除查询参数，请将 `Query` 的参数 `include_in_schema` 设置为 `False`：

{* ../../docs_src/query_params_str_validations/tutorial014_an_py310.py hl[10] *}

## 自定义校验 { #custom-validation }

在某些情况下，您可能需要进行一些无法通过上述参数完成的**自定义校验**。

在这种情况下，您可以使用一个**自定义校验函数**，该函数在正常校验之后（例如，在校验值是 `str` 之后）应用。

您可以使用 `Annotated` 中的 <a href="https://docs.pydantic.dev/latest/concepts/validators/#field-after-validator" class="external-link" target="_blank">Pydantic 的 `AfterValidator`</a> 来实现这一点。

/// tip

Pydantic 也有 <a href="https://docs.pydantic.dev/latest/concepts/validators/#field-before-validator" class="external-link" target="_blank">`BeforeValidator`</a> 和其他验证器。🤓

///

例如，这个自定义验证器检查商品 ID 是否以 `isbn-` 开头（表示 <abbr title="ISBN means International Standard Book Number">ISBN</abbr> 书号）或以 `imdb-` 开头（表示 <abbr title="IMDB (Internet Movie Database) is a website with information about movies">IMDB</abbr> 电影 URL ID）：

{* ../../docs_src/query_params_str_validations/tutorial015_an_py310.py hl[5,16:19,24] *}

/// info

这在 Pydantic 2 或更高版本中可用。😎

///

/// tip

如果您需要进行任何需要与任何**外部组件**（如数据库或其他 API）通信的验证，您应该改用 **FastAPI 依赖项**，您将在稍后学习它们。

这些自定义验证器适用于**仅**使用请求中提供的**相同数据**即可检查的事物。

///

### 理解代码 { #understand-that-code }

重点是**在 `Annotated` 中使用带有函数的 `AfterValidator`**。您可以随意跳过这部分。🤸

---

但是，如果您对这个特定的代码示例感到好奇并且仍然感兴趣，这里有一些额外的细节。

#### 带有 `value.startswith()` 的字符串 { #string-with-value-startswith }

您注意到了吗？使用 `value.startswith()` 的字符串可以接受一个元组，它将检查元组中的每个值：

{* ../../docs_src/query_params_str_validations/tutorial015_an_py310.py ln[16:19] hl[17] *}

#### 随机项 { #a-random-item }

通过 `data.items()`，我们得到一个<abbr title="可以用 for 循环迭代的对象，如列表、集合等。">可迭代对象</abbr>，其中包含每个字典项的键和值的元组。

我们使用 `list(data.items())` 将此可迭代对象转换为一个适当的 `list`。

然后，使用 `random.choice()` 我们可以从列表中获取一个**随机值**，因此，我们得到一个包含 `(id, name)` 的元组。它将类似于 `("imdb-tt0371724", "银河系漫游指南")`。

然后，我们将元组的**这两个值分配**给变量 `id` 和 `name`。

因此，如果用户没有提供商品 ID，他们仍然会收到一个随机建议。

...我们**只用一行简单的代码**就完成了所有这些。🤯 你不喜欢 Python 吗？🐍

{* ../../docs_src/query_params_str_validations/tutorial015_an_py310.py ln[22:30] hl[29] *}

## 总结

你可以为查询参数声明额外的校验和元数据。

通用的校验和元数据：

* `alias`
* `title`
* `description`
* `deprecated`

特定于字符串的校验：

* `min_length`
* `max_length`
* `regex`

使用 `AfterValidator` 进行自定义验证。

在这些示例中，你了解了如何声明对 `str` 值的校验。

请参阅下一章节，以了解如何声明对其他类型例如数值的校验。

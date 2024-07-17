---

title: Pandas, Execel, Csv

date: 2024-07-16 12:00:00 +/-8000

categories: [Python, Pandas]

tags: [Python, Pandas, Data Process]   # TAG names should always be lowercase

author: bulalalla

description: 对Pandas功能进行介绍，

---

> **注：撰写此笔记时，pandas的stable版本为2.2**

{: .prompt-info }

## pandas介绍

pandas是一个专门处理 表格式（tabular）数据的python工具包，在Pandas中，一个表格式数据被叫做 ```DataFrame```

![image-20240716134915391](../assets/img/image-20240716134915391.png)

### 功能简述

pandas不仅拥有对表格式数据的读写能力，还可以对数据进行绘图、筛选、计算等，本部分对pandas的能力做一个简要的总结，后面再做详细介绍

| 功能     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| 读写数据 | pandas支持多种类型文件的读写(csv, excel, sql, json, parquet...)，使用函数```read_*```进行读取，```to_*```进行存储 |
| 筛选     | pandas中拥有一些 slicing, selecting, extracting函数，支持数据的分片、选择、提取 |
| 绘图     | pandas也可以对数据进行绘图，得益于Matplotlib库的支持         |
| 插入     | pandas可以通过简单的操作对现有的数据插入新的列/行            |
| 其它     | 数值计算(max, min, mean, counts...)，改变表格结构、合并多个文件、处理时间数据、文本数据处理 |



## pandas 安装

1. 在anaconda环境下

   ```shell
   conda install -c <your_forge_name> pandas
   ```

2. 使用pip从pypi中安装

   ```shell
   # 安装 pandas
   pip install pandas
   # 安装指定的依赖
   pip install "pandas[<依赖包名>]"
   ```

   > 截至目前pandas要求pip版本最低为19.3

   {: .prompt-warning }

3. 安装正在开发的pandas版本

   开发版本通常每天从 anaconda.org 的 PyPI 注册表上传到 Scientific-python-nightly-wheels 索引，可以通过运行来安装它。

   ```shell
   pip install --pre --extra-index https://pypi.anaconda.org/scientific-python-nightly-wheels/simple pandas
   ```

   当你想要卸载一个当前pandas时，执行：

   ```shell
   pip uninstall pandas -y
   # -y 表示yes，不询问是否确定卸载
   ```

### 依赖

依赖可能会随这版本更新改变，若有不同请参考[官网安装教程](https://pandas.pydata.org/docs/getting_started/install.html)

#### 必须依赖

| Package         | 最低支持版本 |
| --------------- | ------------ |
| NumPy           | 1.22.4       |
| python-dateutil | 2.8.2        |
| pytz            | 2020.1       |
| tzdata          | 2022.7       |

#### 可选依赖

pandas 有许多可选依赖项，仅用于特定方法。例如，pandas.read_hdf()需要pytables包，而DataFrame.to_markdown()需要tabulate包。

> 当使用了一些方法需要额外的包，若未安装，则会抛出```ImportError```

{: .prompt-warning}

##### 提高性能的依赖（推荐安装）

```pip install "pandas[performance]"```

| Dependency                                         | Minimum Version | pip extra   | Notes                                                        |
| -------------------------------------------------- | --------------- | ----------- | ------------------------------------------------------------ |
| [numexpr](https://github.com/pydata/numexpr)       | 2.8.4           | performance | 通过使用多核以及智能分块和缓存来加速某些数值运算，以实现大幅加速 |
| [bottleneck](https://github.com/pydata/bottleneck) | 1.3.6           | performance | 通过使用专门的 cython 例程来加速某些类型的`nan`，以实现大幅加速 |
| [numba](https://github.com/numba/numba)            | 0.56.4          | performance | 用于接受 `engine="numba"` 的操作的替代执行引擎，使用 JIT 编译器，使用 LLVM 编译器将 Python 函数转换为优化的机器代码。 |

##### 可视化依赖

```pip install "pandas[plot, output-formatting]"```

| Dependency | Minimum Version | pip extra         | Notes                                                        |
| ---------- | --------------- | ----------------- | ------------------------------------------------------------ |
| matplotlib | 3.6.3           | plot              | Plotting library                                             |
| Jinja2     | 3.1.2           | output-formatting | Conditional formatting with DataFrame.style                  |
| tabulate   | 0.9.0           | output-formatting | Printing in Markdown-friendly format (see [tabulate](https://github.com/astanin/python-tabulate)) |

##### 数值计算处理依赖

 `pip install "pandas[computation]"`

| Dependency | Minimum Version | pip extra   | Notes                                  |
| ---------- | --------------- | ----------- | -------------------------------------- |
| SciPy      | 1.10.0          | computation | Miscellaneous statistical functions    |
| xarray     | 2022.12.0       | computation | pandas-like API for N-dimensional data |

##### Excel依赖

 `pip install "pandas[excel]"`

| Dependency      | Minimum Version | pip extra | Notes                               |
| --------------- | --------------- | --------- | ----------------------------------- |
| xlrd            | 2.0.1           | excel     | Reading Excel                       |
| xlsxwriter      | 3.0.5           | excel     | Writing Excel                       |
| openpyxl        | 3.1.0           | excel     | Reading / writing for xlsx files    |
| pyxlsb          | 1.0.10          | excel     | Reading for xlsb files              |
| python-calamine | 0.1.7           | excel     | Reading for xls/xlsx/xlsb/ods files |

##### HTML依赖

`pip install "pandas[html]"`

| Dependency     | Minimum Version | pip extra | Notes                     |
| -------------- | --------------- | --------- | ------------------------- |
| BeautifulSoup4 | 4.11.2          | html      | HTML parser for read_html |
| html5lib       | 1.1             | html      | HTML parser for read_html |
| lxml           | 4.9.2           | html      | HTML parser for read_html |

One of the following combinations of libraries is needed to use the top-level [`read_html()`](https://pandas.pydata.org/docs/reference/api/pandas.read_html.html#pandas.read_html) function:

- [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup) and [html5lib](https://github.com/html5lib/html5lib-python)
- [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup) and [lxml](https://lxml.de/)
- [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup) and [html5lib](https://github.com/html5lib/html5lib-python) and [lxml](https://lxml.de/)
- Only [lxml](https://lxml.de/), although see [HTML Table Parsing](https://pandas.pydata.org/docs/user_guide/io.html#io-html-gotchas) for reasons as to why you should probably **not** take this approach.

> - if you install [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup) you must install either [lxml](https://lxml.de/) or [html5lib](https://github.com/html5lib/html5lib-python) or both. [`read_html()`](https://pandas.pydata.org/docs/reference/api/pandas.read_html.html#pandas.read_html) will **not** work with *only* [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup) installed.
> - You are highly encouraged to read [HTML Table Parsing gotchas](https://pandas.pydata.org/docs/user_guide/io.html#io-html-gotchas). It explains issues surrounding the installation and usage of the above three libraries.
>

{: .prompt-warning}

##### XML依赖

`pip install "pandas[xml]"`

| Dependency | Minimum Version | pip extra | Notes                                               |
| ---------- | --------------- | --------- | --------------------------------------------------- |
| lxml       | 4.9.2           | xml       | XML parser for read_xml and tree builder for to_xml |

##### SQL数据库依赖

 `pip install "pandas[postgresql, mysql, sql-other]"`

| Dependency             | Minimum Version | pip extra                    | Notes                                       |
| ---------------------- | --------------- | ---------------------------- | ------------------------------------------- |
| SQLAlchemy             | 2.0.0           | postgresql, mysql, sql-other | SQL support for databases other than sqlite |
| psycopg2               | 2.9.6           | postgresql                   | PostgreSQL engine for sqlalchemy            |
| pymysql                | 1.0.2           | mysql                        | MySQL engine for sqlalchemy                 |
| adbc-driver-postgresql | 0.8.0           | postgresql                   | ADBC Driver for PostgreSQL                  |
| adbc-driver-sqlite     | 0.8.0           | sql-other                    | ADBC Driver for SQLite                      |

##### 其他类型数据依赖

`pip install "pandas[hdf5, parquet, feather, spss, excel]"`

| Dependency  | Minimum Version | pip extra        | Notes                                                     |
| ----------- | --------------- | ---------------- | --------------------------------------------------------- |
| PyTables    | 3.8.0           | hdf5             | HDF5-based reading / writing                              |
| blosc       | 1.21.3          | hdf5             | Compression for HDF5; only available on `conda`           |
| zlib        |                 | hdf5             | Compression for HDF5                                      |
| fastparquet | 2022.12.0       |                  | Parquet reading / writing (pyarrow is default)            |
| pyarrow     | 10.0.1          | parquet, feather | Parquet, ORC, and feather reading / writing               |
| pyreadstat  | 1.2.0           | spss             | SPSS files (.sav) reading                                 |
| odfpy       | 1.4.1           | excel            | Open document format (.odf, .ods, .odt) reading / writing |

##### 从云中访问数据

 `pip install "pandas[fss, aws, gcp]"`

| Dependency | Minimum Version | pip extra     | Notes                                                        |
| ---------- | --------------- | ------------- | ------------------------------------------------------------ |
| fsspec     | 2022.11.0       | fss, gcp, aws | Handling files aside from simple local and HTTP (required dependency of s3fs, gcsfs). |
| gcsfs      | 2022.11.0       | gcp           | Google Cloud Storage access                                  |
| pandas-gbq | 0.19.0          | gcp           | Google Big Query access                                      |
| s3fs       | 2022.11.0       | aws           | Amazon S3 access                                             |

##### 剪切板

 `pip install "pandas[clipboard]"`.

| Dependency  | Minimum Version | pip extra | Notes         |
| ----------- | --------------- | --------- | ------------- |
| PyQt4/PyQt5 | 5.15.9          | clipboard | Clipboard I/O |
| qtpy        | 2.3.0           | clipboard | Clipboard I/O |

##### 压缩

`pip install "pandas[compression]"`

| Dependency | Minimum Version | pip extra   | Notes                 |
| ---------- | --------------- | ----------- | --------------------- |
| Zstandard  | 0.19.0          | compression | Zstandard compression |

## 基本数据类型

pandas有两种基本数据类型`Series`和`DataFrame`，分别代表一维数据和二维数据

| 维度 | 名字      | 描述                                                 |
| ---- | --------- | ---------------------------------------------------- |
| 1    | Series    | 一维同类型带标签的数组                               |
| 2    | DataFrame | 具有潜在异构类型列的通用二维标记、大小可变的表格结构 |

### Series

Series 是一个一维标记数组，可以保存任何数据类型（整数、字符串、浮点数、Python 对象等）,轴标签统称为索引。

> 可以理解为Excel表格中的一列，Index就相当于Excel中行编号，只不过这里的Index可以不止为数字
>
> 为什么是一列而不是一行呢？因为通常每列数据类型是相同的，Series想表示一组同类型的数据

{: .prompt-tip}

#### 构造Series

`Series` 是一个一维标记数组，能够容纳任何数据类型（整数、字符串、浮点数、Python 对象等）。轴标签统称为`index`。创建 `Series `的基本方法是调用：

```python
s = pd.Series(data, index=index)
```

这里的`data`可以是python的字典、ndarray（n-dimension array，实际上传入的数组必须是1维的）、标量（一个值，比如1），`index`代表数据轴上的标签，根据`data`的不同`index`拥有不同的效果：

##### 当data是字典时

此时可以不传入index，字典对应的key会被对应到每个value的index

```python
In [7]: d = {"b": 1, "a": 0, "c": 2}

In [8]: pd.Series(d)
Out[8]: 
b    1
a    0
c    2
dtype: int64
```

如果传入了index，如

```python
[In 11]: pd.Series(d, index=["b", "c", "d", "a"])
Out[11]: 
b    1.0
c    2.0
d    NaN
a    0.0
dtype: float64
```

> NaN（not a number）是在pandas中缺失数据时使用的标准标记

{: .prompt-tip}

##### 当传入的data是ndarry时

此时index的长度必须与`ndarray`的长度相同（注：不会像dict那样自动填充NaN），data的index会与传入的index对应，如果不传入index默认值会为`[0, ..., len(data)-1]`

```python
In [3]: s = pd.Series(np.random.randn(5), index=["a", "b", "c", "d", "e"])

In [4]: s
Out[4]: 
a    0.469112
b   -0.282863
c   -1.509059
d   -1.135632
e    1.212112
dtype: float64

In [5]: s.index
Out[5]: Index(['a', 'b', 'c', 'd', 'e'], dtype='object')

In [6]: pd.Series(np.random.randn(5))
Out[6]: 
0   -0.173215
1    0.119209
2   -1.044236
3   -0.861849
4   -2.104569
dtype: float64
```

##### 当传入的数据是标量时

此时最好传入index，这个标量的值会被重复到与index的长度一致：

```python
In [12]: pd.Series(5.0, index=["a", "b", "c", "d", "e"])
Out[12]: 
a    5.0
b    5.0
c    5.0
d    5.0
e    5.0
dtype: float64   
# 若不传入index
In [12]: pd.Series(5)
In [13]: print(s.array)
out[13]:
0    1
dtype: int64
```

##### 为什么要用Index？

- **标识和访问数据**：索引为数据点提供了一个唯一的标识，这使得可以通过索引标签来访问特定的数据值。没有索引的话，只能使用位置索引（类似于数组的索引）来访问数据。
- **数据对齐**：索引使得不同`Series`对象之间的数据能够根据索引进行对齐操作。这对于数据运算和合并非常有用。例如，在进行加法运算时，只有索引匹配的数据才会相加。
- **增加数据的描述性**：索引标签可以是字符串、日期、时间等，更加直观地描述数据的含义，而不仅仅是数字位置索引。
- **支持时间序列数据**：索引尤其在处理时间序列数据时非常有用。时间序列数据的索引通常是时间戳，这使得可以方便地进行时间相关的操作，比如重采样和时间切片。

#### Series的使用技巧

##### 像数组一样使用（Index无关型操作）

Series拥有`iloc`属性，它提供通过位置索引（整数索引）的访问和操作`Series`对象的数据。当只关心数据位置而不是标签时非常有用。

`Series.iloc`主要有以下几个作用：

1. **单个元素访问**：可以通过整数索引访问单个元素。
2. **切片操作**：可以通过整数位置进行切片操作，获取一个子集。
3. **布尔索引**：可以通过布尔数组进行索引，获取符合条件的子集。

```python
# 单个元素访问
import pandas as pd

data = [10, 20, 30, 40, 50]
index = ['a', 'b', 'c', 'd', 'e']
series = pd.Series(data, index=index)

# 访问第二个元素
print(series.iloc[1])  # 输出: 20

# 切片操作
# 获取从第2个到第4个元素（不包括第4个）
print(series.iloc[1:3])  

# 输出:
# b    20
# c    30
# dtype: int64

# 使用布尔索引
print(series.iloc[[True, False, True, False, True]])  

# 输出:
# a    10
# c    30
# e    50
# dtype: int64

```

##### 像字典一样使用

`Series`对象可以通过设置的`Index`标签像字典一样访问数据

```python
In [21]: s["a"]
Out[21]: 0.4691122999071863

In [22]: s["e"] = 12.0

In [23]: s
Out[23]: 
a     0.469112
b    -0.282863
c    -1.509059
d    -1.135632
e    12.000000
dtype: float64

In [24]: "e" in s
Out[24]: True

In [25]: "f" in s
Out[25]: False

# 使用get函数
In [27]: s.get("f")

In [28]: s.get("f", np.nan)
Out[28]: nan
```

##### 向量操作--标签自动对齐

当进行两个向量之间的加法操作时（或其他操作），`Series`和Numpy array一样不需要使用循环一个一个的计算，可以直接进行操作：

```python
In [29]: s + s
Out[29]: 
a     0.938225
b    -0.565727
c    -3.018117
d    -2.271265
e    24.000000
dtype: float64

In [30]: s * 2
Out[30]: 
a     0.938225
b    -0.565727
c    -3.018117
d    -2.271265
e    24.000000
dtype: float64

In [31]: np.exp(s)
Out[31]: 
a         1.598575
b         0.753623
c         0.221118
d         0.321219
e    162754.791419
dtype: float64
```

与Numpy不同的是，`Series`会根据Index值自动对齐。此外两个`Series`拥有不同Index也可以进行操作，会自动对其它们重叠的Index，如果一个Index另一个对象没有，则操作结果为 `NaN` ,最终结果的Index为两个对象Index的并集：

```python
In [32]: s.iloc[1:] + s.iloc[:-1]
Out[32]: 
a         NaN
b   -0.565727
c   -3.018117
d   -2.271265
e         NaN
dtype: float64
```

##### name属性

在许多情况下，`Series` 名称可以自动分配，特别是从 DataFrame 中选择单个列时，该名称将被分配列标签。

### DataFrame

`DataFrame` 是一个二维标记数据结构，其列可能具有不同类型的类型。你可以将它想象成电子表格或 SQL 表，或 `Series` 对象的字典。它通常是最常用的 pandas 对象。

#### 构造DataFrame

DataFrame可以接收多种输入：

- 一维数组的字典、lists、dicts或`Series`(**注意复数形式**)
- 二维数组
- 单个`Series`
- 另一个`DataFrame`
- [Structured or record](https://numpy.org/doc/stable/user/basics.rec.html) ndarray

##### 使用value为Series对象的字典构造

字典中`value`为`Series`对象，每个`item`会被视为一列，列名为`item`的`key`，列内容为`Series`，每个`Series`都会有`Index`属性，最终构造出的总`Index`为所有`Index`的并集，若某个`Series`对象无某个`index`，则会填充`NaN`

```python
In [38]: d = {
   ....:     "one": pd.Series([1.0, 2.0, 3.0], index=["a", "b", "c"]),
   ....:     "two": pd.Series([1.0, 2.0, 3.0, 4.0], index=["a", "b", "c", "d"]),
   ....: }
   ....: 
# 直接构造
In [39]: df = pd.DataFrame(d)
In [40]: df
Out[40]: 
   one  two
a  1.0  1.0
b  2.0  2.0
c  3.0  3.0
d  NaN  4.0
# 构造时指定Index，则只会保留传入的Index
In [41]: pd.DataFrame(d, index=["d", "b", "a"])
Out[41]: 
   one  two
d  NaN  4.0
b  2.0  2.0
a  1.0  1.0
# 构造时传入cloumns参数，则只会保留对应key的item
In [42]: pd.DataFrame(d, index=["d", "b", "a"], columns=["two", "three"])
Out[42]: 
   two three
d  4.0   NaN
b  2.0   NaN
a  1.0   NaN
```

##### 使用value为数组/列表的字典构造

此时所有`value`对应的数组，长度一定要相同，否则会抛出`ValueError`。此外如果不传入`Index`，则`Index会`使用`ange(n)`生成，n为长度，如果传入了`Index`，则要保证传入的`Index`长度要与数组长度对应，否则也会抛出`ValueError`

```python
In [45]: d = {"one": [1.0, 2.0, 3.0, 4.0], "two": [4.0, 3.0, 2.0, 1.0]}
# 不传入Index
In [46]: pd.DataFrame(d)
Out[46]: 
   one  two
0  1.0  4.0
1  2.0  3.0
2  3.0  2.0
3  4.0  1.0
# 传入Index
In [47]: pd.DataFrame(d, index=["a", "b", "c", "d"])
Out[47]: 
   one  two
a  1.0  4.0
b  2.0  3.0
c  3.0  2.0
d  4.0  1.0
```



## pandas的IO操作（Excel、csv、...）
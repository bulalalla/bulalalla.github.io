---

title: Pandas Table Data Lib

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


# 项目说明

本项目包含三部分：

1. **Pandas 步骤**：读取一个满足要求的数据集（至少一列数值型列、1000–1,000,000 行），计算**平均值、 中位数、 众数**。
2. **艰难方式（标准库）**：使用**纯 Python 标准库**（不得使用 pandas、statistics、numpy 等）对同一数据集完成相同计算。
3. **数据可视化**：绘制**仅使用 Python 标准库**实现的“字符/文本”可视化（不允许 matplotlib、seaborn、plotly 等）。

> 建议：先实现一个最简“迷你图”，例如：
>
> ```
> Rat sightings, in thousands
> 2014: ***
> 2015: *****
> 2016: ****
> ```

## 数据集要求与建议

- 至少一列**数值**列；
- 行数在 **1,000–1,000,000** 之间（过大可筛选取样）；
- 快速选择，**不要在找数据上花费太久**；
- 放到 `data/` 目录，命名为 `dataset.csv`（你也可以另取名字，但记得在笔记本里改路径）。

> 若数据集中有多列数值列，**任选一列**用于本次计算与可视化。

## 结构

- `_config.yml` 与 `_toc.yml`：Jupyter Book 配置与目录
- `intro.md`：本说明页
- `01_pandas_step.ipynb`：Pandas 解法
- `02_stdlib_step.ipynb`：标准库“艰难方式”
- `03_visualization.ipynb`：标准库绘图（字符可视化）
- `data/`：放置数据集（CSV）
- `images/`：如需添加图片，可放在此处

## 构建方式（本地命令行）

若你在本地终端中：

```bash
pip install -U jupyter-book
cd my-stats-project
jupyter-book build .
# 构建完成后在 _build/html/index.html 打开
```

你也可以仅在 Jupyter 环境中运行每个 notebook 完成作业，无需构建整本书。
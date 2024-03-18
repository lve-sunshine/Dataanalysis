# Dataanalysis
交通分析，KNN聚类分析打车点位置推荐方案

## 1 数据清洗
数据清洗脚本位于 ```/mydata/datacleaning/data_cleaning.py```，去除数据中的重复值、空值、超过经纬度的异常值。选择了经度、纬度、速度、状态、时间五个字段作为进行数据分析的原始数据。

基于在项目根目录下终端使用脚本的方法如下：
### 1.1 默认使用深圳范围进行数据清洗：
```python
python /mydata/datacleaning/data_cleaning.py input_file_path output_file_path
```
- `input_file_path`：输入文件的路径。输入文件一定要满足存在表头，数据表头名称一定有 **latitude**、**longitude**、**speed**、**state**。
- `output_file_path`：输出文件的路径。如果输出文件存在于某个目录中，确保该目录事先已经创建。
### 1.2 指定其他经纬度范围进行数据清洗：
```bash
python /mydata/datacleaning/data_cleaning.py input_file_path output_file_path lat_min lat_max lon_min lon_max
```
- `input_file_path`：输入文件的路径。文件需满足**表头存在**且对应数据表头名称一定为**latitude**、**longitude**、**speed**、**state**。
- `output_file_path`：输出文件的路径。
- `lat_min`：指定纬度的最小值。
- `lat_max`：指定纬度的最大值。
- `lon_min`：指定经度的最小值。
- `lon_max`：指定经度的最大值。
### 1.3 示例
```
python /mydata/datacleaning/data_cleaning.py ../data/input/ ../data/output/shenzhen_cleaning.csv
```
### 1.4 说明
清洗脚本默认在存在输出文件已经存在时直接进行追加。

## 2 数据处理
数据处理目的为；**将清洗好的数据源文件按time进行分割，最终分别得到24个小时各个小时阶段的汇总停车经纬度点**。

脚本位于`/mydata/datacleaning/devide_time.py`,根据需求可修改处如下图：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/6a65b917-c770-463e-b9ea-68a2f18deffc)

![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/973e112f-fc62-484f-84f2-16e667e4b149)

**得到输出文件默认直接保存在`/devide_time.py`目录下，有需要请自行添加相关目录更该操作。

## 3 聚类分析
聚类使用了MiniBatchKMeans算法，默认聚类中心数量为300，聚类思想大致为：**分别对24个小时的各个时间段的停车点经纬度汇总文件进行聚类分析，最终分别得到24个小时各个时间段的300个聚类中心点**，脚本位置在于`/mydata/model/Kmeans/MiniBathKMeans.py`
```
python /mydata/model/Kmeans/MiniBathKMeans.py
```
该文件未进行脚本设置，直接使用`/mydata/in/`目录下的各时间段文件作为输入，直接输出在`/mydata/out/`目录下,若需修改路径则直接修改`MiniBathKMeans.py`如下标记处：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/3c0569c8-d32c-45be-a268-c5b2e9f77ce1)

### 3.1 输入示例
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/b7a1108a-c5f3-4621-83c9-96deab87b911)

### 3.2 输出示例
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/cea18034-1756-483c-96cf-2dbc6b9e36c2)

### 3.3 权重划分
本聚类分析对于聚类中心的权重划分思想大致为：根据给定数据集中每个聚类点（num）在整个数据集中的数量（num_sum）。
首先，通过计算整个数据集中数量的分位数，将数据集分为五个区间：quantile_20、quantile_40、quantile_60、quantile_80。

然后，根据每个聚类点在整个数据集中的数量与这些分位数之间的关系，确定其权重：
  - 如果聚类点的数量小于 quantile_20，则权重为0。
  - 如果聚类点的数量介于 quantile_20 和 quantile_40 之间，则权重为1。
  - 如果聚类点的数量介于 quantile_40 和 quantile_60 之间，则权重为2。
  - 如果聚类点的数量介于 quantile_60 和 quantile_80 之间，则权重为3。
  - 如果聚类点的数量大于等于 quantile_80，则权重为4。

这种划分方式可以根据聚类点在整个数据集中的相对数量来确定其权重级别。例如，如果一个聚类点的数量相对较少，则被划分为权重0；而如果一个聚类点的数量相对较多，则被划分为权重4。这样可以在后续的分析或使用过程中，根据权重来考虑聚类点的重要性或影响力。若需修改权重分布则直接修改`MiniBathKMeans.py`的`weighing`方法即可，如下图：

![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/02d32e5b-b9da-496a-82df-0f655ee6e169)



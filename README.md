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
数据处理目的为；**将清洗好的数据源文件按time进行分割，最终分别得到24个小时各个小时阶段的停车经纬度点汇总**。

脚本位于`/mydata/datacleaning/devide_time.py`,根据需求可修改处如下图：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/6a65b917-c770-463e-b9ea-68a2f18deffc)

![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/973e112f-fc62-484f-84f2-16e667e4b149)

**得到的输出文件默认直接保存在当前`/devide_time.py`的同级目录下，有需要请自行添加相关目录更该操作。**

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

## 4 可视化展示
本文可视化展示涉及所有内容所在的路径为下图所示：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/17613779-f7ab-4533-9e7d-968c0f94f00b)

- `css`：样式文件目录。
- `data`：聚类中心点文件，创建目录合法格式为`week_{num}`，`num`值表示当前目录为周几的24小时各时间段聚类中心点文本。如下图：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/88332b39-3292-4da0-8cef-8aff818450b0)

- `index.html`：可视化页面展示入口，使用本地浏览器打开`index.html`即可访问。
- `img`：本地图片目录，地图标注的记号图片。
- `js`：页面展示涉及的JavaScript脚本，其下的`GeoUtils.js`文件包含了停车点路径推荐，范围内是否存在停车点等重要算法。注释完备自行研究即可：
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/a7593a27-4692-4fd5-a30c-49f4203b6877)

### 4.1 修改周几的数据来源
在`index.html`中，直接修改标注处的 `week` 值，以指定数据读取的是上述提及的 `data` 目录下的 `week_{week}` 目录中的文件
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/77108653-e77e-4a12-955e-d65ce17b05c2)

![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/748e0f12-d81e-49d1-8d56-9dcbc28cfd28)

原页面周二至周日的数据读取均为`data/week_2`目录下的文件，**本文项目中仅周四数据有效，来源于深圳市交通数据集聚类结果**。

修改数据来源方式如下：
1. 在datam目录下创建合法的week_{num}目录
2. 将合法的聚类中心点文件复制到上目录下
3. 修改上图`html`处的li标记处的`week`值
### 4.2 修改初始地图显示的所在位置
如下图，在`index.html`下的该行号位置处修改数值参数即可，本文项目设置地点为深圳某处经纬度。
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/335565f9-8865-4a94-abb3-cd6981e0d361)

### 4.3 修改停车点检索范围的半径大小
如下图，在`index.html`所示两个位置修改即可该表圆形半径，此时均为200
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/97045e34-f3e2-4201-a807-482fc6b9d2f4)
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/d5410ccf-2ce6-4f5b-abbe-e2ffbe82cee3)

### 4.4 修改读取聚类中心点方法的逻辑
如下图，在`index.html`所示位置方法即为原作者的相关逻辑
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/67e18c0e-84b7-4ba4-930f-cddbb1d75866)

### 4.5 修改api
本文项目延用原作者的百度api
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/8610d1cc-b3e6-4fd1-9d1a-526884187e21)

## 5. 展示
![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/a128aa51-1094-49ed-ab27-d490f0c1b7c1)

![image](https://github.com/lve-sunshine/Dataanalysis/assets/99074010/b7e3a662-c9bf-4c9f-ade7-22f82eea1bea)









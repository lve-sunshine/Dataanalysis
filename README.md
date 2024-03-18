# Dataanalysis
交通分析，KNN聚类分析打车点位置推荐方案

## 1 数据清洗
数据清洗脚本位于 ```/mydata/datacleaning/data_cleaning.py```，基于在项目根目录下终端使用脚本的方法如下：
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
- `input_file_path`：输入文件的路径。文件需满足表头存在且对应数据表头名称一定为**latitude**、**longitude**、**speed**、**state**。
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
清洗脚本默认在存在输出文件已经存在时直接进行追加

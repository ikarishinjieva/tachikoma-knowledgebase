Kaggle 经验

# Titanic

[Titanic](https://www.kaggle.com/c/titanic) 经验: 

1. 训练集和测试集都需要进行相同的数据处理
2. 数据处理时, 需检查某维度是否存在空值, 如存在空值, 需要填值处理 (平均值/最常值/...)
3. 填值处理时, 如使用到数据分布, 应是全部的数据分布, 对于测试集, 不应是测试集的数据分布 (测试集过小), 如: 
	* 用`cut`对数据分段, 应对全部数据进行cut, 得出bins, 再用bins对测试集/训练集进行cut
4. pairplot, plot上的点, 只要存在Survived=1的数据, 即标记为1, 否则标记为0. (并不反应Survived的多少)
5. Violin Plot, 可显示 两个值 对结果的分布
6. 将 训练集数据 划分成训练集和验证集, 使用`sklearn.model_selection.StratifiedShuffleSplit`
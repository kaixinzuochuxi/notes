# kaggle竞赛思路

## Question and problem definition

场景问题

目的

[7-C](https://www.kaggle.io/svf/1145136/05331c373d1edddc1d3f1983652794ba/__results__.html#Workflow-goals):
* Classifying
    对样本进行分类，了解**不同类** 和 **最终目标**的**相关性**，**意义**
    * 分类
    * 先验知识
* Correlating
    依据现有特征（连续，离散），**现有特征**对**最终目标**的重要性？两者间的相关性？函数关系？特征之间的相关性？函数关系？
* Converting
    特征转变为标准格式的数据，分为**离散**和**连续**型
* Completing
    数据值缺失情况下，如何填补
    是否有填补的必要--是否相关？

* Correcting
    分析已有数据中**错误数据**并**纠正**或**去除**--查找outlier；丢弃不显著或有害结果的特征
    * 有很多重复-contains high ratio of duplicates
    * 逻辑上不相关
    * 相当不完整
    * 数据格式不标准

* Creating
    基于已有特征创造新特征，使得新特征满足**correlation**, **conversion**, **completeness** goals.
    * 特征合并成新特征，数值运算--Family based on Parch and SibSp
    * 从一个特征中提取新特征，字符串的部分信息--名字中提取称呼，Mr. Miss.
    * 数值连续变量转换为数值离散变量--年龄，年龄段
    

* Charting
    基于**数据特性**和**结果**，选择合适可视化**图表**进行展示


对象变量

规律关系

* 根据题目中的描述，哪些规律？
    * On April 15, 1912, during her maiden voyage, the Titanic sank after colliding with an iceberg, killing 1502 out of 2224 passengers and crew. Translated 32% survival rate.
    * One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew.
    * Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class.
    总体上，比例
    主要原因
    不同成分是否会有不同结果

约束



## 数据导入：

* 读取

        train_df = pd.read_csv('../input/train.csv')
        test_df = pd.read_csv('../input/test.csv')
        combine = [train_df, test_df]

* 分析，描述

    * 总览数据

            print(train_df.columns.values)
            # preview the data
            train_df.head()
            train_df.tail()

            train_df.info()
            print('_'*40)
            test_df.info()
    * 特征有哪些？
    * 数值型有哪些，离散型有哪些？
        * 是否有特征既为数值型又为连续型（ mixed data types）？
    * 哪些可能含有错误？打印错误（typos），类型错误。。。
    * 哪些有缺失值？
        * blank
        * null
        * empty value
    * 各个特征的数据类型？str，int，float...
    * 数值特征的分布？

            train_df.describe()
            # Review survived rate using `percentiles=[.61, .62]` knowing our problem 
                description mentions 38% survival rate.
            # Review Parch distribution using `percentiles=[.75, .8]`
            # SibSp distribution `[.68, .69]`
            # Age and Fare `[.1, .2, .3, .4, .5, .6, .7, .8, .9, .99]`
        * 总共有多少数据？占问题比例的多少？891， 891/2224=40%
        * 哪些是数值型离散特征？数值型离散特征的比例？0-1，percentiles=[.61, .62]
        * 样本中**最终目标**与先前已知信息中**最终目标**是否一致？样本存活38%，实际32%
            * Around 38% samples survived representative of the actual survival rate at 32%.
        * 特征的分布：
            * 什么值（有无，高低），什么比例（数值离散）,什么分布（数值连续），比例高低
                * **Most** passengers (> 75%) did not travel with parents or children.
                * Nearly 30% of the passengers had siblings and/or spouse aboard.
                * Few elderly passengers (<1%) within age range 65-80.
            * 变化情况，多数平稳？少数剧烈？
                * Fares varied significantly with few passengers (<1%) paying as high as $512.

    * 离散特征的分布

            train_df.describe(include=['O'])
        * 是否唯一？--名字唯一
        * 特征共有几类？每类数量，比例多少？--性别，两类，数量xxx，比例为xxx
        * 哪些有重复？重复有多少？比例（1-unique/total）？重复意味什么？--乘客的船舱信息，公用

## Analyze by pivoting features

            
                
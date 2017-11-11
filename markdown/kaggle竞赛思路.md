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

            
选择潜在变量，因果关系--离散型，数值离散性
group

    train_df[['Pclass', 'Survived']].groupby(['Pclass'], as_index=False).mean().sort_values(by='Survived', ascending=False)
    选因果
    groupby，另外选index，不以Pclass为index，得到dataframe结构
    求均值
    按照生存率排序
分析验证先前假设

## Analyze by visualizing data

### 1. Correlating numerical features
选择潜在变量，因果关系--连续型
* 直方图

        g = sns.FacetGrid(train_df, col='Survived')
        g.map(plt.hist, 'Age', bins=20)
        # 横向，col为目标，几个取值就有几个图，x为连续变量，y为当前目标值的频数

* 观察

    * 不同**自变量**的**最终结果**分析
        * 哪几类
            * 高，最高，低，最低，
            * 全部，大部分，部分，小部分，无
* 结论

    * 是否引入？
    * 是否填补缺失值？
    * 是否转换为离散特征？--分段性，是




### 2. Correlating numerical and ordinal features
一次做图，研究**连续型，离散型自变量**与**因变量**的关系--连续型，离散型
* 直方图

        # grid = sns.FacetGrid(train_df, col='Pclass', hue='Survived')
        grid = sns.FacetGrid(train_df, col='Survived', row='Pclass', size=2.2, aspect=1.6)
        grid.map(plt.hist, 'Age', alpha=.5, bins=20)
        grid.add_legend();
        # 横向，col为目标，纵向，row为离散变量，x为连续变量，y为当前目标值的频数
        # 类似联合分布

* 观察：
    * 不同类型（离散特征）样本的数量？**最终结果**是否一致？--Pclass=3最多，不一致
    * 连续特征的哪些范围，离散特征的哪几类较为满足**最终结果**？--Infant passengers in Pclass=2 and Pclass=3
    * 特定离散特征下，连续特征是否满足**最终结果**？--Most passengers in Pclass=1 survived
    * 不同离散特征之间是否有相似性？--没有，年龄分布均不一致
* 结论：
    * 是否引入？


### 3. Correlating categorical features
多个离散特征（多个图多条线），离散目标
* 散点图

        # grid = sns.FacetGrid(train_df, col='Embarked')
        grid = sns.FacetGrid(train_df, row='Embarked', size=2.2, aspect=1.6)
        grid.map(sns.pointplot, 'Pclass', 'Survived', 'Sex', palette='deep')
        grid.add_legend()


* 观察--------看图顺序？次数？---看不同图中相同内容、关系，！！！！！类似合并
    * 某些特征的特定值**完全，大部分**好于其他特征值？--是，女性好于男性
        * 某些例外原因？其他变量的影响？--是，Pclass
* 结论
    * 是否引入？


### 4. Correlating categorical and numerical features

* 条形统计图

        # grid = sns.FacetGrid(train_df, col='Embarked', hue='Survived', palette={0: 'k', 1: 'w'})
        grid = sns.FacetGrid(train_df, row='Embarked', col='Survived', size=2.2, aspect=1.6)
        grid.map(sns.barplot, 'Sex', 'Fare', alpha=.5, ci=None)
        grid.add_legend()

* 观察

* 结论

## Wrangle data

### 1. Correcting by dropping features

    print("Before", train_df.shape, test_df.shape, combine[0].shape, combine[1].shape)

    train_df = train_df.drop(['Ticket', 'Cabin'], axis=1)
    test_df = test_df.drop(['Ticket', 'Cabin'], axis=1)
    combine = [train_df, test_df]

    "After", train_df.shape, test_df.shape, combine[0].shape, combine[1].shape

### 2. Creating new feature extracting from existing
* 从名字中提取称呼，以.结尾的前一个词

        for dataset in combine:
            dataset['Title'] = dataset.Name.str.extract(' ([A-Za-z]+)\.', expand=False)

        pd.crosstab(train_df['Title'], train_df['Sex'])
        pd.crosstab(train_df['Title'], train_df['Survived'])

* 观察：
    * 能否有所区分？--大部分title能区分性别
    * **最终结果**的敏感性？轻微变化
    * 特定title大部分能满足**最终结果**--Sir，Lady
* 结论：
    * 保留title特征


* 将得到的初级title特征合并（替换）为几类

    for dataset in combine:
        dataset['Title'] = dataset['Title'].replace(['Lady', 'Countess','Capt', 'Col',\
        'Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare')

        dataset['Title'] = dataset['Title'].replace('Mlle', 'Miss')
        dataset['Title'] = dataset['Title'].replace('Ms', 'Miss')
        dataset['Title'] = dataset['Title'].replace('Mme', 'Mrs')
    
    train_df[['Title', 'Survived']].groupby(['Title'], as_index=False).mean()

* 数字化（ordinal）表示离散特征

    title_mapping = {"Mr": 1, "Miss": 2, "Mrs": 3, "Master": 4, "Rare": 5}
    for dataset in combine:
        dataset['Title'] = dataset['Title'].map(title_mapping)
        dataset['Title'] = dataset['Title'].fillna(0)

    train_df.head()

* 丢弃不需要特征

    train_df = train_df.drop(['Name', 'PassengerId'], axis=1)
    test_df = test_df.drop(['Name'], axis=1)
    combine = [train_df, test_df]
    train_df.shape, test_df.shape

## 3. Converting a categorical feature

数字化（ordinal）表示离散特征--sex

    for dataset in combine:
        dataset['Sex'] = dataset['Sex'].map( {'female': 1, 'male': 0} ).astype(int)

    train_df.head()


## 4. Completing a numerical continuous feature
填补缺失值

方法：

    根据均值方差生成随机数
    依据其他完整数据点的特征值，选择其余特征值相同情况下的的中位数
    结合上面两种方法：基于完整数据点的特征值和均值方差，随机选

    方法2更稳定，更倾向于方法2
    年龄变化为以0.5为单位

    for dataset in combine:
        for i in range(0, 2):
            for j in range(0, 3):
                guess_df = dataset[(dataset['Sex'] == i) & \
                                    (dataset['Pclass'] == j+1)]['Age'].dropna()

                # age_mean = guess_df.mean()
                # age_std = guess_df.std()
                # age_guess = rnd.uniform(age_mean - age_std, age_mean + age_std)

                age_guess = guess_df.median()

                # Convert random age float to nearest .5 age
                guess_ages[i,j] = int( age_guess/0.5 + 0.5 ) * 0.5
                
        for i in range(0, 2):
            for j in range(0, 3):
                dataset.loc[ (dataset.Age.isnull()) & (dataset.Sex == i) & (dataset.Pclass == j+1),\
                        'Age'] = guess_ages[i,j]

        dataset['Age'] = dataset['Age'].astype(int)

    train_df.head()


连续特征转化为离散特征


    train_df['AgeBand'] = pd.cut(train_df['Age'], 5)
    train_df[['AgeBand', 'Survived']].groupby(['AgeBand'], as_index=False).mean().sort_values(by='AgeBand', ascending=True)

离散特征数字化表示

    for dataset in combine:    
        dataset.loc[ dataset['Age'] <= 16, 'Age'] = 0
        dataset.loc[(dataset['Age'] > 16) & (dataset['Age'] <= 32), 'Age'] = 1
        dataset.loc[(dataset['Age'] > 32) & (dataset['Age'] <= 48), 'Age'] = 2
        dataset.loc[(dataset['Age'] > 48) & (dataset['Age'] <= 64), 'Age'] = 3
        dataset.loc[ dataset['Age'] > 64, 'Age']
    train_df.head()

转换完成删除AgeBand列

    train_df = train_df.drop(['AgeBand'], axis=1)
    combine = [train_df, test_df]
    train_df.head()


## 5. Create new feature combining existing features
相似特征结合，丢弃原有特征

    for dataset in combine:
        dataset['FamilySize'] = dataset['SibSp'] + dataset['Parch'] + 1

    train_df[['FamilySize', 'Survived']].groupby(['FamilySize'], as_index=False).mean().sort_values(by='Survived', ascending=False)

相互补充特征--某些特征的反面情况另外组成一个特征，增强此特征重要性

    for dataset in combine:
        dataset['IsAlone'] = 0
        dataset.loc[dataset['FamilySize'] == 1, 'IsAlone'] = 1

    train_df[['IsAlone', 'Survived']].groupby(['IsAlone'], as_index=False).mean()

丢弃原有特征

    train_df = train_df.drop(['Parch', 'SibSp', 'FamilySize'], axis=1)
    test_df = test_df.drop(['Parch', 'SibSp', 'FamilySize'], axis=1)
    combine = [train_df, test_df]

    train_df.head()


## 6. Completing a categorical feature
对于embark特征：

填补缺失值--众数


## 7. Converting categorical feature to numeric
对于embark特征：

转换为数值离散

    for dataset in combine:
        dataset['Embarked'] = dataset['Embarked'].map( {'S': 0, 'C': 1, 'Q': 2} ).astype(int)

    train_df.head()

## 8. Quick completing and converting a numeric feature
对于Fare特征，test有一个缺失值，用众数替代

    test_df['Fare'].fillna(test_df['Fare'].dropna().median(), inplace=True)
    test_df.head()


可转换为分段数值离散特征

    for dataset in combine:
        dataset.loc[ dataset['Fare'] <= 7.91, 'Fare'] = 0
        dataset.loc[(dataset['Fare'] > 7.91) & (dataset['Fare'] <= 14.454), 'Fare'] = 1
        dataset.loc[(dataset['Fare'] > 14.454) & (dataset['Fare'] <= 31), 'Fare']   = 2
        dataset.loc[ dataset['Fare'] > 31, 'Fare'] = 3
        dataset['Fare'] = dataset['Fare'].astype(int)

    train_df = train_df.drop(['FareBand'], axis=1)
    combine = [train_df, test_df]
        
    train_df.head(10)

# Model, predict and solve

当前问题：分类，回归

训练集构建

    X_train = train_df.drop("Survived", axis=1)
    Y_train = train_df["Survived"]
    X_test  = test_df.drop("PassengerId", axis=1).copy()
    X_train.shape, Y_train.shape, X_test.shape

### LR

    # Logistic Regression

    logreg = LogisticRegression()
    logreg.fit(X_train, Y_train)
    Y_pred = logreg.predict(X_test)
    acc_log = round(logreg.score(X_train, Y_train) * 100, 2)
    acc_log

验证先前的经验是否正确：--相关系数

    coeff_df = pd.DataFrame(train_df.columns.delete(0))
    coeff_df.columns = ['Feature']
    coeff_df["Correlation"] = pd.Series(logreg.coef_[0])

    coeff_df.sort_values(by='Correlation', ascending=False)

分析：
* 正相关系数提高准确性，负降低

### SVM

    # Support Vector Machines

    svc = SVC()
    svc.fit(X_train, Y_train)
    Y_pred = svc.predict(X_test)
    acc_svc = round(svc.score(X_train, Y_train) * 100, 2)
    acc_svc

### KNN

    knn = KNeighborsClassifier(n_neighbors = 3)
    knn.fit(X_train, Y_train)
    Y_pred = knn.predict(X_test)
    acc_knn = round(knn.score(X_train, Y_train) * 100, 2)
    acc_knn


### Bayes

    # Gaussian Naive Bayes

    gaussian = GaussianNB()
    gaussian.fit(X_train, Y_train)
    Y_pred = gaussian.predict(X_test)
    acc_gaussian = round(gaussian.score(X_train, Y_train) * 100, 2)
    acc_gaussian

### Perceptron

    # Perceptron

    perceptron = Perceptron()
    perceptron.fit(X_train, Y_train)
    Y_pred = perceptron.predict(X_test)
    acc_perceptron = round(perceptron.score(X_train, Y_train) * 100, 2)
    acc_perceptron


trick：

    pd.corr(),不算NA/null
    pd.hist

有多少缺失值

    null_columns=titanic.columns[titanic.isnull().any()]
    titanic.isnull().sum()


缺失值的相同与不同

    从其他数据中找到相似性
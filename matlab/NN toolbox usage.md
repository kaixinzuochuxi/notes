# MATLAB NN toolbox Usage

[toc]
## workflow
### 1. 数据
自行准备
格式要求：$w*h*c*num$

### 2. 网络搭建
#### 2.0 基本形式

搭建
```
layers = [
    imageInputLayer([28 28 1])
    convolution2dLayer(3,16,'Padding',1)
    batchNormalizationLayer
    reluLayer
    maxPooling2dLayer(2,'Stride',2)
    convolution2dLayer(3,32,'Padding',1)
    batchNormalizationLayer
    reluLayer
    fullyConnectedLayer(10)
    softmaxLayer
    classificationLayer];
```
#### 2.1 Image Input Layer
**描述**
* inputs images
* applies data normalization

**创建**
```
layer = imageInputLayer(inputSize)
layer = imageInputLayer(inputSize,Name,Value)
```
**属性**
* inputSize
    三维输入大小，[h w c]，row vector of three integer values
* DataAugmentation 
    不推荐使用
* Normalization
    是否标准化，char，'zerocenter' (default) | 'none'
* Name
    名字，char，'' (default) | character vector


#### 2.2 卷积
##### 2.2.1 Convolutional Layer
OutputSize=(InputSize – FilterSize + 2*Padding)/Stride + 1
**描述**


**创建**
```
layer = convolution2dLayer(filterSize,numFilters)
layer = convolution2dLayer(filterSize,numFilters,Name,Value)
```

**属性**
* Name
* 滤波器相关
    * FilterSize
        [h w]
    * NumFilters
        positive integer
    * NumChannels
        'auto' (default) | positive integer
    * Stride
        遍历步长，[a b]，[1 1] (default) | vector of two positive integers
* 输出尺寸
    * PaddingSize
        增加全0行/列，[t b l r]上下左右，[0 0 0 0] (default) | vector of four nonnegative integers
    * PaddingMode  
        Method to determine padding size，'manual' (default) | 'same'
        若same，输出与输入一样大小
    * Padding-- will be removed
        水平竖直加行列，[a b]，[0 0] (default) | vector of two nonnegative integers
* 权重相关
    * Weights-可搭建网络后手动初始化
        4-D array，FilterSize(1)--FilterSize(2)--NumChannels--NumFilters
    * Bias-可搭建网络后手动初始化
        3-D array，1--1--NumFilters
    * WeightLearnRateFactor
        权重学习速率，实际速率需乘上global learning rate，1 (default) | nonnegative scalar
    * BiasLearnRateFactor
        权重学习速率，实际速率需乘上global learning rate，1 (default) | nonnegative scalar
    * WeightL2Factor
        正则化系数比例，实际需乘上global L2 regularization factor
    * BiasL2Factor
        正则化系数比例，实际需乘上global L2 regularization factor


##### 2.2.2 Batch Normalization Layer

**描述**

* normalizes each input channel across a mini-batch.
* 步骤
    * 标准化
    * 线性变换
        * learnable offset $β$
        * learnable scale factor $γ$
* 功能
    * speed up training
    * reduce the sensitivity to network initialization

**创建**
```
layer = batchNormalizationLayer
layer = batchNormalizationLayer(___,'Name',Value)
```
**属性**
* Name — Layer name
    '' (default) | character vector
* NumChannels — Number of input channels
    'auto' (default) | positive integer，equal to the number of channels of the input to the layer，
* Epsilon — Constant to add to mini-batch variances
    防止除0，1e-5 (default) | numeric scalar

* 训练参数Learnable Parameters
    * Offset — Channel offsets
        Channel offsets β, specified as a numeric array of size 1-by-1-by-NumChannels
        初始未指定则为0，其后在训练中学习
    * OffsetLearnRateFactor — Learning rate factor for offsets
        1 (default) | nonnegative scalar
    * OffsetL2Factor — L2 regularization factor for offsets
    * Scale — Channel scale factors
        Channel scale factors γ, specified as a numeric array of size 1-by-1-by-NumChannels with positive elements.
        初始未指定则为1，其后在训练中学习
    * ScaleLearnRateFactor — Learning rate factor for scale factors
    * ScaleL2Factor — L2 regularization factor for scale factors

* 训练中决定：
    * TrainedMean — Input mean
        1-by-1-by-NumChannels
    * TrainedVariance — Input variance
        1-by-1-by-NumChannels


##### 2.2.3 非线性函数层

ReLU Layer

**描述**

**创建**
```
layer = reluLayer
layer = reluLayer('Name',Name)
```
**属性**
* Name — Layer name
    '' (default) | character vector



##### 2.2.4 池化层
######AveragePooling2DLayer
 OutputSize=(InputSize – PoolSize + 2*PaddingSize)/Stride + 1
**描述**
down-sampling in rectangular pooling regions and computing the average values of each region
**创建**
```
layer = averagePooling2dLayer(poolSize)
layer = averagePooling2dLayer(poolSize,Name,Value)
```
**属性**

* PoolSize — Dimensions of pooling regions
    [h w]，或单一参数表示正方形
* Stride — Step size for traversing input
    [1 1] (default) | vector of two positive integers
    If the stride dimensions Stride are less than the respective pooling dimensions, then the pooling regions overlap.

* PaddingSize — Size of padding
    [t b l r]，[0 0 0 0] (default) | vector of four nonnegative integers
* PaddingMode — Method to determine padding size
* Padding — Size of padding


######MaxPooling2DLayer

**描述**

**创建**
```
layer = maxPooling2dLayer(poolSize)
layer = maxPooling2dLayer(poolSize,Name,Value)
```
**属性**
* PoolSize — Dimensions of pooling regions
    [h w]，或单一参数表示正方形
* Stride — Step size for traversing input
    [1 1] (default) | vector of two positive integers
    If the stride dimensions Stride are less than the respective pooling dimensions, then the pooling regions overlap.

* PaddingSize — Size of padding
    [t b l r]，[0 0 0 0] (default) | vector of four nonnegative integers
* PaddingMode — Method to determine padding size
* Padding — Size of padding

* HasUnpoolingOutputs — Flag for outputs to unpooling layer
    false (default) | true
    must be nonoverlapping
    * false：single output with the name 'out'
    * true：two additional outputs，
        * indices， maximum value in each pooled region
        * size，Size of the input feature map
######MaxUnpooling2DLayer

**描述**
There are three inputs to this layer:
* 'in' — Input feature map to unpool.
* 'indices' — Indices of the maximum value in each pooled region. This is output by the max pooling layer.
* 'size' — Output size of unpooled feature map. This is output by the max pooling layer.

**创建**
```
layer = maxUnpooling2dLayer
layer = maxUnpooling2dLayer('Name',Name)
```
**属性**
* Name — Layer name


##### 2.2.5 Dropout Layer

**描述**
randomly sets input elements to zero with a given probability.
**创建**
```
layer = dropoutLayer
layer = dropoutLayer(probability)
layer = dropoutLayer(___,'Name',Name)
```
**属性**
* Probability — Probability to drop out input elements
    0.5 (default) | numeric scalar in the range 0 to 1
* Name — Layer name


#### 2.3 全连接层

**描述**

**创建**
```
layer = fullyConnectedLayer(outputSize)
layer = fullyConnectedLayer(outputSize,Name,Value)
```
**属性**
* Name — Layer name
* InputSize — Input size
    'auto' (default) | positive integer
* OutputSize — Output size
    positive integer
* Weights — Layer weights
* Bias — Layer biases

* WeightLearnRateFactor — Learning rate factor for the weights
* BiasLearnRateFactor — Learning rate factor for biases
* WeightL2Factor — L2 regularization factor for the weights
* BiasL2Factor — L2 regularization factor for biases


#### 2.4 输出层
##### 2.4.1 分类
###### 2.4.1.1 softmaxLayer
```
layer = softmaxLayer
layer = softmaxLayer('Name',Name)
```
###### 2.4.1.2 classificationLayer
```
coutputlayer = classificationLayer()
coutputlayer = classificationLayer('Name',Name)
```

损失函数为交叉熵

##### 2.4.2 回归--regressionLayer
```
routputlayer = regressionLayer
routputlayer = regressionLayer('Name',Name)
```

LossFunction: 'mean-squared-error'

### 3. 配置
#### 3.1 trainingOptions
**描述**
Options for training neural network
**创建**
```
options = trainingOptions(solverName)
options = trainingOptions(solverName,Name,Value)
```
**属性**

* solverName — Solver for training network
* 硬件
    * 'ExecutionEnvironment' — Hardware resource for training network
        'auto' (default) | 'cpu' | 'gpu' | 'multi-gpu' | 'parallel'
    * 'WorkerLoad' — Worker load division
        evenly divided (default) | numeric vector
* 可视化
    * 'Plots' — Plots to display during network training
        'none' (default) | 'training-progress'
    * 'Verbose' — Indicator to display training progress information
        1 (default) | 0
    * 'VerboseFrequency' — Frequency of verbose printing
        50 (default) | positive integer
* 中止与恢复
    * 'CheckpointPath' — Path for saving checkpoint networks
        char，'' (default) | character vector
        每轮训练完，分配独立名字存储，可继续


* 学习速率
    * 'InitialLearnRate' — Initial learning rate
        0.01 (default) | positive scalar
    * 'LearnRateSchedule' — Option for dropping learning rate during training
        'none' (default) | 'piecewise'
        'piecewise' —updates the learning rate every certain number of epochs by multiplying with a certain factor
        
    * 'LearnRateDropFactor' — Factor for dropping the learning rate
        0.1 (default) | scalar from 0 to 1
    * 'LearnRateDropPeriod' — Number of epochs for dropping the learning rate
        10 (default) | positive integer



* 训练参数
    * 'MaxEpochs' — Maximum number of epochs
        30 (default) | positive integer
    * 'MiniBatchSize' — Size of mini-batch
        128 (default) | positive integer
    * 'Momentum' — Contribution of previous gradient step
        0.9 (default) | a scalar from 0 to 1
    * 'Shuffle' — Option for data shuffling,训练之前随机排序
        'once' (default) | 'never' | 'every-epoch'
    * 'L2Regularization' — Factor for L2 regularizer
        0.0001 (default) | nonnegative scalar

* 'OutputFcn' — Output functions
    function handle | cell array of function handles
    ````````````````````````````````
* 'ValidationData' — Data to use for validation during training
    ImageDatastore | table | cell array
* 'ValidationFrequency' — Frequency of network validation
    50 (default) | positive integer
* 'ValidationPatience' — Patience of validation stopping
    5 (default) | positive integer | Inf


* 'SequenceLength' — Option to pad, truncate, or split input sequences
    'longest' (default) | 'shortest' | positive integer
* 'SequencePaddingValue' — Value to pad input sequences
    0 (default) | scalar
    
### 4. 初始化
### 5. 训练
### 6. 验证
### 7. 使用


**描述**

**创建**

**属性**








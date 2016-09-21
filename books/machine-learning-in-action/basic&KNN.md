#第一章

	生成随机数组：   	random.rand(m,n)
	数组转换为矩阵：	MAT=mat(array)
	矩阵求逆			invMAT=MAT.I
	创建单位矩阵		eye(m,n)
	
##一般步骤
###对数据
* 收集
* 准备
* 分析

###对算法
* 训练
* 测试
* 使用


#KNN
###优点
精度高，对异常值不敏感，对数据无先验假设
###缺点
计算复杂度大，空间复杂度高
###适用范围
数值型和标称型

##实例
###电影分类
特征：特定的镜头数   
新实例已知特定镜头数，与已知标签样本计算不同种镜头数的距离

####算法：

	For every point in our dataset:
		calculate the distance between inX and the current point
		sort the distances in increasing order
		take k items with lowest distances to inX
		find the majority class among these items
		return the majority class as our prediction for the class of inX

####新函数
#####numpy.shape
	
	返回一个元组，矩阵每一维的变量数，若变量中还含有矩阵则继续返回里面的变量数
	>>group = numpy.array([[1.0,1.1],[1.0,1.0],[0,0],[0,0.1]])
	>>group.shape
	>>(4L, 2L)
#####numpy.tile

	把矩阵按每一个轴重复若干次，返回
	numpy.tile(A, reps)

#####numpy。sum()

	numpy array存储：[[],[],[],[],[]]
	基本单位：行向量，加一维为列向量，以此类推
	每个list表示一行，行向量，[i][j]对应	
	axis=0,全部相加，得到一个数
	axis=1，每个list相加，得到一个list，按列访问
#####numpy.argsort

	numpy.argsort(a, axis=-1, kind='quicksort', order=None)[source]
	axis，根据哪一维排序
	返回原有array中的序号，多维按照行/列每一维进行，大小与原来一样
	b=a.argsort()
	第i个数：a[b[i]]
####代码

	def createDataSet():
		group = array([[1.0,1.1],[1.0,1.0],[0,0],[0,0.1]])
		labels = ['A','A','B','B']
		return group, labels	

	def classify0(inX, dataSet, labels, k):
		dataSetSize = dataSet.shape[0]
		diffMat = tile(inX, (dataSetSize,1)) – dataSet		
		sqDiffMat = diffMat**2							
		sqDistances = sqDiffMat.sum(axis=1)
		distances = sqDistances**0.5
		sortedDistIndicies = distances.argsort()
		classCount={}
		for i in range(k):
			voteIlabel = labels[sortedDistIndicies[i]]
			classCount[voteIlabel] = classCount.get(voteIlabel,0) + 1
		sortedClassCount = sorted(classCount.iteritems(),key=operator.itemgetter(1), reverse=True)
		return sortedClassCount[0][0]

	求解欧氏距离；矩阵扩展处理；矩阵元素幂；k投票；



###约会网站配对效果
特征：   
自定义
####算法
读取文件：
* 打开，得到文件行数
* 初始化矩阵（全0）
* 循环处理每一行
	* 截掉回车字符
	* 分割一行为元素列表
	* 选数值，存入

归一化：

	newValue = (oldValue-min)/(max-min)

测试：

####函数
#####max,min
	
	选轴，然后按其他轴处理
####代码

	def file2matrix(filename):
    	fr = open(filename)
    	numberOfLines = len(fr.readlines())         #get the number of lines in the file
    	returnMat = zeros((numberOfLines,3))        #prepare matrix to return
    	classLabelVector = []                       #prepare labels return   
    	fr = open(filename)
    	index = 0
    	for line in fr.readlines():
    	    line = line.strip()
    	    listFromLine = line.split('\t')
    	    returnMat[index,:] = listFromLine[0:3]				#索引+占位，实际上为数据分行，构建行向量
    	    classLabelVector.append(int(listFromLine[-1]))
    	    index += 1
    	return returnMat,classLabelVector

	读取文件；索引+占位；

	def autoNorm(dataSet):
    	minVals = dataSet.min(0)
    	maxVals = dataSet.max(0)
    	ranges = maxVals - minVals
    	normDataSet = zeros(shape(dataSet))
    	m = dataSet.shape[0]
    	normDataSet = dataSet - tile(minVals, (m,1))
    	normDataSet = normDataSet/tile(ranges, (m,1))   #element wise divide
    	return normDataSet, ranges, minVals


	def datingClassTest():
    	hoRatio = 0.50      #hold out 10%
    	datingDataMat,datingLabels = file2matrix('datingTestSet2.txt')       #load data setfrom file
    	normMat, ranges, minVals = autoNorm(datingDataMat)
    	m = normMat.shape[0]
    	numTestVecs = int(m*hoRatio)			#验证集个数
    	errorCount = 0.0
    	for i in range(numTestVecs):
        	classifierResult = classify0(normMat[i,:],normMat[numTestVecs:m,:],datingLabels[numTestVecs:m],3)
        	print "the classifier came back with: %d, the real answer is: %d" % (classifierResult, datingLabels[i])
        	if (classifierResult != datingLabels[i]): errorCount += 1.0
    	print "the total error rate is: %f" % (errorCount/float(numTestVecs))
    	print errorCount


	预测略


###手写系统识别--数字
####步骤
读取图像

将图像转换为测试向量

识别
####函数
#####os.listdir()
返回list，元素为文件名的字符串
####代码

	def img2vector(filename):
    	returnVect = zeros((1,1024))
    	fr = open(filename)
    	for i in range(32):
        	lineStr = fr.readline()
        	for j in range(32):
            	returnVect[0,32*i+j] = int(lineStr[j])
    	return returnVect


	def handwritingClassTest():
    	hwLabels = []
    	trainingFileList = listdir('trainingDigits')           #load the training set
    	m = len(trainingFileList)
    	trainingMat = zeros((m,1024))
    	for i in range(m):
        	fileNameStr = trainingFileList[i]
        	fileStr = fileNameStr.split('.')[0]     #take off .txt
        	classNumStr = int(fileStr.split('_')[0])
        	hwLabels.append(classNumStr)
        	trainingMat[i,:] = img2vector('trainingDigits/%s' % fileNameStr)
    	testFileList = listdir('testDigits')        #iterate through the test set
    	errorCount = 0.0
    	mTest = len(testFileList)
    	for i in range(mTest):
        	fileNameStr = testFileList[i]
        	fileStr = fileNameStr.split('.')[0]     #take off .txt
        	classNumStr = int(fileStr.split('_')[0])
        	vectorUnderTest = img2vector('testDigits/%s' % fileNameStr)
        	classifierResult = classify0(vectorUnderTest, trainingMat, hwLabels, 3)
        	print "the classifier came back with: %d, the real answer is: %d" % (classifierResult, classNumStr)
        	if (classifierResult != classNumStr): errorCount += 1.0
    	print "\nthe total number of errors is: %d" % errorCount
    	print "\nthe total error rate is: %f" % (errorCount/float(mTest))


	获取目录内容，数目；




































	













































































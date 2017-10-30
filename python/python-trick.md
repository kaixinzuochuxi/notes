无输入时默认输入
	
	name=inpute() or 'default'

确保输入用户名不为空：
	
		name=''
		while not name:
			name=inpute()
* 问题：输入为空格有瑕疵，修改：

		name=''
		while not name or name.isspace():
			name=inpute()
	

用户在提示符下输入单词，不输入时结束：
		
		#含有哑值
		word='a'
		while word:
			word=input()
			pass
		#修改为不含哑值但有重复代码
		word=input()
		while word:
			pass
			word=input()
		#更完美代码
		while True:
			word=input()
			if not word:	break
			pass
		#死循环+break-------判断循环
男孩女孩首字母相同则匹配更高效方法：
	
	#依据匹配规则构建一方字典，k-v：规则项-值，列表推导时将另一方规则作为k输入字典
	girls=[...]
	boys=[...]
	lettergirls={}
	for girl in girls:
		lettergirls.setdefault(girl[0].[]).append(girl)
	print [b+'+'+g for b in boys for g in lettergirls[b[0]]]

按照路径导包

	import sys
	sys.path.append('D:/machinelearninginaction/Ch02')
	from KNN import *


numpy数组去掉一行/一列

    for featVec in dataSet:
        if featVec[axis] == value:				#判断条件
            reducedFeatVec = featVec[:axis]     #chop out axis used for splitting
            reducedFeatVec.extend(featVec[axis+1:])
            retDataSet.append(reducedFeatVec)

检查list内元素是否一样

	if classList.count(classList[0]) == len(classList): 
	        return classList[0]	



均值滤波边界条件的判别：

	neighbors = [
                    M[_x][_y]
                    for _x in (x-1, x, x+1)
                    for _y in (y-1, y, y+1)
                    if 0 <= _x < x_len and 0 <= _y < y_len
                ]























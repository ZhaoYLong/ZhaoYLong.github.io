---
layout:     post
title:      Python & Sphfile格式

subtitle:   这是IDEA
date:       2019-10-12
author:     Laqudee.Z
header-img: img/post-bg-debug.png
catalog: false
tags:
    - IDEA
    - Python
    - DL/ML
---

- 直接上代码吧，代码里啥都有

```py
# coding=utf-8   

from sphfile import SPHFile
import glob
import os

def trainFun(pathName):

	sph_files = glob.glob(pathName)
	print(len(sph_files), "train utterences")
	for i in sph_files:
		sph = SPHFile(i)
		# 生成新的语音文件，并删除旧的文件    

		sph.write_wav(filename=i.replace(".wav", "_n.wav"))   
		os.remove(i) # 删除原语音文件

	print("Completed")


def testFun(pathName):
	sph_files = glob.glob(pathName)
	print(len(sph_files), "test utterences")
	for i in sph_files:
		sph = SPHFile(i)
		# 生成新的语音文件，并删除旧的文件

		sph.write_wav(filename=i.replace(".wav", "_n.wav"))

		os.remove(i)  # 删除原语音文件

	print("Completed")

# 批量修改改过格式的文件名

def filerenames(pathName):    
	
	# 返回指定路径文件夹中所有文件  

	filename_list = os.listdir(pathName)

	a = 0

	for i in filename_list:
		used_name = pathName + filename_list[a]

		filename_list[a] = filename_list[a].replace("_n", "")

		new_name = pathName + filename_list[a]

		os.rename(used_name, new_name)
		# 输出看效果

		print("文件%s重命名成功,新的文件名为%s" %(used_name,new_name))

		a +=1

if __name__ == "__main__":
	pathList = ["test", "train"]  
	# 这里的URL换成你本地的文件存放地址

	testPath = '/Users/a1/Desktop/timit/wav/' + pathList[0] + '/*/*.wav'

	trainPath = '/Users/a1/Desktop/timit/wav/' + pathList[1] + '/*/*.wav'

	pathTrain = '/Users/a1/Desktop/timit/wav/train/'

	pathTest = '/Users/a1/Desktop/timit/wav/test/'

	# 调用格式转换函数对test和train集合里的文件进行转换

	testFun(testPath)

	trainFun(trainPath)

	# 下面是对转换后的文件名就行替换修改

	fileTrain_set = os.listdir(pathTrain)

	for x in fileTrain_set:
		x_new = '/Users/a1/Desktop/timit/wav/train/' + x + '/'  
		filerenames(x_new)  

	fileTest_set = os.listdir(pathTest)
	for x in fileTest_set:
		x_new = '/Users/a1/Desktop/timit/wav/test/' + x + '/'
		filerenames(x_new)

# 代码中出现的url都需要换成你本地的文件地址

# 我写python有点手生了。虽然转换成功，且符合要求。但不太简洁美观。

```

> 为你➕🌟🌟🌟➕
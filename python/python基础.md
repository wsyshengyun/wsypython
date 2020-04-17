## 列表推导式

```
def get_headers(header_raw):  
			return dict(line.split(": ", 1) for line in header_raw.split("\n"))  
```

## 字典

### 字典添加字典

```
dic = {1:2} 
newdic = {'a':1}.update(dic)   # 这种写是错误的  结果为None  
----------------
newdic = {'a':1}
newdic.update(dic)


```

## json

dataStr = json.dumps(python对象)  :   编码成字符串

datapython = json.loads(strpython) : 解码成python对角

与文件打交道

json.dump(python对象, file):   将json写入文件 ; 返回None

datapython = json.load(file): 读取json文件 

> **json需要一次性写入,不能以追加的方式多次写入, 否则读取的时候就会出错;**  





## 文件

文件中间插入字符串

需要把文件读取出来,修改后再全部写入文件.  



## 技巧

### lambda 作为判断条件

![image-20200416212106935](/home/wsy/.config/Typora/typora-user-images/image-20200416212106935.png)
#  节点

## 节点的类型

- bs4.element.Tag

```
此类型可以应用方法选择器；　比如tag类型为此类型，那么可以使用
tag.find_all \ tag.find 等方法查找他的“子”
```

### 全部兄弟节点

> **知识点：.next_siblings  .previous_siblings 属性**

### **前后节点**

> **知识点：.next_element  .previous_element 属性**

与 .next_sibling  .previous_sibling 不同，它并不是针对于兄弟节点，而是在所有节点，不分层次

### **所有前后节点**

> **知识点：.next_elements  .previous_elements 属性**

通过 `.next_siblings` 和 `.previous_siblings` 属性可以对当前节点的兄弟节点迭代输出

## 节点的各种值

### 属性值

- tag['id'] 

- tag.attrs['id']

### 获取文本

- tag.string
- tag.get_text()
- tag.has_attr(‘class’)

## 节点的内容

.string属性   



## 多个内容

.**strings**

```
不过要遍历获取  for i in soup.strings:
```

**.stripped_strings** 

输出的字符串中可能包含了很多空格或空行,使用 `.stripped_strings` 可以去除多余空白内容



## 子

- ### .children

返回的是一个子元素的迭代器

> 使用的时候，next(iter) 或者 for i in iter:....
>
> 

- ### .contents

返回的是一个列表；列表为所有的子元素

> 注意：‘\n’也可以是一个元素

## 子孙

.descendants

是一个什么类型呢?

# 方法选择器

## ｆｉｎｄ＿ａｌｌ（name, attrs, recursive, text, **kwargs）

`查询符合条件的元素`

### name参数

- A 传字符串

- B传正则表达式

  ```
  import re
  for tag in soup.find_all(re.compile("^b")):
      print(tag.name)
  # body
  # b
  ```

  

- C传列表

  ```
  soup.find_all(["a", "b"])	
  ```

  

- D传True

- E传方法 

```
soup.find_all(has_class_but_no_id)
# [<p class="title"><b>The Dormouse's story</b></p>,
#  <p class="story">Once upon a time there were...</p>,
#  <p class="story">...</p>]
```

### keyword参数

- id = ‘link2’ 

- href = re.compile(‘elsie’)

- href=re..., id = ‘link1’ 混合

- class = ‘’, 要改为 class_ = ‘’ # class是python的关键词

- data* 属性不能使用,但可以

  ```
  attrs = {'data-foo':'value'}
  ```



### text 参数

- text = ‘Elsie’
- text = [‘Tillie’, ‘Elsie’, ‘Lacie’]
- text=re.compile(‘Dormouse’)



### limit参数

- limit=2





### recursive参数 

默认为True, 会检索当前tag的所有子孙节点;  如果只搜索tag的直接子节点,可以使用recursive=False

## 查询的结果

结果为一个列表；列表元素为一个ｔａｇ类型的元素

## 参数说明

### name

这个不用说，标签的名字为参数，　比如‘p’,但是只能是一个标签名字　　

### attrs

这个参数是以标签元素的属性做为参数。

- 可以看成一个字典；　参数可以直接传入一个字典，也可以这样传入：attrs={....}

- 对于一些常用的属性，简单的可以直接传入参数比如{‘id’:‘list-1’}

可以这样写　　id=‘list-1’

- 对于class属性｛‘class’:’element’｝可以写为　class_=’element’

注意class下面要加下划线　　

- 对于一个属性多个值，可以将多个值加入列表。

  {‘class’:  [‘attr1’, ‘attr2’]}



### text参数　　

- text=‘’
- text=re.compile(‘link’)



# 其它方法

### find_parents()和find_parent() 

前者返回所有祖先节点，后者返回直接父节点。

###  find_next_siblings()和find_next_sibling()：

前者返回后面所有的兄弟节点，后者返回后面第一个兄弟节点。

### find_all_next（）

find_next() 前者返回的所有符合条件的节点；后者返回第一个符合条件的节点　　

### find_all_previous() 

find_previous() 前者返回所有符合条件的节点；后者返回第一个符合条件的节点　

### find()

类似于find_all() , 只返回第一个符号条件的节点



# ＣＳＳ选择器　　

## select()

传入相应的ｃｓｓ选择器即可　　

- 对于属性

‘.父属性名　空格　．子属性名’

- 对于标签

＇父标签名　空格　　子标签名＇

- 对于id

＇#id值＇

-  以上可以混合使用

### 结果

为tag元素的列表

# BeautifulSoup检索多级标签

- 对于这样的多级标签

  ```
  <li class="l_reply_num" style="margin-left:8px">
  	<span class="red" style="margin-right:3px">4790</span>回复贴，共
  	<span class="red">36</span>页
  </li>, <li class="l_reply_num">
  ```

- 要获取第二个span中的内容，可以这样写：

```
url=urlopen(url)
soup=BeautifulSop(url,'html.parse')//加html.parse代表识别为html语言
total=soup.find_all('li',class_='l_reply_num')//获取到整个li保存到total
res=total[0].contents[2]//获取第一个li标签下的第三个元素，即为：<span class="red">36</span>
result=res.string//获取到第三个span中的36
```

html = '<div class="text1"> \
\- 乐视网即将复牌 关注分级基金溢价机会 </br> \
\- 航信转债 6 月 8 日付息登记日，每张 0.2 元</br> \
\- 量化模型中不同买卖时间的效果对比 & 今日翻多创业板指数</br> \
\- 不惑之年魔都土著夫妇如何保住自己的养老积蓄？</br></div>'
soup = BeautifulSoup(html, 'lxml')
text = soup.find('div', 'text1').get_text(strip=True).encode('utf-8')
print text
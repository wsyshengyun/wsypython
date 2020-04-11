## Selenium的使用



## 基本使用



首先我们来大体看一下Selenium有一些怎样的功能，先用一段实例代码来演示一下：

```text
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait

browser = webdriver.Chrome()
try:
    browser.get('https://www.baidu.com')
    input = browser.find_element_by_id('kw')
    input.send_keys('Python')
    input.send_keys(Keys.ENTER)
    wait = WebDriverWait(browser, 10)
    wait.until(EC.presence_of_element_located((By.ID, 'content_left')))
    print(browser.current_url)
    print(browser.get_cookies())
    print(browser.page_source)
finally:
    browser.close()
```

运行代码之后，如果正确配置好了ChromeDriver，可以发现会自动弹出一个浏览器，浏览器首先会跳转到百度，然后在搜索框中输入Python进行搜索，然后跳转到搜索结果页，等待搜索结果加载出来之后，控制台分别会输出当前的URL，当前的Cookies还有网页源代码。



控制台输出结果：

```text
https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=0&rsv_idx=1&tn=baidu&wd=Python&rsv_pq=c94d0df9000a72d0&rsv_t=07099xvun1ZmC0bf6eQvygJ43IUTTUOl5FCJVPgwG2YREs70GplJjH2F%2BCQ&rqlang=cn&rsv_enter=1&rsv_sug3=6&rsv_sug2=0&inputT=87&rsv_sug4=87
[{'secure': False, 'value': 'B490B5EBF6F3CD402E515D22BCDA1598', 'domain': '.baidu.com', 'path': '/', 'httpOnly': False, 'name': 'BDORZ', 'expiry': 1491688071.707553}, {'secure': False, 'value': '22473_1441_21084_17001', 'domain': '.baidu.com', 'path': '/', 'httpOnly': False, 'name': 'H_PS_PSSID'}, {'secure': False, 'value': '12883875381399993259_00_0_I_R_2_0303_C02F_N_I_I_0', 'domain': '.www.baidu.com', 'path': '/', 'httpOnly': False, 'name': '__bsi', 'expiry': 1491601676.69722}]
<!DOCTYPE html><!--STATUS OK-->...</html>
```

源代码过长在此省略，那么当前的URL，Cookies，源代码都是浏览器中的真实内容。所以说，如果我们用Selenium来驱动浏览器加载网页的话，我们就可以拿到JavaScrit渲染的结果了。

下面我们来详细介绍一下Selenium的用法。

## 声明浏览器对象



Selenium支持非常多的浏览器，如Chrome、Firefox、Edge等，还有手机端的浏览器Android、BlackBerry等，另外无界面浏览器PhantomJS也同样支持。

我们可以用如下的方式初始化：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser = webdriver.Firefox()
browser = webdriver.Edge()
browser = webdriver.PhantomJS()
browser = webdriver.Safari()
```

这样我们就完成了一个浏览器对象的初始化，接下来我们要做的就是调用browser对象，让其执行各个动作，就可以模拟浏览器操作了。

## 访问页面



我们可以用get()方**法**来请求一个网页，参数传入链接URL即可，比如在这里我们用get()方法访问淘宝，然后打印出源代码，代码如下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
print(browser.page_source)  #
browser.close()
```

## 查找元素



### 单个元素



Selenium可以驱动浏览器完成各种操作，比如填充表单，模拟点击等等，比如我们想要完成向某个输入框输入文字的操作，总得需要知道这个输入框在哪里吧？所以Selenium提供了一系列查找元素的方法，我们可以用这些方法来获取想要的元素，以便于下一步执行一些动作或者提取信息。

比如我们想要从淘宝页面中提取搜索框这个元素，首先观察它的源代码：



可以发现它的ID是q，Name也是q，还有许多其他属性，我们获取它的方式就有多种形式了，比如find_element_by_name()是根据Name值获取，ind_element_by_id()是根据ID获取，另外还有根据XPath、CSS Selector等获取。

我们代码实现一下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
input_first = browser.find_element_by_id('q')
input_second = browser.find_element_by_css_selector('#q')
input_third = browser.find_element_by_xpath('//*[@id="q"]')
print(input_first, input_second, input_third)
browser.close()
```

在这里我们使用了三种方式获取输入框，根据ID，CSS Selector，和XPath获取，它们返回的结果是完全一致的。

运行结果：

```text
<selenium.webdriver.remote.webelement.WebElement (session="5e53d9e1c8646e44c14c1c2880d424af", element="0.5649563096161541-1")> 
<selenium.webdriver.remote.webelement.WebElement (session="5e53d9e1c8646e44c14c1c2880d424af", element="0.5649563096161541-1")> 
<selenium.webdriver.remote.webelement.WebElement (session="5e53d9e1c8646e44c14c1c2880d424af", element="0.5649563096161541-1")>
```

可以看到三个元素都是WebElement类型，是完全一致的。

在这里列出所有获取单个元素的方法：

find_element_by_idfind_element_by_namefind_element_by_xpathfind_element_by_link_textfind_element_by_partial_link_textfind_element_by_tag_namefind_element_by_class_namefind_element_by_css_selector

另外Selenium还提供了通用的find_element()方法，它需要传入两个参数，一个是查找的方式By，另一个就是值，实际上它就是find_element_by_id()这种方法的通用函数版本，比如find_element_by_id(id)就等价于find_element(By.ID, id)。

我们用代码实现一下：

```text
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
input_first = browser.find_element(By.ID, 'q')
print(input_first)
browser.close()
```

这样的查找方式实际上功能和上面列举的查找函数完全一致，不过参数更加灵活。

### 多个元素



如果我们查找的目标在网页中只有一个，那么完全可以用find_element()方法，但如果有多个元素，再用find_element()方法查找就只能得到第一个元素了，如果要查找所有满足条件的元素，那就需要用find_elements()这样的方法了，方法名称中element多了一个s。

比如我们在这里查找淘宝导航条的所有条目就可以这样来写：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
lis = browser.find_elements_by_css_selector('.service-bd li')
print(lis)
browser.close()
```

运行结果：

```text
[<selenium.webdriver.remote.webelement.WebElement (session="c26290835d4457ebf7d96bfab3740d19", element="0.09221044033125603-1")>, <selenium.webdriver.remote.webelement.WebElement (session="c26290835d4457ebf7d96bfab3740d19", element="0.09221044033125603-2")>, <selenium.webdriver.remote.webelement.WebElement (session="c26290835d4457ebf7d96bfab3740d19", element="0.09221044033125603-3")>...<selenium.webdriver.remote.webelement.WebElement (session="c26290835d4457ebf7d96bfab3740d19", element="0.09221044033125603-16")>]
```

在此简化了一下输出结果，中间部分省略。

可以看到得到的内容就变成了list类型，list的每个元素都是WebElement类型。

也就是说，如果我们用find_element()方法，只能获取匹配的第一个元素，结果是WebElement类型，如果用find_elements()方法，则结果是list类型，listd每个元素是WebElement类型。

函数的列表如下：

find_elements_by_idfind_elements_by_namefind_elements_by_xpathfind_elements_by_link_textfind_elements_by_partial_link_textfind_elements_by_tag_namefind_elements_by_class_namefind_elements_by_css_selector

当然我们和刚才一样，也可可以直接find_elements()方法来选择，所以也可以这样来写：

```text
lis = browser.find_elements(By.CSS_SELECTOR, '.service-bd li')
```

结果是完全一致的。

## 元素交互



Selenium可以驱动浏览器来执行一些操作，也就是说我们可以让浏览器模拟执行一些动作，比较常见的用法有：

输入文字用send_keys()方法，清空文字用clear()方法，另外还有按钮点击，用click()方法。

我们用一个实例来感受一下：

```text
from selenium import webdriver
import time

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
input = browser.find_element_by_id('q')
input.send_keys('iPhone')
time.sleep(1)
input.clear()
input.send_keys('iPad')
button = browser.find_element_by_class_name('btn-search')
button.click()
```

在这里我们首先驱动浏览器打开淘宝，然后用find_element_by_id()方法获取输入框，然后用send_keys()方法输入iPhone文字，等待一秒之后用clear()方法清空输入框，再次调用send_keys()方法输入iPad文字，之后再用find_element_by_class_name()方法获取搜索按钮，最后调用click()方法完成搜索动作。

通过上面的方法我们就完成了一些常见元素的动作操作，更多的操作可以参见官方文档的交互动作介绍：[http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.remote.webelement](https://link.zhihu.com/?target=http%3A//selenium-python.readthedocs.io/api.html%23module-selenium.webdriver.remote.webelement)

## 动作链



在上面的实例中，一些交互动作都是针对某个元素执行的，比如输入框我们就调用它的输入文字和清空文字方法，按钮就调用它的点击方法，其实还有另外的一些操作它是没有特定的执行对象的。比如鼠标拖拽，键盘按键等操作。所以这些动作我们有另一种方式来执行，那就是加到动作链中。

比如我们现在实现一个元素拖拽操作，将某个元素从一处拖拽到另外一处，我们用代码来感受一下：

```text
from selenium import webdriver
from selenium.webdriver import ActionChains

browser = webdriver.Chrome()
url = 'http://www.runoob.com/try/try.php?filename=jqueryui-api-droppable'
browser.get(url)
browser.switch_to.frame('iframeResult')
source = browser.find_element_by_css_selector('#draggable')
target = browser.find_element_by_css_selector('#droppable')
actions = ActionChains(browser)
actions.drag_and_drop(source, target)
actions.perform()
```

首先我们打开网页中的一个拖拽实例，然后依次选中要被拖拽的元素和拖拽到的目标元素，然后声明了ActionChains对象赋值为actions变量，然后通过调用actions变量的drag_and_drop()方法，然后再调用perform()方法执行动作，就完成了拖拽操作。





更多的动作链操作可以参考官方文档的动作链介绍：[http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.action_chains](https://link.zhihu.com/?target=http%3A//selenium-python.readthedocs.io/api.html%23module-selenium.webdriver.common.action_chains)

## 执行JavaScript



另外对于某些操作，API没有提供的，如下拉进度条等，可以直接模拟运行JavaScript，使用execute_script()方法。

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.zhihu.com/explore')
browser.execute_script('window.scrollTo(0, document.body.scrollHeight)')
browser.execute_script('alert("To Bottom")')
```

在这里我们就利用了execute_script()方法将进度条下拉到最底部，然后弹出alert提示框。

所以说有了这个，基本上API没有提供的所有的功能都可以用执行JavaScript的方式来实现了。

## 获取元素信息



我们在前面说过通过page_source属性可以获取网页的源代码，获取源代码之后就可以使用解析库如正则、BeautifulSoup、PyQuery等来提取信息了。

不过既然Selenium已经提供了选择元素的方法，返回的是WebElement类型，那么它也有相关的方法和属性来直接提取元素信息，如属性、文本等等。这样的话我们能就不用通过解析源代码来提取信息了，非常方便。

那接下来我们就看一下可以通过怎样的方式来获取元素信息吧。

### 获取属性



我们可以使用get_attribute()方法来获取元素的属性，那么这个的前提就是先选中这个元素。

我们用一个实例来感受一下：

```text
from selenium import webdriver
from selenium.webdriver import ActionChains

browser = webdriver.Chrome()
url = 'https://www.zhihu.com/explore'
browser.get(url)
logo = browser.find_element_by_id('zh-top-link-logo')
print(logo)
print(logo.get_attribute('class'))
```

运行之后程序便会驱动浏览器打开知乎的页面，然后获取知乎的LOGO元素，然后将它的class打印出来。

控制台输出结果：

```text
<selenium.webdriver.remote.webelement.WebElement (session="e08c0f28d7f44d75ccd50df6bb676104", element="0.7236390660048155-1")>
zu-top-link-logo
```

我们通过get_attribute()方法，然后传入想要获取的属性名，就可以得到它的值了。

### 获取文本值



每个WebEelement元素都有text属性，我们可以通过直接调用这个属性就可以得到元素内部的文本信息了，就相当于BeautifulSoup的get_text()方法、PyQuery的text()方法。

我们用一个实例来感受一下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
url = 'https://www.zhihu.com/explore'
browser.get(url)
input = browser.find_element_by_class_name('zu-top-add-question')
print(input.text)
```

在这里们依然是先打开知乎页面，然后获取提问按钮这个元素，再将其文本值打印出来。

控制台输出结果：

```text
提问
```

### 获取ID、位置、标签名、大小



另外WebElement元素还有一些其他的属性，比如id属性可以获取元素id，location可以获取该元素在页面中的相对位置，tag_name可以获取标签名称 ，size可以获取元素的大小，也就是宽高，这些属性有时候还是很有用的。

我们用实例来感受一下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
url = 'https://www.zhihu.com/explore'
browser.get(url)
input = browser.find_element_by_class_name('zu-top-add-question')
print(input.id)
print(input.location)
print(input.tag_name)
print(input.size)
```

在这里我们首先获得了提问按钮这个元素，然后调用其id、location、tag_name、size属性即可获取对应的属性值。

## 切换Frame



我们知道在网页中有这样一种标签叫做iframe，也就是子Frame，相当于页面的子页面，它的结构和外部网页的结构是完全一致的。Selenium打开页面后，它默认是在父级Frame里面操作，而此时如果页面中还有子Frame，它是不能获取到子Frame里面的元素的。所以这时候我们就需要使用switch_to.frame()方法来切换Frame。

我们首先用一个实例来感受一下：

```text
import time
from selenium import webdriver
from selenium.common.exceptions import NoSuchElementException

browser = webdriver.Chrome()
url = 'http://www.runoob.com/try/try.php?filename=jqueryui-api-droppable'
browser.get(url)
browser.switch_to.frame('iframeResult')
try:
    logo = browser.find_element_by_class_name('logo')
except NoSuchElementException:
    print('NO LOGO')
browser.switch_to.parent_frame()
logo = browser.find_element_by_class_name('logo')
print(logo)
print(logo.text)
```

控制台输出：

```text
NO LOGO
<selenium.webdriver.remote.webelement.WebElement (session="4bb8ac03ced4ecbdefef03ffdc0e4ccd", element="0.13792611320464965-2")>
RUNOOB.COM
```

我们还是以上文演示动作链操作的网页为实例，首先我们通过switch_to.frame()方法切换到子Frame里面，然后我们尝试获取父级Frame里的LOGO元素，是不能找到的，找不到的话就会抛出NoSuchElementException异常，异常被捕捉之后就会输出NO LOGO，接下来我们重新切换回父Frame，然后再次重新获取元素，发现就可以成功获取了。

所以，当页面中包含子Frame时，如果我们想获取子Frame中的元素，需要先调用switch_to.frame()方法切换到对应的Frame，然后再进行操作。

## 等待

### 延时等待



在Selenium中，get()方法会在网页框架加载结束之后就结束执行，此时如果获取page_source可能并不是浏览器完全加载完成的页面，如果某些页面有额外的Ajax请求，我们在网页源代码中也不一定能成功获取到。所以这里我们需要延时等待一定时间确保元素已经加载出来。

在这里等待的方式有两种，一种隐式等待，一种显式等待。

### 隐式等待



当使用了隐式等待执行测试的时候，如果Selenium没有在DOM中找到元素，将继续等待，超出设定时间后则抛出找不到元素的异常, 换句话说，当查找元素而元素并没有立即出现的时候，隐式等待将等待一段时间再查找 DOM，默认的时间是0。

我们用一个实例来感受一下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.implicitly_wait(10)
browser.get('https://www.zhihu.com/explore')
input = browser.find_element_by_class_name('zu-top-add-question')
print(input)
```

在这里我们用implicitly_wait()方法实现了隐式等待。

### 显式等待



隐式等待的效果其实并没有那么好，因为我们只是规定了一个固定时间，而页面的加载时间是受到网络条件影响的。

所以在这里还有一种更合适的显式等待方法，它指定好要查找的元素，然后指定一个最长等待时间。如果在规定时间内加载出来了这个元素，那就返回查找的元素，如果到了规定时间依然没有加载出该元素，则会抛出超时异常。

我们用一个实例来感受一下：

```text
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

browser = webdriver.Chrome()
browser.get('https://www.taobao.com/')
wait = WebDriverWait(browser, 10)
input = wait.until(EC.presence_of_element_located((By.ID, 'q')))
button = wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, '.btn-search')))
print(input, button)
```

在这里我们首先引入了WebDriverWait这个对象，指定好最长等待时间，然后调用它的until()方法，传入要等待条件expected_conditions，比如在这里我们传入了presence_of_element_located这个条件，就代表元素出现的意思，其参数是元素的定位元组，也就是ID为q的元素搜索框。

所以这样可以做到的效果就是，在10秒内如果ID为q的元素即搜索框成功加载出来了，那就返回该元素，如果超过10秒还没有加载出来，那就抛出异常。

对于按钮，可以更改一下等待条件，比如改为element_to_be_clickable，也就是可点击，所以查找按钮时是查找CSS选择器为.btn-search的按钮，如果10秒内它是可点击的也就是成功加载出来了，那就返回这个按钮元素，如果超过10秒还不可点击，也就是没有加载出来，那就抛出异常。

运行代码，在网速较佳的情况下是可以成功加载出来的。

控制台输出：

```text
<selenium.webdriver.remote.webelement.WebElement (session="07dd2fbc2d5b1ce40e82b9754aba8fa8", element="0.5642646294074107-1")>
<selenium.webdriver.remote.webelement.WebElement (session="07dd2fbc2d5b1ce40e82b9754aba8fa8", element="0.5642646294074107-2")>
```

可以看到控制台成功输出了两个元素，都是WebElement类型。

如果网络有问题，10秒内没有成功加载，那就抛出TimeoutException，控制台输出如下：

```text
TimeoutException Traceback (most recent call last)
<ipython-input-4-f3d73973b223> in <module>()
      7 browser.get('https://www.taobao.com/')
      8 wait = WebDriverWait(browser, 10)
----> 9 input = wait.until(EC.presence_of_element_located((By.ID, 'q')))
```

关于等待条件，其实还有很多，比如判断标题内容，判断某个元素内是否出现了某文字，在这里将所有的加载条件列举如下：

等待条件含义
title_is标题是某内容
title_contains标题包含某内容
presence_of_element_located元素加载出，传入定位元组，如(By.ID, 'p')
visibility_of_element_located元素可见，传入定位元组
visibility_of可见，传入元素对象
presence_of_all_elements_located所有元素加载出
text_to_be_present_in_element某个元素文本包含某文字
text_to_be_present_in_element_value某个元素值包含某文字
frame_to_be_available_and_switch_to_it frame加载并切换
invisibility_of_element_located元素不可见
element_to_be_clickable元素可点击
staleness_of判断一个元素是否仍在DOM，可判断页面是否已经刷新
element_to_be_selected元素可选择，传元素对象
element_located_to_be_selected元素可选择，传入定位元组
element_selection_state_to_be传入元素对象以及状态，相等返回True，否则返回False
element_located_selection_state_to_be传入定位元组以及状态，相等返回True，否则返回False
alert_is_present是否出现Alert

更多详细的等待条件的参数及用法介绍可以参考官方文档：[http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.support.expected_conditions](https://link.zhihu.com/?target=http%3A//selenium-python.readthedocs.io/api.html%23module-selenium.webdriver.support.expected_conditions)

## 前进后退



我们平常使用浏览器都有前进和后退功能，使用Selenium也可以完成这个操作，使用back()方法可以后退，forward()方法可以前进。

我们用一个实例来感受一下：

```text
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.baidu.com/')
browser.get('https://www.taobao.com/')
browser.get('https://www.python.org/')
browser.back()
time.sleep(1)
browser.forward()
browser.close()
```

在这里我们连续访问三个页面，然后调用back()方法就可以回到第二个页面，接下来再调用forward()方法又可以前进到第三个页面。

## Cookies



使用Selenium还可以方便地对Cookies进行操作，例如获取、添加、删除Cookies等等。

我们再用实例来感受一下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.zhihu.com/explore')
print(browser.get_cookies())
browser.add_cookie({'name': 'name', 'domain': 'www.zhihu.com', 'value': 'germey'})
print(browser.get_cookies())
browser.delete_all_cookies()
print(browser.get_cookies())
```

首先我们访问了知乎，然后加载完成之后，浏览器实际上已经生成了Cookies了，我们调用get_cookies()方法就可以获取所有的Cookies，然后我们添加一个Cookie，传入一个字典，有name、domain、value键名。接下来我们再次获取所有的Cookies，可以发现结果就多了这一项Cookie。最后我们调用delete_all_cookies()方法，删除所有的Cookies，再重新获取，结果就为空了。

控制台输出：

```text
[{'secure': False, 'value': '"NGM0ZTM5NDAwMWEyNDQwNDk5ODlkZWY3OTkxY2I0NDY=|1491604091|236e34290a6f407bfbb517888849ea509ac366d0"', 'domain': '.zhihu.com', 'path': '/', 'httpOnly': False, 'name': 'l_cap_id', 'expiry': 1494196091.403418}]
[{'secure': False, 'value': 'germey', 'domain': '.www.zhihu.com', 'path': '/', 'httpOnly': False, 'name': 'name'}, {'secure': False, 'value': '"NGM0ZTM5NDAwMWEyNDQwNDk5ODlkZWY3OTkxY2I0NDY=|1491604091|236e34290a6f407bfbb517888849ea509ac366d0"', 'domain': '.zhihu.com', 'path': '/', 'httpOnly': False, 'name': 'l_cap_id', 'expiry': 1494196091.403418}]
[]
```

因此通过以上方法来操作Cookies还是非常方便的。

## 选项卡管理



我们在访问网页的时候会开启一个个选项卡，那么在Selenium中也可以对选项卡进行操作。

```text
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.baidu.com')
browser.execute_script('window.open()')
print(browser.window_handles)
browser.switch_to_window(browser.window_handles[1])
browser.get('https://www.taobao.com')
time.sleep(1)
browser.switch_to_window(browser.window_handles[0])
browser.get('https://python.org')
```

控制台输出：

```text
['CDwindow-4f58e3a7-7167-4587-bedf-9cd8c867f435', 'CDwindow-6e05f076-6d77-453a-a36c-32baacc447df']
```

首先我们访问了百度，然后调用了execute_script()方法，传入window.open()方法新开启一个选项卡，然后接下来我们想切换到该选项卡，可以调用window_handles属性获取当前开启的所有选项卡，返回的是选项卡的代号列表，要想切换选项卡只需要调用switch_to_window()方法，传入选项卡的代号即可。在这里我们将第二个选项卡代号传入，即跳转到了第二个选项卡，然后接下来在第二个选项卡下打开一个新的页面，然后切换回第一个选项卡可以重新调用switch_to_window()方法，再执行其他操作即可。

如此以来我们便实现了选项卡的管理。

## 异常处理



在使用Selenium过程中，难免会遇到一些异常，例如超时、元素未找到等错误，一旦出现此类错误，程序便不会继续运行了，所以异常处理在程序中是十分重要的。

在这里我们可以使用try..except来捕获各种异常。

首先我们演示一下元素未找到的异常，示例如下：

```text
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.baidu.com')
browser.find_element_by_id('hello')
```

在这里我们打开百度页面，然后尝试选择一个并不存在的元素，这样就会遇到异常。

运行之后控制台输出如下：

```text
NoSuchElementException Traceback (most recent call last)
<ipython-input-23-978945848a1b> in <module>()
      3 browser = webdriver.Chrome()
      4 browser.get('https://www.baidu.com')
----> 5 browser.find_element_by_id('hello')
```

可以看到抛出了NoSuchElementException这类异常，这通常是元素未找到的异常，为了防止程序遇到异常而中断，我们需要捕获一下这些异常。

```text
from selenium import webdriver
from selenium.common.exceptions import TimeoutException, NoSuchElementException

browser = webdriver.Chrome()
try:
    browser.get('https://www.baidu.com')
except TimeoutException:
    print('Time Out')
try:
    browser.find_element_by_id('hello')
except NoSuchElementException:
    print('No Element')
finally:
    browser.close()
```

如上例锁上，这里我们使用try..except来捕获各类异常，比如我们对find_element_by_id查找元素的方法捕获NoSuchElementException，这样一旦出现这样的错误，就进行异常处理，程序也不会中断了。

控制台输出：

```text
No Element
```

更多的异常累可以参考官方文档：[http://selenium-python.readthedocs.io/api.html#module-selenium.common.exceptions](https://link.zhihu.com/?target=http%3A//selenium-python.readthedocs.io/api.html%23module-selenium.common.exceptions)，如果出现了某个异常，我们对它进行捕获即可。

## 准备工作

1. 安装`seleniumm`

```
pip install selenium
```

\2. 下载浏览器驱动

Firefox浏览器驱动：[geckodriver](https://link.zhihu.com/?target=https%3A//github.com/mozilla/geckodriver/releases)

Chrome浏览器驱动：[chromedriver](https://link.zhihu.com/?target=https%3A//sites.google.com/a/chromium.org/chromedriver/home) , [taobao备用地址](https://link.zhihu.com/?target=https%3A//npm.taobao.org/mirrors/chromedriver)

IE浏览器驱动：[IEDriverServer](https://link.zhihu.com/?target=http%3A//selenium-release.storage.googleapis.com/index.html)

Edge浏览器驱动：[MicrosoftWebDriver](https://link.zhihu.com/?target=https%3A//developer.microsoft.com/en-us/microsoft-edge/tools/webdriver)

Opera浏览器驱动：[operadriver](https://link.zhihu.com/?target=https%3A//github.com/operasoftware/operachromiumdriver/releases)

PhantomJS浏览器驱动：[phantomjs](https://link.zhihu.com/?target=http%3A//phantomjs.org/)

**需要把浏览器驱动放入系统路径中，或者直接告知selenuim的驱动路径**

可以测试是否正常使用，以下代码：

```text
用from selenium import webdriver


driver = webdriver.Firefox()   # Firefox浏览器
# driver = webdriver.Firefox("驱动路径")

driver = webdriver.Chrome()    # Chrome浏览器

driver = webdriver.Ie()        # Internet Explorer浏览器

driver = webdriver.Edge()      # Edge浏览器

driver = webdriver.Opera()     # Opera浏览器

driver = webdriver.PhantomJS()   # PhantomJS
```

## 元素定位

```text
find_element_by_id()
find_element_by_name()
find_element_by_class_name()
find_element_by_tag_name()
find_element_by_link_text()
find_element_by_partial_link_text()
find_element_by_xpath()
find_element_by_css_selector()
```

在`element`变成`elements`就是找所有满足的条件，返回数组。

## 控制浏览器操作

- 控制浏览器窗口大小

```
driver.set_window_size(480, 800)
```

- 浏览器后退，前进

```
# 后退 driver.back() # 前进 driver.forward()
```

- 刷新

```
driver.refresh() # 刷新
```

## Webelement常用方法

- 点击和输入

```
driver.find_element_by_id("kw").clear() # 清楚文本 driver.find_element_by_id("kw").send_keys("selenium") # 模拟按键输入 driver.find_element_by_id("su").click() # 单机元素
```

- 提交

可以在搜索框模拟回车操作

```
search_text = driver.find_element_by_id('kw') search_text.send_keys('selenium') search_text.submit()
```

- 其他

size： 返回元素的尺寸。

text： 获取元素的文本。

get_attribute(name)： 获得属性值。

is_displayed()： 设置该元素是否用户可见。

## 鼠标操作

在 WebDriver 中， 将这些关于鼠标操作的方法封装在 ActionChains 类提供。

ActionChains 类提供了鼠标操作的常用方法：

- perform()： 执行所有 ActionChains 中存储的行为；
- context_click()： 右击；
- double_click()： 双击；
- drag_and_drop()： 拖动；
- move_to_element()： 鼠标悬停。

举个例子：

```python
from selenium import webdriver
# 引入 ActionChains 类
from selenium.webdriver.common.action_chains import ActionChains

driver = webdriver.Chrome()
driver.get("https://www.baidu.cn")

# 定位到要悬停的元素
above = driver.find_element_by_link_text("设置")
# 对定位到的元素执行鼠标悬停操作
ActionChains(driver).move_to_element(above).perform()
```

## 键盘事件

以下为常用的键盘操作：

- send_keys(Keys.BACK_SPACE) 删除键（BackSpace）
- send_keys(Keys.SPACE) 空格键(Space)
- send_keys(Keys.TAB) 制表键(Tab)
- send_keys(Keys.ESCAPE) 回退键（Esc）
- send_keys(Keys.ENTER) 回车键（Enter）
- send_keys(Keys.CONTROL,'a') 全选（Ctrl+A）
- send_keys(Keys.CONTROL,'c') 复制（Ctrl+C）
- send_keys(Keys.CONTROL,'x') 剪切（Ctrl+X）
- send_keys(Keys.CONTROL,'v') 粘贴（Ctrl+V）
- send_keys(Keys.F1) 键盘 F1
- ……
- send_keys(Keys.F12) 键盘 F12

```text
# 输入框输入内容
driver.find_element_by_id("kw").send_keys("seleniumm")

# 删除多输入的一个 m
driver.find_element_by_id("kw").send_keys(Keys.BACK_SPACE)
```

## 获取断言信息

```text
title = driver.title # 打印当前页面title
now_url = driver.current_url # 打印当前页面URL
user = driver.find_element_by_class_name('nums').text # # 获取结果数目
```

## 等待页面加载完成

- 显示等待

显式等待使WebdDriver等待某个条件成立时继续执行，否则在达到最大时长时抛出超时异常（TimeoutException）。

```text
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Firefox()
driver.get("http://www.baidu.com")

element = WebDriverWait(driver, 5, 0.5).until(
                      EC.presence_of_element_located((By.ID, "kw"))
                      )
element.send_keys('selenium')
driver.quit()
```

WebDriverWait类是由WebDirver 提供的等待方法。在设置时间内，默认每隔一段时间检测一次当前页面元素是否存在，如果超过设置时间检测不到则抛出异常。具体格式如下：

```
WebDriverWait(driver, timeout, poll_frequency=0.5, ignored_exceptions=None)
```

- driver ：浏览器驱动。
- timeout ：最长超时时间，默认以秒为单位。
- poll_frequency ：检测的间隔（步长）时间，默认为0.5S。
- ignored_exceptions ：超时后的异常信息，默认情况下抛NoSuchElementException异常。
- WebDriverWait()一般由until()或until_not()方法配合使用，下面是until()和until_not()方法的说明。
- until(method, message=‘’) 调用该方法提供的驱动程序作为一个参数，直到返回值为True。
- until_not(method, message=‘’) 调用该方法提供的驱动程序作为一个参数，直到返回值为False。

在本例中，通过as关键字将expected_conditions 重命名为EC，并调用presence_of_element_located()方法判断元素是否存在。

- 隐式等待

如果某些元素不是立即可用的，隐式等待是告诉WebDriver去等待一定的时间后去查找元素。 默认等待时间是0秒，一旦设置该值，隐式等待是设置该WebDriver的实例的生命周期。

```text
from selenium import webdriver
driver = webdriver.Firefox()    
driver.implicitly_wait(10) # seconds    
driver.get("http://somedomain/url_that_delays_loading")    
myDynamicElement = driver.find_element_by_id("myDynamicElement") 
```

## 在不同的窗口和框架之间移动

```text
driver.switch_to_window("windowName")
driver.switch_to_frame("frameName")
```

以直接取表单的id 或name属性。如果iframe没有可用的id和name属性，则可以通过下面的方式进行定位。

```text
#先通过xpth定位到iframe
xf = driver.find_element_by_xpath('//*[@id="x-URS-iframe"]')

#再将定位对象传给switch_to_frame()方法
driver.switch_to_frame(xf)
```

一旦我们完成了frame中的工作，我们可以这样返回父frame:

```text
driver.switch_to_default_content()
```

## 警告框处理

```text
alert = driver.switch_to_alert()
```

- text：返回 alert/confirm/prompt 中的文字信息。
- accept()：接受现有警告框。
- dismiss()：解散现有警告框。
- send_keys(keysToSend)：发送文本至警告框。keysToSend：将文本发送至警告框。

## 下拉框选择

```text
from selenium import webdriver
from selenium.webdriver.support.select import Select
from time import sleep

driver = webdriver.Chrome()
driver.implicitly_wait(10)
driver.get('http://www.baidu.com')
sel = driver.find_element_by_xpath("//select[@id='nr']")
Select(sel).select_by_value('50')  # 显示50条
```

## 文件上传

```text
driver.find_element_by_name("file").send_keys('D:\\upload_file.txt')  # # 定位上传按钮，添加本地文件
```

## cookie操作

WebDriver操作cookie的方法：

- get_cookies()： 获得所有cookie信息。
- get_cookie(name)： 返回字典的key为“name”的cookie信息。
- add_cookie(cookie_dict) ： 添加cookie。“cookie_dict”指字典对象，必须有name 和value 值。
- delete_cookie(name,optionsString)：删除cookie信息。“name”是要删除的cookie的名称，“optionsString”是该cookie的选项，目前支持的选项包括“路径”，“域”。
- delete_all_cookies()： 删除所有cookie信息

## 调用JavaScript代码

```text
js="window.scrollTo(100,450);"
driver.execute_script(js) # 通过javascript设置浏览器窗口的滚动条位置
```

通过execute_script()方法执行JavaScripts代码来移动滚动条的位置。

## 窗口截图

```text
driver.get_screenshot_as_file("D:\\baidu_img.jpg") # 截取当前窗口，并指定截图图片的保存位置
```

## 关闭浏览器

- close() 关闭单个窗口
- quit() 关闭所有窗口
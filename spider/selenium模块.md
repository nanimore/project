- selenium中文文档https://python-selenium-zh.readthedocs.io/zh_CN/latest/

# 快速上手

- 示例1：

  ```python
  #爬取全历史一个页面的画作名字
  from selenium import webdriver
  from time import sleep
  from lxml import etree
  
  # 实例化对象  PS:需要先下载浏览器驱动http://chromedriver.storage.googleapis.com/index.html
  driver = webdriver.Chrome()    # Chrome浏览器
  # 发起请求，访问页面
  driver.get('https://www.allhistory.com/painting')
  # 等待页面某些元素加载 现在很多Web应用都在使用AJAX技术。浏览器载入一个页面时，页面内的元素可能是在不同的时间载入的
  sleep(1)
  # 定位到页面最下方‘知道了’按钮，并点击
  driver.find_element_by_class_name('cookie-banner-btn').click()
  # 获取页面源码
  page_text = driver.page_source
  
  # xpath定位
  tree = etree.HTML(page_text)
  imgList_li = tree.xpath('//*[@id="imglist"]/ul//li')
  imgList = []
  for i in imgList_li:
      title = i.xpath('./div[@class="imagetitle"]/text()')[0].strip()
      imgList.append({'imagetitle':title})
      print(title)
  # 等待3秒
  sleep(3)
  # 关闭浏览器驱动对象
  driver.quit() #关闭浏览器
  #driver.close() #关闭当前页面
  ```

- 示例2（模拟登陆）：

  ```python
  input_name = driver.find_element_by_xpath('') #定位到用户名输入框
  input_pass = driver.find_element_by_xpath('')
  button_submit = driver.find_element_by_xpath('')
  input_name.send_keys('username') #输入用户名
  input_pass.send_keys('password')
  button_submit.click() #点击提交
  ```

  

# 元素定位

```html
<html>
 <body>
  <form id="loginForm">
   <input name="username" type="text" />
   <input name="password" type="password" />
   <input name="continue" type="submit" value="Login" />
   <input name="continue" type="button" value="Clear" />
  </form>
   <p>Are you sure you want to do this?</p>
   <a href="continue.html">Continue</a>
   <a href="cancel.html">Cancel</a>
   <p class="content">Site content goes here.</p>
 </body>
<html>
```

- 根据Id定位
  `login_form = driver.find_element_by_id('loginForm')`

- 根据 Name 定位
  `username = driver.find_element_by_name('username')
  password = driver.find_element_by_name('password')`

- 用链接文本定位超链接
  `continue_link = driver.find_element_by_link_text('Continue') `
  `continue_link = driver.find_element_by_partial_link_text('Conti')`

- 标签名定位
  `heading1 = driver.find_element_by_tag_name('p')`

- class定位（定位p元素）
  `content = driver.find_element_by_class_name('content')`

- css选择器定位（定位p元素）
  `content = driver.find_element_by_css_selector('p.content')`

# 等待

- 显示等待（推荐使用显式）

  - 显式的waits等待一个确定的条件触发然后才进行更深一步的执行。最糟糕的的做法是time.sleep()，这指定的条件是等待一个指定的时间段。 这里提供一些便利的方法让你编写的代码只等待需要的时间，WebDriverWait结合ExpectedCondition是一种实现的方法：

    ```python
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC
    
    driver = webdriver.Firefox()
    driver.get("http://somedomain/url_that_delay_loading")
    try:
        element = WebDriverWait(driver,10).until(
            EC.presence_of_element_located((By.ID,"myDynamicElement"))
        )
    finally:
        driver.quit()
    ```

    这段代码会等待10秒，如果10秒内找到元素则立即返回，否则会抛出TimeoutException异常，WebDriverWait默认每500毫秒调用一下ExpectedCondition直到它返回成功为止。ExpectedCondition类型是布尔的，成功的返回值就是true,其他类型的ExpectedCondition成功的返回值就是 not null

- 隐式等待

  - 当我们要找一个或者一些不能立即可用的元素的时候，隐式`waits`会告诉WebDriver轮询DOM指定的次数，默认设置是0次。一旦设定，WebDriver对象实例的整个生命周期的隐式调用也就设定好了。

    ```python
    from selenium import webdriver
    
    driver = webdriver.Firefox()
    driver.implicitly_wait(10) # seconds
    driver.get("http://somedomain/url_that_delays_loading")
    myDynamicElement = driver.find_element_by_id('myDynamicElement')
    ```

# 行为链

- 详细说明：https://python-selenium-zh.readthedocs.io/zh_CN/latest/7.2%20%E8%A1%8C%E4%B8%BA%E9%93%BE/

- ActionChains可以完成简单的交互行为，例如鼠标移动，鼠标点击事件，键盘输入，以及内容菜单交互。这对于模拟那些复杂的类似于鼠标悬停和拖拽行为很有用

- ```python
  menu = driver.find_element_by_css_selector(".nav")
  hidden_submenu = driver.find_element_by_css_selector(".nav #submenu1")
  
  actions = ActionChains(driver)
  actions.move_to_element(menu)
  actions.click(hidden_submenu)
  action.perform() #按顺序执行
  ```

- > click(on_element=None)

  点击一个元素

  参数： * on_element:要点击的元素，如果是`None`，点击鼠标当前的位置

  > click_and_hold(on_element=None)

  鼠标左键点击一个元素并且保持

  参数： * on_element:同click()类似

  > double_click(on_element=None)

  双击一个元素

  参数： * on_element:同click()类似

  > drag_and_drop(source, target)

  鼠标左键点击`source`元素，然后移动到`target`元素释放鼠标按键

  参数： *source:鼠标点击的元素* target:鼠标松开的元素

  > drag_and_drop_by_offset(source, xoffset,yoffset)

  拖拽目标元素到指定的偏移点释放

  参数: *source:点击的参数* xoffset:X偏移量 * yoffset:Y偏移量

  > key_down(value,element=None)

  只按下键盘，不释放。我们应该只对那些功能键使用(Contril,Alt,Shift)

  参数： *value：要发送的键，值在`Keys`类里有定义* element:发送的目标元素，如果是`None`，value会发到当前聚焦的元素上

  例如，我们要按下 ctrl+c:

  ```
  ActionChains(driver).key_down(Keys.CONTROL).send_keys('c').key_up(Keys.CONTROL).perform()
  ```

  > key_up(value,element=None)

  释放键。参考key_down的解释

  > move_by_offset(xoffset,yoffset)

  将当前鼠标的位置进行移动

  参数： *xoffset:要移动的X偏移量，可以是正也可以是负* yoffset:要移动的Y偏移量，可以是正也可以是负

  > move_to_element(to_element)

  把鼠标移到一个元素的中间

  参数： * to_element:目标元素

  > move_to_element_with_offset(to_element,xoffset,yoffset)

  鼠标移动到元素的指定位置，偏移量以元素的左上角为基准

  参数： *to_element:目标元素* xoffset:要移动的X偏移量 * yoffset:要移动的Y偏移量

  > perform()

  执行所有存储的动作

  > release(on_element=None)

  释放一个元素上的鼠标按键，

  参数： * on_element:如果为`None`,在当前鼠标位置上释放

  > send_keys(*keys_to_send)

  向当前的焦点元素发送键

  参数: * keys_to_send:要发送的键，修饰键可以到`Keys`类里找到

  > send_keys_to_element(element,*keys_to_send)

  向指定的元素发送键

# 切换窗口

- 循环切换每个窗口

  ```python
  for handle in driver.window_handles:
      driver.switch_to.window(handle)
  ```

- 切换到某一个窗口
  `driver.switch_to.frame(driver.window_handles[0])`

- 框架和框架之间切换
  `driver.switch_to_frame("frameName")`
  `driver.switch_to_frame("frameName.0.child")`

- 弹出对话框
  `alert = driver.switch_to_alert()`

- 在浏览器的历史记录的后退或者前进
  `driver.forward() `

  `driver.back()`

- cookies

  ```python
  # Go to the correct domain
  driver.get("http://example.com")
  
  # Now set the cookie. This one's valid for the entire domain
  cookie = {'name':'foo','value':'bar'}
  driver.add_cookie(cookie)
  
  # And now output all the available cookies for the current URL
  driver.get_cookies()
  ```

  
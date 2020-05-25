# 中文README
***
[点击这里](https://github.com/g1879/DrissionPage/blob/master/README.zh-cn.md)

# Introduction
***

DrissionPage, the combination of driver and session, is a Python-based Web automation operation integration tool.  
It integrates selenium and requests_html to achieve seamless switching between them.  
Therefore, the convenience of selenium and the high efficiency of requests can be considered.  
It encapsulates commonly used methods of page elements and is very suitable for the expansion of the PO mode of automated operation.  
Even better, it is very concise and user-friendly, with little code and friendly to novices.  

# Background
***

When a novice learns a web crawler, in the face of a website that needs to log in, it is necessary to analyze data packets, JS source code, construct complex requests, and often have to deal with verification codes, JS confusion, signature parameters and other measures, which is difficult to learn. When acquiring data, some data is generated by JavaScript calculation. If you only get the source data, you must also reproduce the calculation process. The experience is not good and the development efficiency is not high.

Using selenium can avoid these problems to a great extent, but selenium is not efficient. Therefore, what this library has to do is to combine selenium and requests into one, and provide a humanized use method to improve development and operation efficiency.

In addition to merging the two, this library also encapsulates commonly used functions in units of web pages, which simplifies selenium operations and statements. When used in web page automation operations, it reduces the consideration of details, focuses on function implementation, and is more convenient to use.

The design concept of this library is to keep everything simple, try to provide a simple and direct method of use, and is more friendly to novices.

**Project address：**

- https://github.com/g1879/DrissionPage
- https://gitee.com/g1879/DrissionPage

**email：** g1879@qq.com

# Features
***

-Allows seamless switching between selenium and requests, sharing session.
-The two modes provide a unified operation method with consistent user experience.
-Common methods are encapsulated in units of pages to facilitate PO mode expansion.
-Humanized operation method of page elements to reduce the workload of page analysis and coding.
-Save configuration information to file for easy recall.
-Some common functions (such as click) have been optimized to better meet the actual needs.

# Simple Demo

***

Example: Log in to the website with selenium, then switch to requests to read the web page, and print the element attributes.

```python
from DrissionPage import *
from time import sleep

drission = Drission()  # Create Drive Object
page = MixPage(drission)  # Create page object, default driver mode
page.get('https://gitee.com/profile')  # Visit the personal center page (not logged in, redirect to the login page)

# Use selenium to enter the account password to log in
page.ele('@id:user_login').input('your_user_name')
page.ele('@id:user_password').input('your_password\n')
sleep(1)  # Wait for login

page.change_mode()  # Switch to session mode

print('Title after login:', page.title, '\n')  # Output of session mode after login

# Get and print attributes
foot = page.ele('@id:footer-left')  # Find elements by id
first_col = foot.ele('css:>div')  # Find first div element in the lower level of foot element by css selector.
lnk = first_col.ele('text:Git Branching')  # Find elements by text content
text = lnk.text  # Get element text
href = lnk.attr('href')  # Get element attribute value
 
print(first_col)
print(text, href)
```

Output:

```
Title after login: Dashboard - Gitee

<SessionElement div class='column'>
Learn Git Branching https://oschina.gitee.io/learn-git-branching/
```

# Installation

***

```
pip install DrissionPage
```
Only python3.6 and above are supported. Driver mode currently only supports chrome.  
To use the driver mode, you must download chrome and ** corresponding version ** of chromedriver. [chromedriver download](https://chromedriver.chromium.org/downloads)  
Currently only tested in the Windows environment.

# Instructions

***

## import

```python
from DrissionPage import *
```



## Initialization

Before using selenium, you must configure the path of chrome.exe and chromedriver.exe and ensure that their versions match.

If you only use session mode, you can skip this section.

There are three ways to configure the path:

-Write two paths to system variables.
-Pass in the path manually when using it.
-Write the path to the ini file of this library (recommended).

If you choose the third method, please run these lines of code before using the library for the first time, and record these two paths in the ini file.

```python
from DrissionPage.easy_set import set_paths
driver_path = 'C:\\chrome\\chromedriver.exe'  # Your chromedriver.exe path, optional
chrome_path = 'D:\\chrome\\chrome.exe'  # Your chrome.exe path, optional
set_paths(driver_path, chrome_path)
```

This method also checks if the chrome and chromedriver versions match, and displays:

```
版本匹配，可正常使用。

or

版本不兼容。
请下载与当前chrome版本匹配的chromedriver。
当前chromedriver版本：<你的chromedriver版本号>
查看chrome版本方法：帮助 -> 关于Google Chrome
chromedriver下载网址：https://chromedriver.chromium.org/downloads
```

After the inspection is passed, the driver mode can be used normally.

In addition to the above two paths, this method can also set the following paths:

```python
debugger_address  # Opened browser address, eg. 127.0.0.1:9222
download_path  # Download path
global_tmp_path  # Temporary folder path
```

Tips：

-Different projects may require different versions of chrome and chromedriver. You can also save multiple ini files to use as needed.
-It is recommended to use the green version of chrome, and manually set the path to avoid browser upgrades that do not match the chromedriver version.
-It is recommended to set debugger_address when debugging a project, and use a manually opened browser to debug, saving time and effort.



## Create Drission Object 

Drission objects are used to manage driver and session objects. It can be created by directly reading the configuration information of the ini file, or it can be passed in during initialization.

```python
# Created by default ini file
drission = Drission()  

# Created by other ini files
drission = Drission(ini_path = 'D:\\settings.ini') 
```

To manually pass in the configuration:

```python
# Create with incoming configuration information (ignore ini file)
from DrissionPage.config import DriverOptions

driver_options = DriverOptions()  # Create driver configuration object
driver_options.binary_location = 'D:\\chrome\\chrome.exe'  # chrome.exe path
session_options = {'headers': {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6)'}}
driver_path = 'C:\\chrome\\chromedriver.exe'  # driver_path path

drission = Drission(driver_options, session_options, driver_path)  # Create object through incoming configuration
```



## Use MixPage objects

The MixPage page object encapsulates commonly used web page operations and implements the switch between driver and session mode.

```python
page = MixPage(drission)  # Default driver mode
page = MixPage(drission, mode='s', timeout=10)  # Session mode, element waiting time 5 seconds (default 10 seconds)

# Visit URL
page.get(url, **kwargs)
page.post(url, data, **kwargs)  # Only session mode has post method.Call the post method will automatically switch to session mode.

# Switch mode
page.change_mode()

# Page operation
print(page.html)  # Page source code
page.run_script(js)  # Run js statement
page.close_other_tabs(num)  # Close other tabs
page.to_iframe(iframe)  # switch to iframe
page.screenshot(path)  # Screenshot of the page
page.scrool_to_see(element)  # Scroll until an element is visible
# See APIs for details...
```

Tips：Calling a method that belongs only to the driver mode will automatically switch to the driver mode.



## Find elements

There are many ways to find page elements (eles function will return a list of all the element objects that meet the requirements).  

Note: The element search timeout is 10 seconds by default, you can also set it as required.

```python
# Find by attribute
page.ele('@id:ele_id', timeout = 2)  # Find the element with id ele_id and set the waiting time to 2 seconds
page.eles('@class:class_name')  # Find all elements with class class_name   

# Search by tag name
page.ele('tag:li')  # Find the first li element  
page.eles('tag:li')  # Find all li elements  

# Find by location
page.ele('@id:ele_id').parent  # Parent element  
page.ele('@id:ele_id').next  # Next sibling element  
page.ele('@id:ele_id').prev  # Previous brother element  

# Find by text
page.ele('search text')  # Find elements containing incoming text  
page.eles('text:search text')  # If the text starts with @, tag :, css :, xpath :, text :, add text: in front to avoid conflicts.  

# Find by xpath or css selector
page.eles('xpath://div[@class="ele_class"]')  
page.eles('css:div.ele_class')  

# Find by loc
loc1 = By.ID, 'ele_id'
loc2 = By.XPATH, '//div[@class="ele_class"]'
page.ele(loc1)
page.ele(loc2)

# Find subordinate elements
element = page.ele('@id:ele_id')
element.ele('@class:class_name')  # Find the first element whose class is ele_class at the lower level of element
element.eles('tag:li')  # Find all li elements below ele_id

# Tandem search
page.ele('@id:ele_id').ele('tag:div').next.ele('some text').eles('tag:a')
```



## Element operations

```python
# Get element information
element = page.ele('@id:ele_id')
element.html  # Return html inside element
element.text  # Returns the text value after removing the html tag in the element
element.tag  # Return element tag name
element.attrs  # Returns a dictionary of all attributes of the element
element.attr('class')  # Returns the element's class attribute
element.is_valid  # Driver mode only, used to determine whether the element is still available

# Operating element
element.click()  # Click element
element.input(text)  # Enter text
element.run_script(js)  # Run js
element.submit()  # submit Form
element.clear()  # Clear element
element.is_selected()  # Is selected
element.is_enabled()  # it's usable or not
element.is_displayed()  # Is it visible
element.is_valid()  # Whether it is valid, used to judge the situation where the page jump causes the element to fail
element.select(text)  # Select the drop-down list option
element.set_attr(attr,value)  # Set element attributes
element.size  # Returns the element size
element.location  # Returns the element position
```



## Save configuration

Because chrome and headers have many configurations, an ini file is set up to save commonly used configurations. You can use the OptionsManager object to get and save the configuration, and use the DriverOptions object to modify the chrome configuration. You can also save multiple ini files and call them according to different projects.

Tips：It is recommended to save common configuration files to another path to prevent the configuration from being reset when the library is upgraded.

### ini file

The ini file has three parts by default: paths, chrome_options, and session_options. The initial contents are as follows.

```ini
[paths]
; chromedriver.exe path
chromedriver_path =
; Temporary folder path, used to save screenshots, download files, etc.
global_tmp_path =

[chrome_options]
; The opened browser address and port, such as 127.0.0.1:9222
debugger_address =
; chrome.exe path
binary_location =
; Configuration information
arguments = [
            ; Hide browser window
            '--headless',
            ; Mute
            '--mute-audio',
            ; No sandbox
            '--no-sandbox',
            ; Google documentation mentions the need to add this attribute to avoid bugs
            '--disable-gpu'
            ]
; Plugin
extensions = []
; Experimental configuration
experimental_options = {
                       'prefs': {
                       ; Download without pop-up
                       'profile.default_content_settings.popups': 0,
                       ; No pop-up window
                       'profile.default_content_setting_values': {'notifications': 2},
                       ; Disable PDF plugin
                       'plugins.plugins_list': [{"enabled": False, "name": "Chrome PDF Viewer"}],
                       ; Set to developer mode, anti-anti-reptile (useless)
                       'excludeSwitches': ["ignore-certificate-errors", "enable-automation"],
                       'useAutomationExtension': False
                       }
                       }

[session_options]
headers = {
          "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/603.3.8 (KHTML, like Gecko) Version/10.1.2 Safari/603.3.8",
          "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
          "Connection": "keep-alive",
          "Accept-Charset": "utf-8;q=0.7,*;q=0.7"
          }
```

### Usage example

```python
from DrissionPage import *
from DrissionPage.configs import *

driver_options = DriverOptions()  # Read configuration from default ini file
driver_options = DriverOptions('D:\\settings.ini')  # Read configuration from incoming ini file
driver_options.add_argument('--headless')  # Add configuration
driver_options.remove_experimental_options('prefs')  # Remove configuration
driver_options.save()  # Save configuration
driver_options.save('D:\\settings.ini')  # Save to other path

options_manager = OptionsManager()  # Create OptionsManager object
driver_path = options_manager.get_value('paths', 'chromedriver_path')  # Read path information
drission = Drission(driver_options, driver_path)  # Create a Drission object using configuration

drission = Drission(ini_path = 'D:\\settings.ini')  # Use other ini files to create objects
```

### OptionsManager object

The OptionsManager object is used to read, set, and save configurations.

```python
get_value(section, item) -> str  # Get the value of a configuration
get_option(section) -> dict  # Return all configuration properties in dictionary format
set_item(section, item, value)  # Set configuration properties
save()  # Save configuration to default ini file
save('D:\\settings.ini')  # Save to other path
```

** Note **: If you do not pass in the path when saving, it will be saved to the ini file in the module directory, even if you are not reading the default ini file.

### DriverOptions object

The DriverOptions object inherits from the Options object of selenium.webdriver.chrome.options, and adds the following methods to it:

```python
remove_argument(value)  # Delete an argument value
remove_experimental_option(key)  # Delete a experimental_option setting
remove_all_extensions()  # Remove all plugins
save()  # Save configuration to ini file
save('D:\\settings.ini')  # Save to other path
```


# PO mode

***

MixPage encapsulates common page operations and can be easily used for expansion.  

Example: Expand a list page reading class.

```python
import re
from time import sleep
from DrissionPage import *

class ListPage(MixPage):
    """This class encapsulates the method of reading the list page, according to the necessary 4 elements, can read the homogeneous list page"""
    def __init__(self, drission: Drission, url: str = None, **xpaths):
        super().__init__(drission)
        self._url = url
        self.xpath_cloumn_name = xpaths['cloumn_name']  # [xpath str, re str]
        self.xpath_next_btn = xpaths['next_btn']
        self.xpath_rows = xpaths['rows']
        self.xpath_total_pages = xpaths['total_pages']  # [xpath str, re str]
        self.total_pages = self.get_total_pages()
        if url:
            self.get(url)

    def get_cloumn_name(self) -> str:
        if self.xpath_cloumn_name[1]:
            s = self.ele(f'xpath:{self.xpath_cloumn_name[0]}').text
            r = re.search(self.xpath_cloumn_name[1], s)
            return r.group(1)
        else:
            return self.ele(f'xpath:{self.xpath_cloumn_name[0]}').text

    def get_total_pages(self) -> int:
        if self.xpath_total_pages[1]:
            s = self.ele(f'xpath:{self.xpath_total_pages[0]}').text
            r = re.search(self.xpath_total_pages[1], s)
            return int(r.group(1))
        else:
            return int(self.ele(f'xpath:{self.xpath_total_pages[0]}').text)

    def click_next_btn(self, wait: float = None):
        self.ele(f'xpath:{self.xpath_next_btn}').click()
        if wait:
            sleep(wait)

    def get_current_page_list(self, content_to_fetch: list) -> list:
        """
        content_to_fetch：[[xpath1,para1],[xpath2,para2]...]
        output list：[[para1,para2...],[para1,para2...]...]
        """
        result_list = []
        rows = self.eles(f'xpath:{self.xpath_rows}')
        for row in rows:
            row_result = []
            for j in content_to_fetch:
                row_result.append(row.ele(f'xpath:{j[0]}').attr(j[1]))
            result_list.append(row_result)
            print(row_result)
        return result_list

    def get_list(self, content_to_fetch: list, wait: float = None) -> list:
        current_list = self.get_current_page_list(content_to_fetch)
        for _ in range(self.total_pages - 1):
            self.click_next_btn(wait)
            current_list.extend(self.get_current_page_list(content_to_fetch))
        return current_list
```

# Others

***

## DriverPage and SessionPage

If there is no need to switch modes, only DriverPage or SessionPage can be used as required, the usage is consistent with MixPage.  

```python
from DrissionPage.session_page import SessionPage
from DrissionPage.drission import Drission

session = Drission().session
page = SessionPage(session)  # Pass in Session object
page.get('http://www.baidu.com')
print(page.ele('@id:su').text)  # Output：百度一下

driver = Drission().driver
page = DriverPage(driver)  # Pass in Driver object
page.get('http://www.baidu.com')
print(page.ele('@id:su').text)  # Output:百度一下
```

# APIs

***

## Drission class

​	class **Drission**(driver_options: Union[dict, Options] = None, session_options: dict = None, driver_path: str = None, ini_path = None)

​	Used to manage driver and session objects.

​	Parameter Description:

- driver_options - Chrome configuration parameters, can receive Options object or dictionary
- session_options - session configuration parameters, receive dictionary
- driver_path - chromedriver.exe path, if not set, system variables must be set in the system
- ini_path - ini file path, the default is the ini file in the DrissionPage folder

### session

​	Returns the HTMLSession object, which is created automatically when called.

### driver

​	Obtain the WebDriver object, which is automatically created when it is called and initialized according to the incoming configuration or ini file configuration.

### driver_options

​	Return driver configuration in dictionary format.

### session_options

​	Return session configuration in dictionary format.

### cookies_to_session

​	cookies_to_session(copy_user_agent: bool = False, driver: WebDriver = None, session: Session = None) -> None

​	Copy cookies from driver to session. By default, self.driver is copied to self.session, and driver and session can also be received for operation.

​	Parameter Description:

- copy_user_agent - Whether to copy user_agent to session
- driver - WebDriver object, copy cookies
- session - Session object, receiving cookies

### cookies_to_driver

​	cookies_to_driver(url: str, driver: WebDriver = None, session: Session = None) -> None

​	Copy cookies from session to driver. By default, self.session is copied to self.driver, and driver and session can also be received for operation. Need to specify url or domain name.

​	Parameter Description:

- url - cookies domain
- driver - WebDriver object, receiving cookies
- session - Session object, copy cookies

### user_agent_to_session

​	user_agent_to_session(driver: WebDriver = None, session: Session = None) -> None

​	Copy the user agent from the driver to the session. By default, self.driver is copied to self.session, and driver and session can also be received for operation.

​	Parameter Description:

- driver - WebDriver object, copy user agent
- session - Session object, receiving user agent

### close_driver

​	close_driver() -> None

​	Close the browser and set the driver to None.

### close_session

​	close_session() -> None

​	Close the session and set it to None.

### close

​	close() -> None

​	Close the driver and session.



## MixPage class

class **MixPage**(drission: Drission, mode='d', timeout: float = 10)

MixPage encapsulates common functions for page operations and can seamlessly switch between driver and session modes. Cookies are automatically synchronized when switching.  
The function of obtaining information is common to the two modes, and the function of operating page elements is only available in the d mode. Calling a function unique to a certain mode will automatically switch to that mode.  
It inherits from DriverPage and SessionPage classes. These functions are implemented by these two classes. MixPage exists as a scheduling role.

Parameter Description:

- drission - Drission object
- mode - Mode, optional 'd' or 's', default is 'd'
- timeout - Search element time-out time (can also be set separately each time element search)

### url  

​	Returns the currently visited URL.

### mode

​	Returns the current mode ('s' or 'd').

### drission

​	Returns the currently used Dirssion object.

### driver

​	Returns the driver object, if not created, it will switch to driver mode when called.

### session

​	Returns the session object, if not created.

### response

​	Return the Response object and switch to session mode when calling.

### cookies

​	Returns cookies, obtained from the current mode.

### html

​	Return the page html text.

### title

​	Return to the page title text.

### change_mode

​	change_mode(mode: str = None, go: bool = True) -> None

​	Switch mode, you can specify the target mode, if the target mode is consistent with the current mode, then directly return.

​	Parameter Description:

- mode - Specify the target mode, 'd' or 's'.
- go - Whether to jump to the current url after switching modes

### get

​	get(url: str, params: dict = None, go_anyway=False, **kwargs) -> Union[bool, None]

​	Jump to a url, sync cookies before jumping, and return whether the target url is available after jumping.

​	Parameter Description:

- url - Target url
- params - url parameters
- go_anyway - Whether to force a jump. If the target url is the same as the current url, the default is not to jump.
- kwargs - Used to access parameters when in session mode.

### ele

​	ele(loc_or_ele: Union[tuple, str, DriverElement, SessionElement], mode: str = None, timeout: float = None, show_errmsg: bool = False) -> Union[DriverElement, SessionElement]

​	Get elements according to query parameters and return elements or element lists.  
​	If the query parameter is a string, you can select the '@property name:', 'tag:', 'text:', 'css:', 'xpath:' method. When there is no control mode, it is searched by text mode by default.  
​	If it is loc, query directly according to the content.

​	Parameter Description:

- loc_or_str - Query condition parameters, if an element object is passed in, return directly
- mode - Find one or more, pass in 'single' or 'all'
- timeout - Search element timeout time, valid in driver mode
- show_errmsg - Whether to throw and display when an exception occurs

​	Examples:

- page.ele('@id:ele_id') - Find elements by attributes
- page.ele('tag:div') - Find elements by tag name
- page.ele('text:some text') - Find elements by text
- page.ele('some text') - Find elements by text
- page.ele('css:>div') - Find elements by css selector
- page.ele('xpath://div') - Find elements by xpath
- page.ele((By.ID, 'ele_id')) - Find elements by loc

### eles

​	eles(loc_or_str: Union[tuple, str], timeout: float = None, show_errmsg: bool = False) -> List[DriverElement]

​	Obtain a list of elements that meet the criteria based on query parameters. The query parameter usage method is the same as the ele method.

​	Parameter Description:

- loc_or_str - Query condition parameters
- timeout - Search element timeout time, valid in driver mode
- show_errmsg - Whether to throw and display when an exception occurs

### cookies_to_session

​	cookies_to_session(copy_user_agent: bool = False) -> None

​	Manually copy cookies from driver to session.

​	Parameter Description:

- copy_user_agent - Whether to also copy user agent

### cookies_to_driver

​	cookies_to_driver(url=None) -> None

​	Manually copy cookies from session to driver.

​	Parameter Description:

- url - cookie domain or url

### post

​	post(url: str, params: dict = None, data: dict = None, go_anyway: bool = False, **kwargs) -> Union[bool, None]

​	Jump by post, and switch to session mode automatically when calling.

​	Parameter Description:

- url - Target url
- parame - url parameter
- data - Submitted data
- go_anyway - Whether to force a jump. If the target url is the same as the current url, the default is not to jump.
- kwargs - Access parameters such as headers

### download

​	download(file_url: str, goal_path: str = None, rename: str = None, **kwargs) -> tuple

​	Download a file, return success and download information string. Changing the method will automatically avoid renaming the existing file in the target path.

​	Parameter Description:

- file_url - File URL
- goal_path - Storage path, the default is the temporary folder specified in the ini file
- rename - Rename the file name, not renamed by default
- kwargs - Connection parameters for requests



The following methods only take effect in driver mode, and will automatically switch to driver mode when called

***

### check_page

​	check_page() -> bool

​	After the subclass is derived, it is used to check whether the domain name meets expectations, and the function is implemented by the subclass.

### run_script

​	run_script(script: str) -> Any

​	Execute JavaScript code.

​	Parameter Description:

- script - JavaScript code text

### get_tabs_sum

​	get_tabs_sum() -> int

​	Returns the number of browser tabs.

### get_tab_num

​	get_tab_num() -> int

​	Returns the serial number of the current tab.

### to_tab

​	to_tab(index: int = 0) -> None

​	Jump to a tab page with a serial number.

Parameter Description:

- index - The serial number of the target tab, start from 0

### close_current_tab

​	close_current_tab() -> None

​	Close the current tab.

### close_other_tabs

​	close_other_tabs(tab_index: int = None) -> None

​	Close the tab page except the serial number.

​	Parameter Description:

- index - The serial number of the reserved tab, start from 0

### to_iframe

​	to_iframe(loc_or_ele: Union[str, tuple, WebElement] = 'main') -> bool

​	Jump to iframe, default jump to the highest level.

​	Parameter Description:

- loc_or_ele - The search condition of the iframe element is the same as the search condition of the ele () method.

​	Examples:
- to_iframe('@id:iframe_id')
- to_iframe(iframe_element)

### scroll_to_see

​	scroll_to_see(loc_or_ele: Union[WebElement, tuple]) -> None

​	Scroll until the element is visible.

​	Parameter Description:

- loc_or_ele - The search condition of the iframe element is the same as the search condition of the ele () method.

### scroll_to

​	scroll_to(mode: str = 'bottom', pixel: int = 300) -> None

​	Scroll the page and decide how to scroll according to the parameters.

​	Parameter Description:

- mode - Scrolling direction, top, bottom, rightmost, leftmost, up, down, left, right
- pixel - Scrolling pixels

### refresh

​	refresh() -> None

​	Refresh page.

### back

​	back() -> None

​	The page back.

### set_window_size

​	set_window_size(x: int = None, y: int = None) -> None

​	Set the window size and maximize it by default.

​	Parameter Description:

- x - Target width
- y - Target height

### screenshot

​	screenshot(path: str = None, filename: str = None) -> str

​	Take a screenshot of the web page and return the path of the screenshot file.

​	Parameter Description:

- path - Screenshot save path, default is the temporary folder specified in the ini file
- filename - Screenshot file name, default is page title as file name

### chrome_downloading

​	chrome_downloading(download_path: str = None) -> list

​	Check whether the browser is downloaded.

​	Parameter Description:

- download_path - Download path, the default is the download path in chrome options configuration

### close_driver

​	close_driver() -> None

​	Close the driver and browser, and switch to s mode.

### close_session

​	close_session() -> None

​	Close the session and switch to d mode.

## DriverElement class

class DriverElement(ele: WebElement, timeout: float = 10)

The element object of the driver mode wraps a WebElement object and encapsulates common functions.

Parameter Description:

- ele - WebElement object
- timeout - Search element time-out time (can also be set separately each time element search)

### inner_ele

​	The wrapped WebElement object.

### attrs

​	Return all attributes and values of the elements in a dictionary.

### text

​	Returns the text inside the element.

### html

​	Returns the html text in the element.

### tag

​	Returns the element label name text.

### parent

​	Returns the parent element object.

### next

​	Returns the next sibling element object.

### prev

​	Returns the last sibling element object.

### size

​	Returns the element size as a dictionary.

### location

​	Put the element coordinates back in a dictionary.

### ele

​	ele(loc_or_str: Union[tuple, str], mode: str = None, show_errmsg: bool = False, timeout: float = None) -> Union[DriverElement, List[DriverElement], None]

​	Get elements based on query parameters.  
​	If the query parameter is a string, you can select the '@property name:', 'tag:', 'text:', 'css:', and 'xpath:' methods. When there is no control mode, it is searched by text mode by default.  
​	If it is loc, query directly according to the content.

​	Parameter Description:

- loc_or_str - Query condition parameters
- mode - Find one or more, pass in 'single' or 'all'
- show_errmsg - Whether to throw and display when an exception occurs
- timeout - Find Element Timeout

​	Examples:：

- element.ele('@id:ele_id') - Find elements by attributes
- element.ele('tag:div') - Find elements by tag name
- element.ele('text:some text') - Find elements by text
- element.ele('some text') - Find elements by text
- element.ele('css:>div') - Find elements by css selector
- element.ele('xpath://div') - Find elements by xpath
- element.ele((By.ID, 'ele_id')) - Find elements by loc

### eles

​	eles(loc_or_str: Union[tuple, str], show_errmsg: bool = False, timeout: float = None) ->  List[DriverElement]

​	Obtain a list of elements that meet the criteria based on query parameters. The query parameter usage method is the same as the ele method.

​	Parameter Description:

- loc_or_str - Query condition parameters
- show_errmsg - Whether to throw and display when an exception occurs
- timeout - Find Element Timeout

### attr

​	attr(attr: str) -> str

​	Get the value of an attribute of an element.

​	Parameter Description:

- attr - Attribute name

### click

​	click(by_js=False) -> bool

​	Click on the element. If it is unsuccessful, click on js. You can specify click on js.

​	Parameter Description:

- by_js - Whether to click with js

### input

​	input(value, clear: bool = True) -> bool

​	Enter text.

​	Parameter Description:

- value - Text value
- clear - Whether to clear the text box before entering

### run_script

​	run_script(script: str) -> Any

​	Run JavaScript on the element.

​	Parameter Description:

- script - JavaScript text

### submit

​	submit() -> None

​	Submit form.

### clear

​	clear() -> None

​	Clear the text box.

### is_selected

​	is_selected() -> bool

​	Whether the element is selected.

### is_enabled

​	is_enabled() -> bool

​	Whether the element is available on the page.

### is_displayed

​	is_displayed() -> bool

​	Whether the element is visible.

### is_valid

​	is_valid() -> bool

​	Whether the element is valid. This method is used to determine the situation where the page jump element cannot be used

### screenshot

​	screenshot(path: str = None, filename: str = None) -> str

​	Take a screenshot of the web page and return the path of the screenshot file.

​	Parameter Description:

- path - Screenshot save path, default is the temporary folder specified in the ini file
- filename - Screenshot file name, default is page title as file name

### select

​	select(text: str) -> bool

​	Choose from the drop-down list.

​	Parameter Description:

- text - Option text

### set_attr

​	set_attr(attr: str, value: str) -> bool

​	Set element attributes.

​	Parameter Description:

- attr - parameter name
- value - Parameter value



## SessionElement class

class SessionElement(ele: Element)

The element object of session mode wraps an Element object and encapsulates common functions.

Parameter Description:

- ele - Element object of requests_html library

### inner_ele

​	The wrapped Element object.

### attrs

​	Returns the names and values of all attributes of the element in dictionary format.

### text

​	Returns the text inside the element.

### html

​	Returns the html text in the element.

### tag

​	Returns the element label name text.

### parent

​	Returns the parent element object.

### next

​	Returns the next sibling element object.

### prev 

​	Returns the last sibling element object.

### ele

​	ele(loc_or_str: Union[tuple, str], mode: str = None, show_errmsg: bool = False) -> Union[SessionElement, List[SessionElement], None]

​	Get elements based on query parameters.  
​	If the query parameter is a string, you can select the '@property name:', 'tag:', 'text:', 'css:', and 'xpath:' methods. When there is no control mode, it is searched by text mode by default.  
​	If it is loc, query directly according to the content.

​	Parameter Description:

- loc_or_str - Query condition parameters

- mode - Find one or more, pass in 'single' or 'all'

- show_errmsg - Whether to throw and display when an exception occurs

​	Examples:

- element.ele('@id:ele_id') - Find elements by attributes
- element.ele('tag:div') - Find elements by tag name
- element.ele('text:some text') - Find elements by text
- element.ele('some text') - Find elements by text
- element.ele('css:>div') - Find elements by css selector
- element.ele('xpath://div') - Find elements according to xpath
- element.ele((By.ID, 'ele_id')) - Find elements according to loc

### eles

​	eles(loc_or_str: Union[tuple, str], show_errmsg: bool = False) ->  List[SessionElement]

​	Obtain a list of elements that meet the criteria based on query parameters. The query parameter usage method is the same as the ele method.

​	Parameter Description:

- loc_or_str - Query condition parameters
- show_errmsg - Whether to throw and display when an exception occurs

### attr

​	attr(attr: str) -> str

​	Get the value of an attribute of an element.

​	Parameter Description:

- attr - Attribute name



## OptionsManager class

​	class OptionsManager(path: str = None)

​	The class that manages the content of the configuration file.

​	Parameter Description:

- path - Ini file path, if not imported, the configs.ini file in the current folder is read by default

### get_value

​	get_value(section: str, item: str) -> Any

​	Get the configured value.

​	Parameter Description:

- section - Paragraph name
- item - Configuration item name

### get_option

​	get_option(section: str) -> dict

​	Return configuration information for the entire paragraph in dictionary format.

​	Parameter Description:

- section - Paragraph name

### set_item

​	set_item(section: str, item: str, value: str) -> None

​	Set configuration values.

​	Parameter Description:

- section - Paragraph name
- item - Configuration item name
- value - Content of value

### save

​	save(path: str = None) -> None

​	Save the settings to a file.

​	Parameter Description:

- path - The path of the ini file, which is saved to the module folder by default



## DriverOptions class

​	class DriverOptions(read_file=True)

​	The chrome browser configuration class, inherited from the Options class of selenium.webdriver.chrome.options, adds methods to delete configuration and save to file.

​	Parameter Description:

- read_file - Boolean, specifies whether to read configuration information from the ini file when creating

### remove_argument

​	remove_argument(value: str) -> None

​	Remove a setting.

​	Parameter Description:

- value - The attribute value to be removed

### remove_experimental_option

​	remove_experimental_option(key: str) -> None

​	Remove an experiment setting and delete the incoming key value.

​	Parameter Description:

- key - The key value of the experiment to be removed

### remove_argument

​	remove_argument() -> None

​	Remove all plug-ins, because the plug-in is stored in the entire file, it is difficult to remove one of them, so if you need to set, remove all and reset.

### save()

​	save(path: str = None) -> None

​	Save the settings to a file.

​	Parameter Description:

- path - The path of the ini file, which is saved to the module folder by default



## Useful methods

### set_paths

​	set_paths(driver_path: str = None, chrome_path: str = None, debugger_address: str = None, global_tmp_path: str = None, download_path: str = None) -> None

​	Convenient way to set the path, save the incoming path to the default ini file, and check whether the chrome and chromedriver versions match.

​	Parameter Description:

- driver_path - chromedriver.exe path
- chrome_path - chrome.exe path
- debugger_address - Debug browser address, for example: 127.0.0.1:9222
- download_path - Download path
- global_tmp_path - Temporary folder path

### check_driver_version

​	check_driver_version(driver_path: str = None, chrome_path: str = None) -> bool

​	Check if the chrome and chromedriver versions match.

​	Parameter Description:

- driver_path - chromedriver.exe path
- chrome_path - chrome.exe path
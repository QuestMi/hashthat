## 2020-01-01 Test-Driven Development with Django 

### 为什么TDD很重要？[Why Tdd is important]

TDD可以让您对复杂的系统充满信心，并帮助团队更具自主性，以便能快速定位错误的位置。

### TDD 的理念（TDD phiosophy） 

1. 不要写任何的代码，直到有一个失败的测试；
2. 编写代码通过测试；

当想在软件中构建某个东西时，首先应创建一个测试，来测试你想要的结果，然后确保测试失败，再开始编写代码，让测试通过；

### 功能测试

##### 用selenium编写测试

##### 创建虚拟环境

```python
# 安装virtualenv
pip3 install virtualenv

# 创建名为venv的虚拟环境
virtualenv  venv

# 激活虚拟环境
source venv/bin/activate 

# 安装selenium 
pip install selenium 
```

##### 编写测试

```python
# func_test.py 
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('http://127.0.0.1:8000')

assert browser.page_source.find('install')
```

##### 运行代码

```python
python funct_test.py 
# 没有任何返回 
```

##### 编写代码让测试通过

```python
pip install django 

# 创建django项目
django-admin startproject hashthat 

cd hashthat

# 启动项目
python manage.py runserver 

# 再次运行测试
cd .. 
python funct_test.py 
# 可以看到浏览器中有一个django的初始页面
```

### Django 的测试框架（The Django testing framework）

```python
# 创建一个django的app
cd hashthat 
django-admin startapp hashing 

cd hashing 
```

##### 打开tests.py 文件

```python
from django.test import TestCase 
from selenium import webdriver 

class FunctionalTest(TestCase):
   def setUp(self):
       self.browser = webdriver.Chrome() 
    
   def test_there_is_homepage(self):
    	 self.browser.get('http://127.0.0.1:8000')
       self.assertIn('install', self.browser.page_source)
  
   def tearDown(self):
       self.browser.quit() 
```

##### 运行测试：

```python
python manage.py test 
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 2.274s

OK
Destroying test database for alias 'default'...
```

##### 扩展测试

```python
# 打开tests.py,修改测试文件
class FunctionTest(TestCase):
    def setUp(self):
        self.brower = webdriver.Chrome()
    
    def test_there_is_homepage(self):
    		self.browser.get('http://127.0.0.1:8000')
    		self.assertIn('install', self.browser.page_source)
    
    def test_hash_of_hello(self):
    		self.browser.get('http://127.0.0.1:8000')
    		text = self.browser.find_element_by_id('id_text')
    		self.browser.send_keys('hello')
    		self.browser_element_by_name('submit').click()
    		self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)
    
    def tearDown(self):
    		self.browser.quit()
```

##### 运行测试

```python
python manage.py test 
# 报错了，很正常
```

### 单元测试（Unit Test）

```python
# 打开tests.py 文件
from django.test import TestCase
from selenium import webdriver

#
# class FunctionalTest(TestCase):
#
#     def setUp(self) -> None:
#         self.browser = webdriver.Chrome()
#
#     def test_there_is_homepage(self):
#         self.browser.get('http://localhost:8000')
#         self.assertIn('Enter hash here:', self.browser.page_source)
#
#     def test_hash_of_hello(self):
#         self.browser.get('http://localhost:8000')
#         text = self.browser.find_element_by_id('id_text')
#         text.send_keys('hello')
#         self.browser.find_element_by_name('submit').click()
#         self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)
#
#     def tearDown(self) -> None:
#         self.browser.quit()


class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

```

##### 运行测试

```python
>> python manage.py test 

Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_home_homepage_template (hashing.tests.UnitTestCast)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/xiaoqinglin/projects/hashthat/hashing/tests.py", line 29, in test_home_homepage_template
    self.assertTemplateUsed(response, 'hashing/home.html')
  File "/Users/xiaoqinglin/projects/hashthat/venv/lib/python3.7/site-packages/django/test/testcases.py", line 639, in assertTemplateUsed
    self.fail(msg_prefix + "No templates used to render the response")
AssertionError: No templates used to render the response

----------------------------------------------------------------------
Ran 1 test in 0.010s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

##### 将app加入的settings.py中，配置路由

```python
# settings.py 
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'hashing',
]


# urls.py 
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
]
```

##### 打开hashing/views.py 

```python
from django.shortcuts import render 

def home(request):
    return render(request, 'hashing/home.html')
```

##### 新建hashing/templates/hashing/home.html

```html
// 在home.html中添加hello
Hello!
```

##### 运行测试

```python
>> python manage.py test 

Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.008s

OK
Destroying test database for alias 'default'...

```

##### 打开tests.py

```python
class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())
```

##### 运行测试

```python
>>  python manage.py test
System check identified no issues (0 silenced).
E
======================================================================
ERROR: hashing.tests (unittest.loader._FailedTest)
----------------------------------------------------------------------
ImportError: Failed to import test module: hashing.tests
Traceback (most recent call last):
  File "/usr/local/Cellar/python/3.7.3/Frameworks/Python.framework/Versions/3.7/lib/python3.7/unittest/loader.py", line 436, in _find_test_path
    module = self._get_module_from_name(name)
  File "/usr/local/Cellar/python/3.7.3/Frameworks/Python.framework/Versions/3.7/lib/python3.7/unittest/loader.py", line 377, in _get_module_from_name
    __import__(name)
  File "/Users/xiaoqinglin/projects/hashthat/hashing/tests.py", line 3, in <module>
    from .forms import HashForm
ModuleNotFoundError: No module named 'hashing.forms'

```

##### 测试页面数据

##### 新建hasing/forms.py 

```python
from django import forms 

class HashForm(forms.Form):
	text = forms.CharField(label='Enter hash here:', widget=forms.Textarea) 
```

##### 打开hashing/views.py

```python
from django.shortcuts import render 
from .forms import HashForm 

def home(request):
	form=HashForm()
	return render(request, 'hashing/home.html', {'form': form})
```

##### 打开hashing/templates/hashing/home.html

```python
<form action="{% url 'home' %}" method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <input name="submit" type="submit" value="Hash">
</form>
```

##### 哈希测试

##### 打开hashing/tests.py 

```python
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib

class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
...
----------------------------------------------------------------------
Ran 3 tests in 0.013s

OK
Destroying test database for alias 'default'...
```

##### 打开hashing/models.py 

```python
from django.db import models 

class Hash(models.Model):
    text = models.TextField()
    hash = models.CharField(max_length=64)
```

##### 运行数据迁移

```python
>> python manage.py makemigrations
Migrations for 'hashing':
  hashing/migrations/0001_initial.py
    - Create model Hash
    
>> python manage.py migrate 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, hashing, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying hashing.0001_initial... OK
  Applying sessions.0001_initial... OK
```

##### 编写用户输入测试

##### 打开hashing/tests.py 

```python
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash

class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def test_hash_object(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

```

##### 打开hashthat/settings.py

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        'TEST_NAME': os.path.join(BASE_DIR, 'test_db.sqlite3') # 增加这一项测试数据库配置
    }
}
```

##### 保存model

##### 打开hashing/views.py

```python
from django.shortcuts import render 
from .forms import HashForm 
from .models import Hash 
import hashlib 

def home(request):
	if requests.method == 'POST':
		filled_form = HashForm(request.POST)
		if filled_form.is_valid():
			text = filled_form.cleaned_data['text']
			text_hash = hashlib.sha256(text.encode('utf-8')).hexdigest()
            try:
                Hash.objects.get(hash=text_hash)
            except Hash.DoesNotExist:
                hash = Hash()
                hash.text = text
                hash.hash = text_hash
                hash.save()
    form = HashForm()
    return render(request, 'hashing/home.html', {'form': form})
```

##### 运行测试

```python
>> python manage.py test   
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
....
----------------------------------------------------------------------
Ran 4 tests in 0.036s

OK
Destroying test database for alias 'default'...
```

##### 测试post请求

##### 打开hashing/tests.py

```python
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash

class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def save_hash(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        return hash

    def test_hash_object(self):
        hash = self.save_hash()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

    def test_viewing_hash(self):
        hash = self.save_hash()
        response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertContains(response, 'hello')

```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
....F
======================================================================
FAIL: test_viewing_hash (hashing.tests.UnitTestCast)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/xiaoqinglin/projects/hashthat/hashing/tests.py", line 58, in test_viewing_hash
    self.assertContains(response, 'hello')
  File "/Users/xiaoqinglin/projects/hashthat/venv/lib/python3.7/site-packages/django/test/testcases.py", line 446, in assertContains
    response, text, status_code, msg_prefix, html)
  File "/Users/xiaoqinglin/projects/hashthat/venv/lib/python3.7/site-packages/django/test/testcases.py", line 418, in _assert_contains
    " (expected %d)" % (response.status_code, status_code)
AssertionError: 404 != 200 : Couldn't retrieve content: Response code was 404 (expected 200)

----------------------------------------------------------------------
Ran 5 tests in 0.019s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

##### 通过测试

##### 打开urls.py

```python
from django.contrib import admin
from django.urls import path
from hashing import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
    path('hash/<str:hash>', views.hash, name='hash') # 增加该列
]
```

##### 打开hashing/views.py 

```python
# Create your views here.
def home(request):
    if request.method == 'POST':
        filled_form = HashForm(request.POST)
        if filled_form.is_valid():
            text = filled_form.cleaned_data['text']
            text_hash = hashlib.sha256(text.encode('utf-8')).hexdigest()
            try:
                Hash.objects.get(hash=text_hash)
            except Hash.DoesNotExist:
                hash = Hash()
                hash.text = text
                hash.hash = text_hash
                hash.save()
            return redirect('hash', hash=text_hash)
    form = HashForm()
    return render(request, 'hashing/home.html', {'form': form})

def hash(request, hash):
    hash = Hash.objects.get(hash=hash)
    return render(request, 'hashing/hash.html', {'hash': hash})
```

##### 新建hashing/templates/hashing/hash.html 

```python
<h2>Hash:</h2>
<h2>{{ hash.hash  }}</h2>
<h2>Text:</h2>
{{ hash.text }}
<h3><a href="{% url 'home' %}">Home</a></h3>
```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.....
----------------------------------------------------------------------
Ran 5 tests in 0.015s

OK
Destroying test database for alias 'default'...

```

##### 用户功能输入测试

##### 打开hashing/test.py (取消功能测试代码)

```python
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash


class FunctionalTest(TestCase):

    def setUp(self) -> None:
        self.browser = webdriver.Chrome()

    def test_there_is_homepage(self):
        self.browser.get('http://localhost:8000')
        self.assertIn('Enter hash here:', self.browser.page_source)

    def test_hash_of_hello(self):
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        self.browser.find_element_by_name('submit').click()
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def tearDown(self) -> None:
        self.browser.quit()


class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def save_hash(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        return hash

    def test_hash_object(self):
        hash = self.save_hash()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

    def test_viewing_hash(self):
        hash = self.save_hash()
        response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertContains(response, 'hello')
```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.......
----------------------------------------------------------------------
Ran 7 tests in 4.027s

OK
Destroying test database for alias 'default'...
```

##### 编写异常的测试，打开hashing/tests.py

```python
from django.core.exceptions import ValidationError
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash


class FunctionalTest(TestCase):

    def setUp(self) -> None:
        self.browser = webdriver.Chrome()

    def test_there_is_homepage(self):
        self.browser.get('http://localhost:8000')
        self.assertIn('Enter hash here:', self.browser.page_source)

    def test_hash_of_hello(self):
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        self.browser.find_element_by_name('submit').click()
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def tearDown(self) -> None:
        self.browser.quit()


class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def save_hash(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        return hash

    def test_hash_object(self):
        hash = self.save_hash()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

    def test_viewing_hash(self):
        hash = self.save_hash()
        response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertContains(response, 'hello')

    def test_bad_data(self): 
        def bad_hash():
            hash = Hash()
            hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824gggg'
            hash.full_clean()
        self.assertRaises(ValidationError, bad_hash
```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
........
----------------------------------------------------------------------
Ran 8 tests in 4.151s

OK
Destroying test database for alias 'default'...
```

##### 等待（waitting）

##### 打开hashing/tests.py 

```python
from django.core.exceptions import ValidationError
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash


class FunctionalTest(TestCase):

    def setUp(self) -> None:
        self.browser = webdriver.Chrome()

    def test_there_is_homepage(self):
        self.browser.get('http://localhost:8000')
        self.assertIn('Enter hash here:', self.browser.page_source)

    def test_hash_of_hello(self):
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        self.browser.find_element_by_name('submit').click()
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def test_hash_ajax(self):
        # 增加
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def tearDown(self) -> None:
        self.browser.quit()


class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def save_hash(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        return hash

    def test_hash_object(self):
        hash = self.save_hash()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

    def test_viewing_hash(self):
        hash = self.save_hash()
        response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertContains(response, 'hello')

    def test_bad_data(self):
        def bad_hash():
            hash = Hash()
            hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824ggggg'
            hash.full_clean()
        self.assertRaises(ValidationError, bad_hash)
```

##### 打开hashing/urls.py

```python
from django.contrib import admin
from django.urls import path
from hashing import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
    path('hash/<str:hash>', views.hash, name='hash'),
    path('qucik_hash', views.quick_hash, name='quick_hash') # 增加这一行
]
```

##### 打开hashing/views.py

```python
from django.shortcuts import render, redirect
from .forms import HashForm
from .models import Hash
import hashlib
from django.http import JsonResponse


# Create your views here.
def home(request):
    if request.method == 'POST':
        filled_form = HashForm(request.POST)
        if filled_form.is_valid():
            text = filled_form.cleaned_data['text']
            text_hash = hashlib.sha256(text.encode('utf-8')).hexdigest()
            try:
                Hash.objects.get(hash=text_hash)
            except Hash.DoesNotExist:
                hash = Hash()
                hash.text = text
                hash.hash = text_hash
                hash.save()
            return redirect('hash', hash=text_hash)
    form = HashForm()
    return render(request, 'hashing/home.html', {'form': form})


def hash(request, hash):
    hash = Hash.objects.get(hash=hash)
    return render(request, 'hashing/hash.html', {'hash': hash})


def quick_hash(request):
    # 增加了这里
    text = request.GET['text']
    return JsonResponse({'hash': hashlib.sha256(text.encode('utf-8')).hexdigest()})
```

##### 打开hashing/templates/hashing/home.html

```html 
<form action="{% url 'home' %}" method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <input name="submit" type="submit" value="Hash">
</form>

<h2 id="quick_hash"></h2>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
<script>
    $('#id_text').keyup(function () {
       var text = $(this).val();

       $.ajax({
          url:'/quick_hash',
          data:{
              'text': text
          },
          dataType:'json',
          success: function (data) {
              $('#quick_hash').text(data['hash']);
          }
       });
    });
</script>
```

##### 打开hashing/urls.py 

```python
from django.contrib import admin
from django.urls import path
from hashing import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
    path('hash/<str:hash>', views.hash, name='hash'),
    path('quick_hash', views.quick_hash, name='quick_hash') # 增加这一行
]
```

##### 打开hashing/tests.py

```python
from django.core.exceptions import ValidationError
from django.test import TestCase
from selenium import webdriver
from .forms import HashForm
import hashlib
from .models import Hash
import time


class FunctionalTest(TestCase):

    def setUp(self) -> None:
        self.browser = webdriver.Chrome()

    def test_there_is_homepage(self):
        self.browser.get('http://localhost:8000')
        self.assertIn('Enter hash here:', self.browser.page_source)

    def test_hash_of_hello(self):
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        self.browser.find_element_by_name('submit').click()
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def test_hash_ajax(self):
        self.browser.get('http://localhost:8000')
        text = self.browser.find_element_by_id('id_text')
        text.send_keys('hello')
        time.sleep(5)  # wait for AJAX # 增加 这一行
        self.assertIn('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', self.browser.page_source)

    def tearDown(self) -> None:
        self.browser.quit()


class UnitTestCast(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'hashing/home.html')

    def test_hash_form(self):
        form = HashForm(data={'text': 'hello'})
        self.assertTrue(form.is_valid())

    def test_hash_func_works(self):
        text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest()
        self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824', text_hash)

    def save_hash(self):
        hash = Hash()
        hash.text = 'hello'
        hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
        hash.save()
        return hash

    def test_hash_object(self):
        hash = self.save_hash()
        pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertEqual(hash.text, pulled_hash.text)

    def test_viewing_hash(self):
        hash = self.save_hash()
        response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
        self.assertContains(response, 'hello')

    def test_bad_data(self):
        def bad_hash():
            hash = Hash()
            hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824ggggg'
            hash.full_clean()

        self.assertRaises(ValidationError, bad_hash)
```

##### 运行测试

```python
>> python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.........
----------------------------------------------------------------------
Ran 9 tests in 7.559s

OK
Destroying test database for alias 'default'...
```

##### 


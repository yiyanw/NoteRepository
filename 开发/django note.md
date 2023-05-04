默认目录介绍

```
mysite
|-- manage.py [项目管理，启动，创建app，数据管理]
|-- mysite
	|-- __init__.py 
	|-- settings.py [配置文件]
	|-- urls.py [url与函数对应关系]
    |-- asgi.py [接受网络请求]
    |-- wsgi.py	[接受网络请求]
```



创建app (component)

* 在项目根目录下执行

```
python manage.py startapp <component name>
```

app 结构介绍

```
app
|-- __init__.py
|-- admin.py [后台管理模块]
|-- apps.py [app启动class]
|-- migrations [数据库变更记录]
	|-- __init__.py
|-- models.py [django对数据库操作的封装]
|-- tests.py [单元测试部分]
|-- views.py [对应urls.py中配置的函数]
```

注册app

* 在settings.py中注册app

  * 在 INSTALLED_APPS字段中添加对应app的启动类名称(apps.py里的那个类)

  * ```python
    # e.g. 在App01这个app中，apps.py中有个class叫 App01Config
    class App01Config(AppConfig):
        default_auto_field = 'django.db.models.BigAutoField'
        name = 'App01'
        
        
    # 将App01.apps.App01Config添加在INSTALLED_APPS中
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'App01.apps.App01Config',
    ]
    ```



注册url

* 在urls.py中注册url，并指定调用的函数

  ```python
  from App01 import views
  
  urlpatterns = [
      path('admin/', admin.site.urls), # 默认生成的url可以删掉
      path('test/', views.test)
  ]
  ```

* 在对应的views.py 中添加被调用的方法

  ```python
  from django.http import HttpResponse
  
  # Create your views here.
  def test(request):
      return HttpResponse("welcome")
  ```

* 结果

  <img src="django note/image-20220812180159312-16602913288861.png" alt="image-20220812180159312" style="zoom:50%;" />

嵌入 react

https://icode.best/i/28685144220754



windows 查看端口，kill进程

https://www.runoob.com/w3cnote/windows-finds-port-usage.html





# pymysql

1. 连接数据库

```python
import pymysql

...

db = pymysql.connect(host=ip,
                     user=username,
                     password=password,
                     database=database,
                     port=port,
                     charset='utf8')
```

2. 执行sql query

```python
cursor = db.cursor()
cursor.execute('<SQL QUERY>')
cursor.close()

# cursor一次只能执行一条，批量执行需要for loop
```

3. 读取sql文件

   ```python
   def read_sql_file(path):
       with open(path, "r") as sql_file:
           ret = sql_file.read().split(';')
           ret.pop()
           return ret
   ```

   

## mysql操作

1. 登录mysql `mysql -u <username> -p`

2. 查看用户权限

​		`SHOW GRANTS FOR 'username'@'hostname';`

3. 删除用户

`drop user 'username';`

但是目前测试下来，删不干净。然后可以接着删 + 刷新权限：

```sql
delete from mysql.user where user='<your-user>';
flush privileges;
```

1. 
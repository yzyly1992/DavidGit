#### 2019 Python Web 开发建站教程

作为初学完Python基础知识的同学，一定会急切想知道如何使这些基础Python知识，如何应用Python做出些有趣的东西来。建立一个个人的博客网站是一个非常有成就感的不错选择。本教程基于廖雪峰老师2016年的Python3教程实践部分章节结合最新的前端技术改编而来**2019 Python Web 应用开发教程**。相反于使用成熟方便的Python Web开发框架像Flask, Django等。本教程会从最底代码开始来构建数据库，构建Web框架，构建ORM，构建MVC，构建API，到构建前端页面CSS/HTML，Javascript， DOM操作再到服务器部署等。用详尽的代码和注释一步一步引导读者进行网站开发的操作。

最终的网站效果会类似[凹大卜](https://aodabo.tech)这样。网站的主要功能及页面包括：

- 用户的注册，登陆，注销
- 发布新日志，编辑存在日志
- 用户发布日志评论
- 管理日志，用户及日志评论

本教程所有的代码请参考我的[GitHub]()。如发现教程或代码错误，或者有任何疑问，欢迎在教程页面下留言讨论！

以下为教程导航目录：

- [Python 网站开发(1) -- 搭建开发环境](https://aodabo.tech/blog/0015467140723153897237f40b54031ad838d91cc719f12000)
- [Python 网站开发(2) -- 编写网站骨架](https://aodabo.tech/blog/0015467140257875f2beaf701b94628a91c360026d97d13000)
- [Python 网站开发(3) -- 编写ORM](https://aodabo.tech/blog/00154671395836327fd0f81c8724e7fba06c498aea2b630000)
- [Python 网站开发(4) -- 编写Model](https://aodabo.tech/blog/001546713871394a2814d2c180b4e6f8d30c62a3eaf218a000)
- [Python 网站开发(5) -- 搭建Web框架](https://aodabo.tech/blog/001546713806610decd7219a0a0455c9c28ed34e5e32543000)
- [Python 网站开发(6) -- 编写配置文件](https://aodabo.tech/blog/001546713699515d77c2680f9be4c8fbe67a2a11528ba5c000)
- [Python 网站开发(7) -- 搭建MVC](https://aodabo.tech/blog/00154671363904297f04d14f8ad4984bbe4d9b75356af2f000)
- [Python 网站开发(8) -- 搭建API](https://aodabo.tech/blog/0015467135837969036fa273d0b48feb4a3719fe76ee0da000)
- [Python 网站开发(9) -- 构建前端](https://aodabo.tech/blog/001546713315865e2ca9cfd0b8b4ea78be9a4253b7991ce000)
- [Python 网站开发(10) -- 用户注册和登录页面](https://aodabo.tech/blog/001546713217136e1512fe9a9754066b62d867d9ea283ff000)
- [Python 网站开发(11) -- 日志编写页面](https://aodabo.tech/blog/001546713145327a743cdab821a4220bec980d36bfcf8e3000)
- [Python 网站开发(12) -- 日志列表页面](https://aodabo.tech/blog/0015467129787908a6e60563fff4563b73b209f4d1f9b74000)
- [Python 网站开发(13) -- 日志内容详情页面](https://aodabo.tech/blog/0015467128745955dd6d3562ca24cc299ebf72242082c0c000)
- [Python 网站开发(14) -- 管理页面](https://aodabo.tech/blog/001546712781110f93a3009f6ba4a63ba868259827f8349000)
- [Python 网站开发(15) -- 部署网站到远程服务器](https://aodabo.tech/blog/0015467126618282f6a741af9b34043922eea07e4ffb077000)
- [Python 网站开发(16) -- Python建站的其他可能性](https://aodabo.tech/blog/001546712024642647c50c93de34db58ea68238cf94c030000)



#### 搭建开发环境

首先，确定系统安装版本为 3.6.X 或 3.7.X 及以上，若版本为 2.X.X ，请安装[最新版](https://www.python.org/downloads/)的 3.X 的Python.

~~~ python
$ python3 --version
Python 3.6.7
~~~

然后，用`pip`安装网站开发所需要的第三方库：

- 异步框架的 aiohttp
- 前端模板引擎 jinja2
- 数据库 MySQL的Python异步驱动程序 aiomysql (需要先[下载](https://dev.mysql.com/downloads/mysql/)安装最新版的 MySQL, 选择免费的 MySQL Community Server 下载安装就好)
- 轻量级标记语言Markdown, 将文本转换为有效的HTML

```
$ pip3 install aiohttp jinja2 aiomysql markdown
```



#### 构建项目结构

选择一个工作目录，建立如下的目录结构:

~~~
awesome-website/         <-- 根目录
|
+- backup/               <-- 备份目录
|
+- conf/                 <-- 配置文件
|
+- dist/                 <-- 打包目录
|
+- www/                  <-- Web目录，存放.py文件
|  |
|  +- static/            <-- 存放静态文件
|  |
|  +- templates/         <-- 存放模板文件
|
+- LICENSE               <-- 代码LICENSE
~~~

创建好项目的目录结构后，建议同时建立git仓库并同步到GitHub, 保证代码储存及修改的安全。想要了解或学习 git 和 GitHub 的用法，参见廖大 [git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000).



#### 编写网站骨架

为了搭建一个高效的网站，网站的IO处理要建立在asyncio(异步io)的基础上, 我们可以用 aiohttp 写一个基本的服务器应用 `app.py` 存放在`www`目录:

~~~python
import logging; logging.basicConfig(level=logging.INFO)
import asyncio, os, json, time
from datetime import datetime
from aiohttp import web

## 定义服务器响应请求的的返回为 "Awesome Website"
def index(request):
    return web.Response(body=b'<h1>Awesome Website</h1>')

## 建立服务器应用，持续监听本地9000端口的http请求，并异步对首页"/"进行响应
async def init(loop):
    app = web.Application(loop=loop)
    app.router.add_route('GET', '/', index)
    srv = await loop.create_server(app.make_handler(), '127.0.0.1', 9000)
    logging.info('server started at http://127.0.0.1:9000...')
    return srv

loop = asyncio.get_event_loop()
loop.run_until_complete(init(loop))
loop.run_forever()
~~~

在`www`目录下运行这个 `app.py`, 服务器将在9000端口持续监听 http 请求，并异步对首页 `/` 进行响应：

~~~ python
$ python3 app.py
INFO:root:server started at http://127.0.0.1:9000...
~~~

打开浏览器输入地址 `http://127.0.0.1:9000` 进行测试，如果返回我们设定好的`Awesome Website`字符串，就说明我们网站服务器应用的框架已经搭好了。



#### 编写ORM*

对象关系映射 (Object Relational Mapping, 简称ORM) 模式是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术。换句话说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系数据库中。

在一个网站中，所有的数据（包括用户，日志，评论等）都存储在数据库中。我们的网站`awesome-website`选择用MySQL作为数据库。访问数据库需要创建数据库连接，游标对象，执行SQL语句，处理异常，清理资源。这些访问数据库的代码如果分散到每个函数中去，十分难以维护，效率低下不利于复用。因此，我们将常用的MySQL数据库操作用函数封装起来，便于网站调用。

由于我们的网站基于异步编程，系统的每一层都必须是异步。`aiomysql`为MySQL数据库提供了异步IO的驱动。

##### 创建连接池

我们需要创建一个全局的连接池，每个HTTP请求都可以从连接池中直接获取数据库连接。使用连接池的好处是不必频繁地打开和关闭数据库连接，而是能复用就尽量复用。

连接池由全局变量`__pool`存储，缺省情况下将编码设置为`utf8`，自动提交事务。在`www`目录下新建 `orm.py`加入以下代码：

```python
import asyncio, logging, aiomysql

def log(sql, args=()):
    logging.info('SQL: %s' % sql)

async def create_pool(loop, **kw):
    logging.info('create database connection pool...')
    global __pool
    __pool = await aiomysql.create_pool(
        host=kw.get('host', 'localhost'),
        port=kw.get('port', 3306),
        user=kw['user'],
        password=kw['password'],
        db=kw['db'],
        charset=kw.get('charset', 'utf8'),
        autocommit=kw.get('autocommit', True),
        maxsize=kw.get('maxsize', 10),
        minsize=kw.get('minsize', 1),
        loop=loop
    )
```

##### Select

要执行SELECT语句，我们用`select`函数执行，需要传入SQL语句和SQL参数。缀加以下代码至`orm.py`：

```python
async def select(sql, args, size=None):
    log(sql, args)
    global __pool
    with (await __pool) as conn:
        cur = await conn.cursor(aiomysql.DictCursor)
        await cur.execute(sql.replace('?', '%s'), args or ())
        if size:
            rs = await cur.fetchmany(size)
        else:
            rs = await cur.fetchall()
        await cur.close()
        logging.info('rows returned: %s' % len(rs))
        return rs
```

SQL语句的占位符是`?`，而MySQL的占位符是`%s`，`select()`函数在内部自动替换。注意要始终坚持使用带参数的SQL，而不是自己拼接SQL字符串，这样可以防止SQL注入攻击。

##### Insert, Update, Delete

要执行INSERT、UPDATE、DELETE语句，可以定义一个通用的`execute()`函数，因为这3种SQL的执行都需要相同的参数，以及返回一个整数表示影响的行数。缀加以下代码至`orm.py`：

```python
async def execute(sql, args):
    log(sql)
    with (await __pool) as conn:
        try:
            cur = await conn.cursor()
            await cur.execute(sql.replace('?', '%s'), args)
            affected = cur.rowcount
            await cur.close()
        except BaseException as e:
            raise
        return affected
```

`execute()`函数和`select()`函数所不同的是，cursor对象不返回结果集，而是通过`rowcount`返回结果数。

##### ORM

首先要定义的是所有ORM映射的基类`Model`。缀加以下代码至`orm.py`：

```python
class Model(dict, metaclass=ModelMetaclass):

    def __init__(self, **kw):
        super(Model, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Model' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

    def getValue(self, key):
        return getattr(self, key, None)

    def getValueOrDefault(self, key):
        value = getattr(self, key, None)
        if value is None:
            field = self.__mappings__[key]
            if field.default is not None:
                value = field.default() if callable(field.default) else field.default
                logging.debug('using default value for %s: %s' % (key, str(value)))
                setattr(self, key, value)
        return value
```

以及`Field`和各种`Field`子类。缀加以下代码至`orm.py`：

```python
class Field(object):

    def __init__(self, name, column_type, primary_key, default):
        self.name = name
        self.column_type = column_type
        self.primary_key = primary_key
        self.default = default

    def __str__(self):
        return '<%s, %s:%s>' % (self.__class__.__name__, self.column_type, self.name)
    
class StringField(Field):

    def __init__(self, name=None, primary_key=False, default=None, ddl='varchar(100)'):
        super().__init__(name, ddl, primary_key, default)

class BooleanField(Field):

    def __init__(self, name=None, default=False):
        super().__init__(name, 'boolean', False, default)

class IntegerField(Field):

    def __init__(self, name=None, primary_key=False, default=0):
        super().__init__(name, 'bigint', primary_key, default)

class FloatField(Field):

    def __init__(self, name=None, primary_key=False, default=0.0):
        super().__init__(name, 'real', primary_key, default)

class TextField(Field):

    def __init__(self, name=None, default=None):
        super().__init__(name, 'text', False, default)
```

注意到`Model`只是一个基类，要将具体的子类如`User`的映射信息读取出来需要通过metaclass：`ModelMetaclass`。 这样，任何继承自Model的类（比如User），会自动通过ModelMetaclass扫描映射关系，并存储到自身的类属性如`__table__`、`__mappings__`中。缀加以下代码至`orm.py`：

```python
def create_args_string(num):
    L = []
    for n in range(num):
        L.append('?')
    return ', '.join(L)

class ModelMetaclass(type):

    def __new__(cls, name, bases, attrs):
        # 排除Model类本身:
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        # 获取table名称:
        tableName = attrs.get('__table__', None) or name
        logging.info('found model: %s (table: %s)' % (name, tableName))
        # 获取所有的Field和主键名:
        mappings = dict()
        fields = []
        primaryKey = None
        for k, v in attrs.items():
            if isinstance(v, Field):
                logging.info('  found mapping: %s ==> %s' % (k, v))
                mappings[k] = v
                if v.primary_key:
                    # 找到主键:
                    if primaryKey:
                        raise RuntimeError('Duplicate primary key for field: %s' % k)
                    primaryKey = k
                else:
                    fields.append(k)
        if not primaryKey:
            raise RuntimeError('Primary key not found.')
        for k in mappings.keys():
            attrs.pop(k)
        escaped_fields = list(map(lambda f: '`%s`' % f, fields))
        attrs['__mappings__'] = mappings # 保存属性和列的映射关系
        attrs['__table__'] = tableName
        attrs['__primary_key__'] = primaryKey # 主键属性名
        attrs['__fields__'] = fields # 除主键外的属性名
        # 构造默认的SELECT, INSERT, UPDATE和DELETE语句:
        attrs['__select__'] = 'select `%s`, %s from `%s`' % (primaryKey, ', '.join(escaped_fields), tableName)
        attrs['__insert__'] = 'insert into `%s` (%s, `%s`) values (%s)' % (tableName, ', '.join(escaped_fields), primaryKey, create_args_string(len(escaped_fields) + 1))
        attrs['__update__'] = 'update `%s` set %s where `%s`=?' % (tableName, ', '.join(map(lambda f: '`%s`=?' % (mappings.get(f).name or f), fields)), primaryKey)
        attrs['__delete__'] = 'delete from `%s` where `%s`=?' % (tableName, primaryKey)
        return type.__new__(cls, name, bases, attrs)
```

然后，我们往Model类添加class方法，就可以让所有子类调用class方法：

```python
class Model(dict):

    ...

    @classmethod
    async def findAll(cls, where=None, args=None, **kw):
        ## find objects by where clause
        sql = [cls.__select__]
        if where:
            sql.append('where')
            sql.append(where)
        if args is None:
            args = []
        orderBy = kw.get('orderBy', None)
        if orderBy:
            sql.append('order by')
            sql.append(orderBy)
        limit = kw.get('limit', None)
        if limit is not None:
            sql.append('limit')
            if isinstance(limit, int):
                sql.append('?')
                args.append(limit)
            elif isinstance(limit, tuple) and len(limit) == 2:
                sql.append('?, ?')
                args.extend(limit)
            else:
                raise ValueError('Invalid limit value: %s' % str(limit))
        rs = await select(' '.join(sql), args)
        return [cls(**r) for r in rs]

    @classmethod
    async def findNumber(cls, selectField, where=None, args=None):
        ## find number by select and where
        sql = ['select %s _num_ from `%s`' % (selectField, cls.__table__)]
        if where:
            sql.append('where')
            sql.append(where)
        rs = await select(' '.join(sql), args, 1)
        if len(rs) == 0:
            return None
        return rs[0]['_num_']

    @classmethod
    async def find(cls, pk):
        ## find object by primary key
        rs = await select('%s where `%s`=?' % (cls.__select__, cls.__primary_key__), [pk], 1)
        if len(rs) == 0:
            return None
        return cls(**rs[0])
```

往Model类添加实例方法，就可以让所有子类调用实例方法：

```python
class Model(dict):

    ...

	async def save(self):
        args = list(map(self.getValueOrDefault, self.__fields__))
        args.append(self.getValueOrDefault(self.__primary_key__))
        rows = await execute(self.__insert__, args)
        if rows != 1:
            logging.warn('failed to insert record: affected rows: %s' % rows)

    async def update(self):
        args = list(map(self.getValue, self.__fields__))
        args.append(self.getValue(self.__primary_key__))
        rows = await execute(self.__update__, args)
        if rows != 1:
            logging.warn('failed to update by primary key: affected rows: %s' % rows)

    async def remove(self):
        args = [self.getValue(self.__primary_key__)]
        rows = await execute(self.__delete__, args)
        if rows != 1:
            logging.warn('failed to remove by primary key: affected rows: %s' % rows)
```



#### 编写Model

`orm.py`编写完成后，就可以把网站应用需要的**三个表**（user, blog, comment）用`Model`表示出来。在`www`目录下，新建`models.py`:

```python
import time, uuid

from orm import Model, StringField, BooleanField, FloatField, TextField

def next_id():
    return '%015d%s000' % (int(time.time() * 1000), uuid.uuid4().hex)

class User(Model):
    __table__ = 'users'

    id = StringField(primary_key=True, default=next_id, ddl='varchar(50)')
    email = StringField(ddl='varchar(50)')
    passwd = StringField(ddl='varchar(50)')
    admin = BooleanField()
    name = StringField(ddl='varchar(50)')
    image = StringField(ddl='varchar(500)')
    created_at = FloatField(default=time.time)

class Blog(Model):
    __table__ = 'blogs'

    id = StringField(primary_key=True, default=next_id, ddl='varchar(50)')
    user_id = StringField(ddl='varchar(50)')
    user_name = StringField(ddl='varchar(50)')
    user_image = StringField(ddl='varchar(500)')
    name = StringField(ddl='varchar(50)')
    summary = StringField(ddl='varchar(200)')
    content = TextField()
    created_at = FloatField(default=time.time)

class Comment(Model):
    __table__ = 'comments'

    id = StringField(primary_key=True, default=next_id, ddl='varchar(50)')
    blog_id = StringField(ddl='varchar(50)')
    user_id = StringField(ddl='varchar(50)')
    user_name = StringField(ddl='varchar(50)')
    user_image = StringField(ddl='varchar(500)')
    content = TextField()
    created_at = FloatField(default=time.time)
```

在编写ORM时，给一个Field增加一个`default`参数可以让ORM自己填入缺省值，非常方便。并且，缺省值可以作为函数对象传入，在调用`save()`时自动计算。例如，主键`id`的缺省值是函数`next_id`，创建时间`created_at`的缺省值是函数`time.time`，可以自动设置当前日期和时间。日期和时间用`float`类型存储在数据库中，而不是`datetime`类型，这么做的好处是不必关心数据库的时区以及时区转换问题，排序非常简单，显示的时候，只需要做一个`float`到`str`的转换，也非常容易。

##### 初始化数据库表

由于网站表的数量较少，可以手动创建SQL脚本`schema.sql`到根目录：

```mysql
-- schema.sql

drop database if exists awesome;

create database awesome;

use awesome;

create user 'www-data'@'localhost' identified by 'www-data';
alter user 'www-data'@'localhost' identified with mysql_native_password by 'www-data';
grant select, insert, update, delete on awesome.* to 'www-data'@'localhost';

create table users (
    `id` varchar(50) not null,
    `email` varchar(50) not null,
    `passwd` varchar(50) not null,
    `admin` bool not null,
    `name` varchar(50) not null,
    `image` varchar(500) not null,
    `created_at` real not null,
    unique key `idx_email` (`email`),
    key `idx_created_at` (`created_at`),
    primary key (`id`)
) engine=innodb default charset=utf8;

create table blogs (
    `id` varchar(50) not null,
    `user_id` varchar(50) not null,
    `user_name` varchar(50) not null,
    `user_image` varchar(500) not null,
    `name` varchar(50) not null,
    `summary` varchar(200) not null,
    `content` mediumtext not null,
    `created_at` real not null,
    key `idx_created_at` (`created_at`),
    primary key (`id`)
) engine=innodb default charset=utf8;

create table comments (
    `id` varchar(50) not null,
    `blog_id` varchar(50) not null,
    `user_id` varchar(50) not null,
    `user_name` varchar(50) not null,
    `user_image` varchar(500) not null,
    `content` mediumtext not null,
    `created_at` real not null,
    key `idx_created_at` (`created_at`),
    primary key (`id`)
) engine=innodb default charset=utf8;
```

把SQL脚本 `schema.sql`放到MySQL命令行里执行，就完成了数据库表的初始化：

```
$ mysql -u root -p < schema.sql
```

然后我们可以编写数据访问代码`test.py`测试一下。如新建一个User的对象：

```python
import orm
from models import User, Blog, Comment

def test():
    await orm.create_pool(user='www-data', password='www-data', database='awesome')

    u = User(name='Test', email='test@example.com', passwd='1234567890', image='about:blank')

    await u.save()

for x in test():
    pass
```

运行`test.py`后，可以在MySQL客户端命令行查询，看看测试的数据是不是正常储存到MySQL里面。



#### 编写Web框架

由于`aiohttp`作为一个Web框架比较底层，我们还需要基于`aiohttp`编写一个更方便处理`URL`的Web框架。

在`www`目录新建`coroweb.py`:

~~~ python
import asyncio, os, inspect, logging, functools

from urllib import parse

from aiohttp import web

## apis是处理分页的模块,后面会编写,可以从github先下载到www下,以防报错
## APIError 是指API调用时发生逻辑错误
from apis import APIError

## 编写装饰函数 @get()
def get(path):
    ## Define decorator @get('/path')
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            return func(*args, **kw)
        wrapper.__method__ = 'GET'
        wrapper.__route__ = path
        return wrapper
    return decorator

## 编写装饰函数 @post()
def post(path):
    ## Define decorator @post('/path')
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            return func(*args, **kw)
        wrapper.__method__ = 'POST'
        wrapper.__route__ = path
        return wrapper
    return decorator

## 以下是RequestHandler需要定义的一些函数
def get_required_kw_args(fn):
    args = []
    params = inspect.signature(fn).parameters
    for name, param in params.items():
        if param.kind == inspect.Parameter.KEYWORD_ONLY and param.default == inspect.Parameter.empty:
            args.append(name)
    return tuple(args)

def get_named_kw_args(fn):
    args = []
    params = inspect.signature(fn).parameters
    for name, param in params.items():
        if param.kind == inspect.Parameter.KEYWORD_ONLY:
            args.append(name)
    return tuple(args)

def has_named_kw_args(fn):
    params = inspect.signature(fn).parameters
    for name, param in params.items():
        if param.kind == inspect.Parameter.KEYWORD_ONLY:
            return True

def has_var_kw_arg(fn):
    params = inspect.signature(fn).parameters
    for name, param in params.items():
        if param.kind == inspect.Parameter.VAR_KEYWORD:
            return True

def has_request_arg(fn):
    sig = inspect.signature(fn)
    params = sig.parameters
    found = False
    for name, param in params.items():
        if name == 'request':
            found = True
            continue
        if found and (param.kind != inspect.Parameter.VAR_POSITIONAL and param.kind != inspect.Parameter.KEYWORD_ONLY and param.kind != inspect.Parameter.VAR_KEYWORD):
            raise ValueError('request parameter must be the last named parameter in function: %s%s' % (fn.__name__, str(sig)))
    return found

## 定义RequestHandler从URL函数中分析其需要接受的参数
class RequestHandler(object):

    def __init__(self, app, fn):
        self._app = app
        self._func = fn
        self._has_request_arg = has_request_arg(fn)
        self._has_var_kw_arg = has_var_kw_arg(fn)
        self._has_named_kw_args = has_named_kw_args(fn)
        self._named_kw_args = get_named_kw_args(fn)
        self._required_kw_args = get_required_kw_args(fn)

    async def __call__(self, request):
        kw = None
        if self._has_var_kw_arg or self._has_named_kw_args or self._required_kw_args:
            if request.method == 'POST':
                if not request.content_type:
                    return web.HTTPBadRequest('Missing Content-Type.')
                ct = request.content_type.lower()
                if ct.startswith('application/json'):
                    params = await request.json()
                    if not isinstance(params, dict):
                        return web.HTTPBadRequest('JSON body must be object.')
                    kw = params
                elif ct.startswith('application/x-www-form-urlencoded') or ct.startswith('multipart/form-data'):
                    params = await request.post()
                    kw = dict(**params)
                else:
                    return web.HTTPBadRequest('Unsupported Content-Type: %s' % request.content_type)
            if request.method == 'GET':
                qs = request.query_string
                if qs:
                    kw = dict()
                    for k, v in parse.parse_qs(qs, True).items():
                        kw[k] = v[0]
        if kw is None:
            kw = dict(**request.match_info)
        else:
            if not self._has_var_kw_arg and self._named_kw_args:
                # remove all unamed kw:
                copy = dict()
                for name in self._named_kw_args:
                    if name in kw:
                        copy[name] = kw[name]
                kw = copy
            # check named arg:
            for k, v in request.match_info.items():
                if k in kw:
                    logging.warning('Duplicate arg name in named arg and kw args: %s' % k)
                kw[k] = v
        if self._has_request_arg:
            kw['request'] = request
        # check required kw:
        if self._required_kw_args:
            for name in self._required_kw_args:
                if not name in kw:
                    return web.HTTPBadRequest('Missing argument: %s' % name)
        logging.info('call with args: %s' % str(kw))
        try:
            r = await self._func(**kw)
            return r
        except APIError as e:
            return dict(error=e.error, data=e.data, message=e.message)
## 定义add_static函数，来注册static文件夹下的文件
def add_static(app):
    path = os.path.join(os.path.dirname(os.path.abspath(__file__)), 'static')
    app.router.add_static('/static/', path)
    logging.info('add static %s => %s' % ('/static/', path))

## 定义add_route函数，来注册一个URL处理函数
def add_route(app, fn):
    method = getattr(fn, '__method__', None)
    path = getattr(fn, '__route__', None)
    if path is None or method is None:
        raise ValueError('@get or @post not defined in %s.' % str(fn))
    if not asyncio.iscoroutinefunction(fn) and not inspect.isgeneratorfunction(fn):
        fn = asyncio.coroutine(fn)
    logging.info('add route %s %s => %s(%s)' % (method, path, fn.__name__, ', '.join(inspect.signature(fn).parameters.keys())))
    app.router.add_route(method, path, RequestHandler(app, fn))

## 定义add_routes函数，自动把handler模块的所有符合条件的URL函数注册了
def add_routes(app, module_name):
    n = module_name.rfind('.')
    if n == (-1):
        mod = __import__(module_name, globals(), locals())
    else:
        name = module_name[n+1:]
        mod = getattr(__import__(module_name[:n], globals(), locals(), [name]), name)
    for attr in dir(mod):
        if attr.startswith('_'):
            continue
        fn = getattr(mod, attr)
        if callable(fn):
            method = getattr(fn, '__method__', None)
            path = getattr(fn, '__route__', None)
            if method and path:
                add_route(app, fn)
~~~

最后，在`app.py`中加入`middleware`、`jinja2`模板和自注册的支持。`app.py`代码修改如下：

~~~ python
import logging; logging.basicConfig(level=logging.INFO)
import asyncio, os, json, time
from datetime import datetime
from aiohttp import web
from jinja2 import Environment, FileSystemLoader

## config 配置代码在后面会创建添加, 可先从github下载到www下,以防报错
from config import configs

import orm
from coroweb import add_routes, add_static

## handlers 是url处理模块在后面会创建编写, 可先从github下载到www下,以防报错
from handlers import cookie2user, COOKIE_NAME

## 初始化jinja2的函数
def init_jinja2(app, **kw):
    logging.info('init jinja2...')
    options = dict(
        autoescape = kw.get('autoescape', True),
        block_start_string = kw.get('block_start_string', '{%'),
        block_end_string = kw.get('block_end_string', '%}'),
        variable_start_string = kw.get('variable_start_string', '{{'),
        variable_end_string = kw.get('variable_end_string', '}}'),
        auto_reload = kw.get('auto_reload', True)
    )
    path = kw.get('path', None)
    if path is None:
        path = os.path.join(os.path.dirname(os.path.abspath(__file__)), 'templates')
    logging.info('set jinja2 template path: %s' % path)
    env = Environment(loader=FileSystemLoader(path), **options)
    filters = kw.get('filters', None)
    if filters is not None:
        for name, f in filters.items():
            env.filters[name] = f
    app['__templating__'] = env

## 以下是middleware,可以把通用的功能从每个URL处理函数中拿出来集中放到一个地方
## URL处理日志工厂
async def logger_factory(app, handler):
    async def logger(request):
        logging.info('Request: %s %s' % (request.method, request.path))
        return (await handler(request))
    return logger

## 认证处理工厂--把当前用户绑定到request上，并对URL/manage/进行拦截，检查当前用户是否是管理员身份
async def auth_factory(app, handler):
    async def auth(request):
        logging.info('check user: %s %s' % (request.method, request.path))
        request.__user__ = None
        cookie_str = request.cookies.get(COOKIE_NAME)
        if cookie_str:
            user = await cookie2user(cookie_str)
            if user:
                logging.info('set current user: %s' % user.email)
                request.__user__ = user
        if request.path.startswith('/manage/') and (request.__user__ is None or not request.__user__.admin):
            return web.HTTPFound('/signin')
        return (await handler(request))
    return auth

## 数据处理工厂
async def data_factory(app, handler):
    async def parse_data(request):
        if request.method == 'POST':
            if request.content_type.startswith('application/json'):
                request.__data__ = await request.json()
                logging.info('request json: %s' % str(request.__data__))
            elif request.content_type.startswith('application/x-www-form-urlencoded'):
                request.__data__ = await request.post()
                logging.info('request form: %s' % str(request.__data__))
        return (await handler(request))
    return parse_data

## 响应返回处理工厂
async def response_factory(app, handler):
    async def response(request):
        logging.info('Response handler...')
        r = await handler(request)
        if isinstance(r, web.StreamResponse):
            return r
        if isinstance(r, bytes):
            resp = web.Response(body=r)
            resp.content_type = 'application/octet-stream'
            return resp
        if isinstance(r, str):
            if r.startswith('redirect:'):
                return web.HTTPFound(r[9:])
            resp = web.Response(body=r.encode('utf-8'))
            resp.content_type = 'text/html;charset=utf-8'
            return resp
        if isinstance(r, dict):
            template = r.get('__template__')
            if template is None:
                resp = web.Response(body=json.dumps(r, ensure_ascii=False, default=lambda o: o.__dict__).encode('utf-8'))
                resp.content_type = 'application/json;charset=utf-8'
                return resp
            else:
                r['__user__'] = request.__user__
                resp = web.Response(body=app['__templating__'].get_template(template).render(**r).encode('utf-8'))
                resp.content_type = 'text/html;charset=utf-8'
                return resp
        if isinstance(r, int) and t >= 100 and t < 600:
            return web.Response(t)
        if isinstance(r, tuple) and len(r) == 2:
            t, m = r
            if isinstance(t, int) and t >= 100 and t < 600:
                return web.Response(t, str(m))
        # default:
        resp = web.Response(body=str(r).encode('utf-8'))
        resp.content_type = 'text/plain;charset=utf-8'
        return resp
    return response

## 时间转换
def datetime_filter(t):
    delta = int(time.time() - t)
    if delta < 60:
        return u'1分钟前'
    if delta < 3600:
        return u'%s分钟前' % (delta // 60)
    if delta < 86400:
        return u'%s小时前' % (delta // 3600)
    if delta < 604800:
        return u'%s天前' % (delta // 86400)
    dt = datetime.fromtimestamp(t)
    return u'%s年%s月%s日' % (dt.year, dt.month, dt.day)

async def init(loop):
    await orm.create_pool(loop=loop, **configs.db)
    app = web.Application(loop=loop, middlewares=[
        logger_factory, auth_factory, response_factory
    ])
    init_jinja2(app, filters=dict(datetime=datetime_filter))
    add_routes(app, 'handlers')
    add_static(app)
    srv = await loop.create_server(app.make_handler(), '127.0.0.1', 9000)
    logging.info('server started at http://127.0.0.1:9000...')
    return srv

loop = asyncio.get_event_loop()
loop.run_until_complete(init(loop))
loop.run_forever()
~~~

有了Web框架，接下来就可以添加需要的URL到`handlers`模块来处理了。



#### 编写配置文件

一个网站应用运行时都需要读取配置文件，一般包括数据库的用户名、口令等。默认的配置文件应该符合本地开发环境，我们把默认的配置文件命名为`config_default.py`:

```python
# config_default.py

configs = {
    'db': {
        'host': '127.0.0.1',
        'port': 3306,
        'user': 'www-data',
        'password': 'www-data',
        'database': 'awesome'
    },
    'session': {
        'secret': 'Awesome'
    }
}
```

当将网站部署到服务器时，通常需要修改数据库的host等信息，直接修改`config_default.py`不是一个好办法，更好的方法是编写一个`config_override.py`，用来覆盖某些默认设置：

```python
# config_override.py

configs = {
    'db': {
        'host': '192.168.0.100'
    }
}
```

应用程序读取配置文件需要优先从`config_override.py`读取。为了简化读取配置文件，可以把所有配置读取到统一的`config.py`中：

```python
# config.py
configs = config_default.configs

try:
    import config_override
    configs = merge(configs, config_override.configs)
except ImportError:
    pass
```

注意：本地测试时不需要编写创建`config_override.py`



#### 编写MVC

MVC模式(Model-View-Controller)是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型(Model)、视图(View)和控制器(Controller)。现在我们的ORM框架、Web框架和配置都已就绪，编写一个简单的MVC，就可以把它们全部启动起来。

通过Web框架的`@get`和ORM框架的Model支持，可以很容易地编写一个处理首页URL的函数(新建handlers.py进行编写)：

```python
@get('/')
def index(request):
    users = await User.findAll()
    return {
        '__template__': 'test.html',
        'users': users
    }
```

`'__template__'`指定的模板文件是`test.html`，其他参数是传递给模板的数据，所以我们在模板的根目录`templates`下创建`test.html`：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Test users - Awesome Python Webapp</title>
</head>
<body>
    <h1>All users</h1>
    {% for u in users %}
    <p>{{ u.name }} / {{ u.email }}</p>
    {% endfor %}
</body>
</html>
```

接下来，如果一切顺利，可以用命令行启动Web服务器：

```
$ python3 app.py
```

然后，在浏览器中访问`http://localhost:9000/`。

如果数据库的`users`表什么内容也没有，你就无法在浏览器中看到循环输出的内容。可以自己在MySQL的命令行里给`users`表添加几条记录，或者使用ORM那一章节里使用过的test.py添加users，然后再访问：



#### 编写API

API就是把Web App的功能全部封装了，所以，通过API操作数据，可以极大地把前端和后端的代码隔离，使得后端代码易于测试，前端代码编写更简单。一个API也是一个URL的处理函数，我们希望能直接通过一个`@api`来把函数变成JSON格式的REST API，这样，获取注册用户可以用一个API实现如下：

```python
@get('/api/users')
def api_get_users(*, page='1'):
    page_index = get_page_index(page)
    num = await User.findNumber('count(id)')
    p = Page(num, page_index)
    if num == 0:
        return dict(page=p, users=())
    users = await User.findAll(orderBy='created_at desc', limit=(p.offset, p.limit))
    for u in users:
        u.passwd = '******'
    return dict(page=p, users=users)
```

只要返回一个`dict`，后续的`response`这个`middleware`就可以把结果序列化为JSON并返回。

以下是所有网站所需要的后端API, 前端页面URL的列表：

后端API包括：

- 获取日志：GET /api/blogs
- 创建日志：POST /api/blogs
- 修改日志：POST /api/blogs/:blog_id
- 删除日志：POST /api/blogs/:blog_id/delete
- 获取评论：GET /api/comments
- 创建评论：POST /api/blogs/:blog_id/comments
- 删除评论：POST /api/comments/:comment_id/delete
- 创建新用户：POST /api/users
- 获取用户：GET /api/users

管理页面包括：

- 评论列表页：GET /manage/comments
- 日志列表页：GET /manage/blogs
- 创建日志页：GET /manage/blogs/create
- 修改日志页：GET /manage/blogs/
- 用户列表页：GET /manage/users

用户浏览页面包括：

- 注册页：GET /register
- 登录页：GET /signin
- 注销页：GET /signout
- 首页：GET /
- 日志详情页：GET /blog/:blog_id

我们将处理这些API和URL的函数统一放在`handlers.py`中：

~~~ python
import re, time, json, logging, hashlib, base64, asyncio

## markdown 是处理日志文本的一种格式语法，具体语法使用请百度
import markdown
from aiohttp import web
from coroweb import get, post

## 分页管理以及调取API时的错误信息
from apis import Page, APIValueError, APIResourceNotFoundError
from models import User, Comment, Blog, next_id
from config import configs

COOKIE_NAME = 'awesession'
_COOKIE_KEY = configs.session.secret

## 查看是否是管理员用户
def check_admin(request):
    if request.__user__ is None or not request.__user__.admin:
        raise APIPermissionError()

## 获取页码信息
def get_page_index(page_str):
    p = 1
    try:
        p = int(page_str)
    except ValueError as e:
        pass
    if p < 1:
        p = 1
    return p

## 计算加密cookie
def user2cookie(user, max_age):
    # build cookie string by: id-expires-sha1
    expires = str(int(time.time() + max_age))
    s = '%s-%s-%s-%s' % (user.id, user.passwd, expires, _COOKIE_KEY)
    L = [user.id, expires, hashlib.sha1(s.encode('utf-8')).hexdigest()]
    return '-'.join(L)

## 文本转HTML
def text2html(text):
    lines = map(lambda s: '<p>%s</p>' % s.replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;'), filter(lambda s: s.strip() != '', text.split('\n')))
    return ''.join(lines)

## 解密cookie
async def cookie2user(cookie_str):
    if not cookie_str:
        return None
    try:
        L = cookie_str.split('-')
        if len(L) != 3:
            return None
        uid, expires, sha1 = L
        if int(expires) < time.time():
            return None
        user = await User.find(uid)
        if user is None:
            return None
        s = '%s-%s-%s-%s' % (uid, user.passwd, expires, _COOKIE_KEY)
        if sha1 != hashlib.sha1(s.encode('utf-8')).hexdigest():
            logging.info('invalid sha1')
            return None
        user.passwd = '******'
        return user
    except Exception as e:
        logging.exception(e)
        return None

## 处理首页URL
@get('/')
def index(*, page='1'):
    page_index = get_page_index(page)
    num = await Blog.findNumber('count(id)')
    p = Page(num, page_index)
    if num == 0:
        blogs = []
    else:
        blogs = await Blog.findAll(orderBy='created_at desc', limit=(p.offset, p.limit))
    return {
        '__template__': 'blogs.html',
        'page': p,
        'blogs': blogs
    }

## 处理日志详情页面URL
@get('/blog/{id}')
def get_blog(id):
    blog = await Blog.find(id)
    comments = await Comment.findAll('blog_id=?', [id], orderBy='created_at desc')
    for c in comments:
        c.html_content = markdown.markdown(c.content)
    blog.html_content = markdown.markdown(blog.content)
    return {
        '__template__': 'blog.html',
        'blog': blog,
        'comments': comments
    }

## 处理注册页面URL
@get('/register')
def register():
    return {
       '__template__': 'register.html'
    }

## 处理登录页面URL
@get('/signin')
def signin():
    return {
        '__template__': 'signin.html'
    }

## 用户登录验证API
@post('/api/authenticate')
def authenticate(*, email, passwd):
    if not email:
        raise APIValueError('email', 'Invalid email.')
    if not passwd:
        raise APIValueError('passwd', 'Invalid password.')
    users = await User.findAll('email=?', [email])
    if len(users) == 0:
        raise APIValueError('email', 'Email not exist.')
    user = users[0]
    # check passwd:
    sha1 = hashlib.sha1()
    sha1.update(user.id.encode('utf-8'))
    sha1.update(b':')
    sha1.update(passwd.encode('utf-8'))
    if user.passwd != sha1.hexdigest():
        raise APIValueError('passwd', 'Invalid password.')
    # authenticate ok, set cookie:
    r = web.Response()
    r.set_cookie(COOKIE_NAME, user2cookie(user, 86400), max_age=86400, httponly=True)
    user.passwd = '******'
    r.content_type = 'application/json'
    r.body = json.dumps(user, ensure_ascii=False).encode('utf-8')
    return r

## 用户注销
@get('/signout')
def signout(request):
    referer = request.headers.get('Referer')
    r = web.HTTPFound(referer or '/')
    r.set_cookie(COOKIE_NAME, '-deleted-', max_age=0, httponly=True)
    logging.info('user signed out.')
    return r

## 获取管理页面
@get('/manage/')
def manage():
    return 'redirect:/manage/comments'

## 评论管理页面
@get('/manage/comments')
def manage_comments(*, page='1'):
    return {
        '__template__': 'manage_comments.html',
        'page_index': get_page_index(page)
    }

## 日志管理页面
@get('/manage/blogs')
def manage_blogs(*, page='1'):
    return {
        '__template__': 'manage_blogs.html',
        'page_index': get_page_index(page)
    }

## 创建日志页面
@get('/manage/blogs/create')
def manage_create_blog():
    return {
        '__template__': 'manage_blog_edit.html',
        'id': '',
        'action': '/api/blogs'
    }

## 编辑日志页面
@get('/manage/blogs/edit')
def manage_edit_blog(*, id):
    return {
        '__template__': 'manage_blog_edit.html',
        'id': id,
        'action': '/api/blogs/%s' % id
    }

## 用户管理页面
@get('/manage/users')
def manage_users(*, page='1'):
    return {
        '__template__': 'manage_users.html',
        'page_index': get_page_index(page)
    }

## 获取评论信息API
@get('/api/comments')
def api_comments(*, page='1'):
    page_index = get_page_index(page)
    num = await Comment.findNumber('count(id)')
    p = Page(num, page_index)
    if num == 0:
        return dict(page=p, comments=())
    comments = await Comment.findAll(orderBy='created_at desc', limit=(p.offset, p.limit))
    return dict(page=p, comments=comments)

## 用户发表评论API
@post('/api/blogs/{id}/comments')
def api_create_comment(id, request, *, content):
    user = request.__user__
    if user is None:
        raise APIPermissionError('Please signin first.')
    if not content or not content.strip():
        raise APIValueError('content')
    blog = await Blog.find(id)
    if blog is None:
        raise APIResourceNotFoundError('Blog')
    comment = Comment(blog_id=blog.id, user_id=user.id, user_name=user.name, user_image=user.image, content=content.strip())
    await comment.save()
    return comment

## 管理员删除评论API
@post('/api/comments/{id}/delete')
def api_delete_comments(id, request):
    check_admin(request)
    c = await Comment.find(id)
    if c is None:
        raise APIResourceNotFoundError('Comment')
    await c.remove()
    return dict(id=id)

## 获取用户信息API
@get('/api/users')
def api_get_users(*, page='1'):
    page_index = get_page_index(page)
    num = await User.findNumber('count(id)')
    p = Page(num, page_index)
    if num == 0:
        return dict(page=p, users=())
    users = await User.findAll(orderBy='created_at desc', limit=(p.offset, p.limit))
    for u in users:
        u.passwd = '******'
    return dict(page=p, users=users)

## 定义EMAIL和HASH的格式规范
_RE_EMAIL = re.compile(r'^[a-z0-9\.\-\_]+\@[a-z0-9\-\_]+(\.[a-z0-9\-\_]+){1,4}$')
_RE_SHA1 = re.compile(r'^[0-9a-f]{40}$')

## 用户注册API
@post('/api/users')
def api_register_user(*, email, name, passwd):
    if not name or not name.strip():
        raise APIValueError('name')
    if not email or not _RE_EMAIL.match(email):
        raise APIValueError('email')
    if not passwd or not _RE_SHA1.match(passwd):
        raise APIValueError('passwd')
    users = await User.findAll('email=?', [email])
    if len(users) > 0:
        raise APIError('register:failed', 'email', 'Email is already in use.')
    uid = next_id()
    sha1_passwd = '%s:%s' % (uid, passwd)
    user = User(id=uid, name=name.strip(), email=email, passwd=hashlib.sha1(sha1_passwd.encode('utf-8')).hexdigest(), image='http://www.gravatar.com/avatar/%s?d=mm&s=120' % hashlib.md5(email.encode('utf-8')).hexdigest())
    await user.save()
    # make session cookie:
    r = web.Response()
    r.set_cookie(COOKIE_NAME, user2cookie(user, 86400), max_age=86400, httponly=True)
    user.passwd = '******'
    r.content_type = 'application/json'
    r.body = json.dumps(user, ensure_ascii=False).encode('utf-8')
    return r

## 获取日志列表API
@get('/api/blogs')
def api_blogs(*, page='1'):
    page_index = get_page_index(page)
    num = await Blog.findNumber('count(id)')
    p = Page(num, page_index)
    if num == 0:
        return dict(page=p, blogs=())
    blogs = await Blog.findAll(orderBy='created_at desc', limit=(p.offset, p.limit))
    return dict(page=p, blogs=blogs)

## 获取日志详情API
@get('/api/blogs/{id}')
def api_get_blog(*, id):
    blog = await Blog.find(id)
    return blog

## 发表日志API
@post('/api/blogs')
def api_create_blog(request, *, name, summary, content, tag):
    check_admin(request)
    if not name or not name.strip():
        raise APIValueError('name', 'name cannot be empty.')
    if not summary or not summary.strip():
        raise APIValueError('summary', 'summary cannot be empty.')
    if not content or not content.strip():
        raise APIValueError('content', 'content cannot be empty.')
    if not tag or not tag.strip():
        raise APIValueError('tag', 'tag cannot be empty.')
    blog = Blog(user_id=request.__user__.id, user_name=request.__user__.name, user_image=request.__user__.image, name=name.strip(), summary=summary.strip(), content=content.strip(), tag=tag.strip())
    await blog.save()
    return blog

## 编辑日志API
@post('/api/blogs/{id}')
def api_update_blog(id, request, *, name, summary, content, tag):
    check_admin(request)
    blog = await Blog.find(id)
    if not name or not name.strip():
        raise APIValueError('name', 'name cannot be empty.')
    if not summary or not summary.strip():
        raise APIValueError('summary', 'summary cannot be empty.')
    if not content or not content.strip():
        raise APIValueError('content', 'content cannot be empty.')
    if not tag or not tag.strip():
        raise APIValueError('tag', 'tag cannot be empty.')
    blog.name = name.strip()
    blog.summary = summary.strip()
    blog.content = content.strip()
    blog.tag = tag.strip()
    await blog.update()
    return blog

## 删除日志API
@post('/api/blogs/{id}/delete')
def api_delete_blog(request, *, id):
    check_admin(request)
    blog = await Blog.find(id)
    await blog.remove()
    return dict(id=id)

## 删除用户API
@post('/api/users/{id}/delete')
async def api_delete_users(id, request):
    check_admin(request)
    id_buff = id
    user = await User.find(id)
    if user is None:
        raise APIResourceNotFoundError('Comment')
    await user.remove()
    # 给被删除的用户在评论中标记
    comments = await Comment.findAll('user_id=?',[id])
    if comments:
        for comment in comments:
            id = comment.id
            c = await Comment.find(id)
            c.user_name = c.user_name + ' (该用户已被删除)'
            await c.update()
    id = id_buff
    return dict(id=id)

~~~



#### 构建前端

至此，后端工作基本构建完成。接下要开始设计和编程前端页面了。为了更容易构建出复杂的HTML前端页面，我们需要一套基础的CSS框架和jQuery作为操作DOM的JavaScript库。

如今好用流行的CSS框架有很多例如：[Bootstrap](https://getbootstrap.com/), [Pure CSS](https://purecss.io/), [Bulma](https://bulma.io/), [Semantic UI](https://semantic-ui.com/) 等。此教程会使用[UIkit](https://getuikit.com/) 作为网站的CSS 框架，具体的教程请参考[官方Documentation](https://getuikit.com/docs/introduction)。请下载UIkit的[最新版本](https://getuikit.com/)(3.0.0或以上，廖大用的UIkit 2.0 将不适用)，将js和css文件放入`www/static`相应的文件目录中：

```python
static/
+- css/
|  +- awesome.css ## 自定义的css
|  +- uikit-rtl.min.css
|  +- uikit.min.css
+- js/
   +- awesome.js ## 自定义的script
   +- jquery.min.js
   +- uikit.min.js
   +- uikit-icons.min.js
   +- sha1.min.js ## 计算HASH值
   +- vue.min.js ## Vue是一种MVVM前端框架, 我们会用它将数据及数据操作与HTML页面显示联系起来
   +- sticky.min.js ## 实现黏性布局
```

由于前端包含很多页面，而每个页面都会有相同的页眉和页脚，最有效率的方法就是构建可以继承的`父模板`, **jinja2**提供了很便捷方法用可替换的`block`来建立模板。例如最简单的父模板`base.html`：

```html
<!-- base.html -->
<html>
    <head>
        <title>{% block title%} 子模板可替换title内容 {% endblock %}</title>
    </head>
    <body>
        {% block content %} 子模板可替换content内容 {% endblock %}
    </body>
</html>
```

对于子模板`a.html`, `b.html`, `c.html`，只需要把父模板的`title`和`content`替换掉：

```
{% extends 'base.html' %}

{% block title %} A or B or C {% endblock %}

{% block content %}
    <h1>Chapter A or B or C</h1>
    <p>blablabla...</p>
{% endblock %}
```

知道了如何用`jinja2`, 我们就可以用`UIkit`框架来完成网站父模板`__base__.html`的编写了（请将所有的html文件存放在`www/templates`目录下）：

~~~ html
<!DOCTYPE html>

<!--处理分页导航栏代码-->
{% macro pagination(page) %}
    <ul class="uk-pagination uk-flex-center uk-margin-medium-top uk-margin-large-bottom">
        {% if page.has_previous %}
            <li><a href="?page={{ page.page_index - 1 }}"><span uk-pagination-previous></span></a></li>
        {% else %}
            <li class="uk-disabled"><a href="#"><span uk-pagination-previous></span></a></li>
        {% endif %}
            <li class="uk-active"><span>{{ page.page_index }}</span></li>
        {% if page.has_next %}
            <li><a href="?page={{ page.page_index + 1 }}"><span uk-pagination-next></span></a></li>
        {% else %}
            <li class="uk-disabled"><a href="#"><span uk-pagination-next></span></a></li>
        {% endif %}
    </ul>
{% endmacro %}

<!--导航页代码-->
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="HandheldFriendly" content="True">
    <meta name="viewport" content="width=device-width,initial-scale=0.9,minimum-scale=0.9,maximum-scale=0.9,user-scalable=no">
    <meta name="wap-font-scale" content="no">
    <!--jinja2 meta块-->
    {% block meta %}<!-- block meta  -->{% endblock %}
    <!--jinja2 title块-->
    <title>{% block title %} ? {% endblock %}| AwesomeDavidBlog</title>
    <link rel="stylesheet" href="/static/css/uikit.min.css">
    <link rel="stylesheet" href="/static/css/uikit-rtl.min.css">
    <link rel="stylesheet" href="/static/css/awesome.css" />
    <script src="/static/js/jquery.min.js"></script>
    <script src="/static/js/sha1.min.js"></script>
    <script src="/static/js/uikit.min.js"></script>
    <script src="/static/js/icons.min.js"></script>
    <script src="/static/js/sticky.min.js"></script>
    <script src="/static/js/vue.min.js"></script>
    <script src="/static/js/awesome.js"></script>
    <!--jinja2 beforehead块-->
    {% block beforehead %}<!-- before head  -->{% endblock %}
</head>

<!--导航页正文内容-->
<body>
    <!--"uk-"开头的都是UIkit里的组件，具体请参考UIkit官网的Documents详解-->
    <!--uk-visible@m是大于中等尺寸屏幕时显示的UI-->
    <div class="uk-margin uk-visible@m" style="background-color:rgba(100,150,185,0);">
    <div class="uk-container uk-container-medium">
    <!--导航栏UI-->
    <nav class="uk-navbar-container" uk-navbar style="background-color:rgba(255,255,255,0);">
        <div class="uk-navbar-left uk-margin-medium-top uk-margin-medium-bottom">
            <a class="uk-navbar-item uk-logo uk-margin-left" href="/">
                <!--此处uk-icon为包含自定义图标再打包的uk-icon.js,读者可以选UIkit自带icon,也可以添加自定义icon重新打包uk-icon.js-->
                <span class="uk-icon uk-margin-small-right" uk-icon="lemon4" ratio="2"></span>
                 凹大卜  
            </a>
            <ul class="uk-navbar-nav">
                <li><a href="/"> Article | 日志</a></li>
                <li><a href="https://aodabo.tech/tags/tutorial"> Tutorial | 教程</a></li>
                <li><a href="https://aodabo.tech/tags/landscape"> Landscape | 景观</a></li>
                <li><a href="https://aodabo.tech/tags/coding"> Coding | 编程</a></li>
            </ul>
        </div>
        <div class="uk-navbar-right uk-margin-medium-top uk-margin-medium-bottom">
            <ul class="uk-navbar-nav">
            {% if __user__ %}
                <li>
                    <a href="#0"> {{ __user__.name }}</a>
                    <div class="uk-navbar-dropdown">
                        <ul class="uk-nav uk-navbar-dropdown-nav">
                            <li><a href="/signout"> Logout</a></li>
                        </ul>
                    </div>
                </li>
            {% else %}
                <li><a href="/signin"> Login</a></li>
                <li><a href="/register"> Register</a></li>
            {% endif %}
            </ul>
        </div>
    </nav>
    </div>
    </div>

    <!--uk-hidden@m是小于中等尺寸屏幕时显示的UI-->
    <nav class="uk-navbar-container uk-margin-medium uk-hidden@m" uk-navbar style="background-color:rgba(255,255,255,0);">
        <div class="uk-navbar-left">
            <a class="uk-navbar-item uk-logo" href="/">
                <span class="uk-icon uk-margin-small-right" uk-icon="lemon4" ratio="2"></span>
                 凹大卜  
            </a>
        </div>
        <div class="uk-navbar-right">
            <ul class="uk-navbar-nav">
            <li>
            <a class="uk-navbar-toggle" uk-toggle="target: #offcanvas-nav" uk-navbar-toggle-icon></a>

            <div id="offcanvas-nav" uk-offcanvas="overlay: true; flip: true">
            <div class="uk-offcanvas-bar uk-flex uk-flex-column">
            <ul class="uk-nav uk-nav-default uk-margin-auto-vertical">
                <li><a href="/"> Article | 日志</a></li>
                <li><a href="https://aodabo.tech/tags/tutorial"> Tutorial | 教程</a></li>
                <li><a href="https://aodabo.tech/tags/landscape"> Landscape | 景观</a></li>
                <li><a href="https://aodabo.tech/tags/tutorial"> Coding | 编程</a></li>
                {% if __user__ %}
                <li><a href="/signout"> Logout | 注销</a></li>
                {% else %}
                <li><a href="/signin"> Login | 登陆</a></li>
                <li><a href="/register"> Register | 注册</a></li>
                {% endif %}
            </ul>
            </div>
            </div>
            </li>
            </ul>
        </div>
    </nav>


    <div class="uk-container uk-container-medium">
            <!-- jinja2 content块 -->
            {% block content %}
            {% endblock %}
    </div>

    <!-- 页面底部图标栏和网站信息 -->
    <div class="uk-margin-medium">
    <div class="uk-container uk-container-center uk-text-center">
        <p>
            <a target="_blank" href="https://github.com/yzyly1992" class="uk-icon-button uk-margin-small-right" ratio="1.1" uk-icon="github"></a>
            <a target="_blank" href="#" class="uk-icon-button uk-margin-small-right" ratio="1.1" uk-icon="instagram"></a>
            <a target="_blank" href="#" class="uk-icon-button uk-margin-small-right" ratio="1.2" uk-icon="netease"></a>
            <a target="_blank" href="#" class="uk-icon-button uk-margin-small-right" ratio="1.2" uk-icon="weibo"></a>
            <a target="_blank" href="#" class="uk-icon-button uk-margin-small-right" ratio="1.1" uk-icon="linkedin"></a>
        </p>
        <p class="uk-text-meta" style="line-height: 10px; padding: 10px 0; margin: 8px 0;">Powered by <a href="https://aodabo.tech">AwesomeDavidBlog!</a> Copyright &copy; 2018.</p>
        <p class="uk-text-meta" style="line-height: 0px; padding: 0px 0; margin: 0px 0;"><a href="https://aodabo.tech/" target="_blank">Zhiyuan Yang</a>. All rights reserved.</p>
    </div>
    </div>

</body>
</html>

~~~



#### 用户注册和登录页面

有了父模板，有了用户注册和登录验证的API，构建注册和登录页面就十分容易了。首先我们来构建用户注册页面`register.html`(在路径`www/templates`下):

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}Register/注册{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交合格的注册信息数据-->
<script>

function validateEmail(email) {
    var re = /^[a-z0-9\.\-\_]+\@[a-z0-9\-\_]+(\.[a-z0-9\-\_]+){1,4}$/;
    return re.test(email.toLowerCase());
}

$(function () {
    var vm = new Vue({
        el: '#vm',
        data: {
            name: '',
            email: '',
            password1: '',
            password2: ''
        },
        methods: {
            submit: function (event) {
                event.preventDefault();
                var $form = $('#vm');
                if (! this.name.trim()) {
                    return $form.showFormError('NAME/请输入名字');
                }
                if (! validateEmail(this.email.trim().toLowerCase())) {
                    return $form.showFormError('CORRECT EMAIL/请输入正确的Email地址');
                }
                if (this.password1.length < 6) {
                    return $form.showFormError('PASSWORD AT LEAST 6 CHAR/口令长度至少为6个字符');
                }
                if (this.password1 !== this.password2) {
                    return $form.showFormError('PASSWORD INCONSIST/两次输入的口令不一致');
                }
                var email = this.email.trim().toLowerCase();
                $form.postJSON('/api/users', {
                    name: this.name.trim(),
                    email: email,
                    passwd: CryptoJS.SHA1(email + ':' + this.password1).toString()
                }, function (err, r) {
                    if (err) {
                        return $form.showFormError(err);
                    }
                    return location.assign('/');
                });
            }
        }
    });
    $('#vm').show();
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换，构建注册页面UI主要内容-->
{% block content %}
    <div class="uk-grid">
    <div class="uk-width-1-1">
        <h4>REGISTER/欢迎注册！</h4>
        <form id="vm" v-on="submit: submit" class="uk-form-stacked">
            <div class="uk-alert uk-alert-danger uk-hidden"></div>
            <div class="uk-margin-top">
                <label class="uk-form-label">NAME/名字:</label>
                <div class="uk-form-controls">
                    <input class="uk-input uk-form-width-medium" v-model="name" type="text" maxlength="50" placeholder="名字">
                </div>
            </div>
            <div class="uk-margin-top">
                <label class="uk-form-label">EMAIL/电子邮件:</label>
                <div class="uk-form-controls">
                    <input class="uk-input uk-form-width-medium" v-model="email" type="text" maxlength="50" placeholder="your-name@example.com">
                </div>
            </div>
            <div class="uk-margin-top">
                <label class="uk-form-label">PASSWORD/输入口令:</label>
                <div class="uk-form-controls">
                    <input class="uk-input uk-form-width-medium" v-model="password1" type="password" maxlength="50" placeholder="输入口令">
                </div>
            </div>
            <div class="uk-margin">
                <label class="uk-form-label">REPEAT PASSWORD/重复口令:</label>
                <div class="uk-form-controls">
                    <input class="uk-input uk-form-width-medium" v-model="password2" type="password" maxlength="50" placeholder="重复口令">
                </div>
            </div>
            <div class="uk-margin-large">
                <button type="submit" class="uk-button uk-button-primary"><i class="uk-icon-user"></i>REGISTER/注册</button>
            </div>
        </form>
    </div>
    </div>

{% endblock %}

~~~

登录页面更为简单，在`www/templates`下编写`signin.html`:

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}Signin/登陆{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交登录验证信息数据-->
<script>

$(function() {
    var vmAuth = new Vue({
        el: '#vm',
        data: {
            email: '',
            passwd: ''
        },
        methods: {
            submit: function(event) {
                event.preventDefault();
                var $form = $('#vm');
                var email = this.email.trim().toLowerCase();
                var data = {
                        email: email,
                        passwd: this.passwd==='' ? '' : CryptoJS.SHA1(email + ':' + this.passwd).toString()
                    };
                $form.postJSON('/api/authenticate', data, function(err, result) {
                    if (! err) {
                        location.assign('/');
                    }
                });
            }
        }
    });
    $('#vm').show();
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换，构建登录页面UI主要内容-->
{% block content %}
    <div class="uk-grid">
    <div class="uk-width-1-1">
        <h4>SIGNIN/欢迎登陆！</h4>
        <form id="vm" v-on="submit: submit" class="uk-form-stacked">
            <div class="uk-alert uk-alert-danger uk-hidden"></div>
            <div class="uk-margin-top">
                <label class="uk-form-label">EMAIL/电子邮箱:</label>
                <div class="uk-inline">
                    <span class="uk-form-icon" uk-icon="user"></span>
                    <input class="uk-input uk-form-width-medium" v-model="email" type="text" maxlength="50" placeholder="Email">
                </div>
            </div>
            <div class="uk-margin-top">
                <label class="uk-form-label">PASSWORD/输入口令:</label>
                <div class="uk-inline">
                    <span class="uk-form-icon uk-form-icon-flip" uk-icon="lock"></span>
                    <input class="uk-input uk-form-width-medium" v-model="passwd" type="password" maxlength="50" placeholder="口令">
                </div>
            </div>
            <div class="uk-margin-top">
                <button type="submit" class="uk-button uk-button-primary">SIGNIN/登陆</button>
            </div>
        </form>
    </div>
    </div>

{% endblock %}

~~~



#### 日志创建页面

和创建注册页面类似，日志创建页面的编写也并不复杂。在`www/templates`下创建`manage_blog_edit.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}编辑日志{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交日志相关数据,包括创建新日志和修改旧日志-->
<script>

var
    ID = '{{ id }}',
    action = '{{ action }}';

function initVM(blog) {
    var vm = new Vue({
        el: '#vm',
        data: blog,
        methods: {
            submit: function (event) {
                event.preventDefault();
                var $form = $('#vm').find('form');
                $form.postJSON(action, this.$data, function (err, r) {
                    if (err) {
                        $form.showFormError(err);
                    }
                    else {
                        return location.assign('/manage/blogs');
                    }
                });
            }
        }
    });
    $('#vm').show();
}

$(function () {
    if (ID) {
        getJSON('/api/blogs/' + ID, function (err, blog) {
            if (err) {
                return fatal(err);
            }
            $('#loading').hide();
            initVM(blog);
        });
    }
    else {
        $('#loading').hide();
        initVM({
            name: '',
            summary: '',
            content: ''
        });
    }
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换，构建日志编写页面UI主要内容-->
{% block content %}
    <div class="uk-grid">
    <div class="uk-width-1-1 uk-margin-bottom">
        <ul class="uk-breadcrumb">
            <li><a href="/manage/comments">评论</a></li>
            <li><a href="/manage/blogs">日志</a></li>
            <li><a href="/manage/users">用户</a></li>
        </ul>
    </div>

    <div id="error" class="uk-width-1-1">
    </div>

    <div id="loading" class="uk-width-1-1 uk-text-center">
        <span><i class="uk-icon-spinner uk-icon-medium uk-icon-spin"></i> 正在加载...</span>
    </div>

    <div id="vm" class="uk-width-2-3">
        <form v-on="submit: submit" class="uk-form-stacked">
            <div class="uk-alert uk-alert-danger uk-hidden"></div>
            <div class="uk-margin-top">
                <label class="uk-form-label">标题:</label>
                <div class="uk-form-controls">
                    <input v-model="name" name="name" type="text" placeholder="标题" class="uk-input uk-form-width-large">
                </div>
            </div>
            <div class="uk-margin-top">
                <label class="uk-form-label">摘要:</label>
                <div class="uk-form-controls">
                    <textarea v-model="summary" rows="4" name="summary" placeholder="摘要" class="uk-textarea" style="resize:none;"></textarea>
                </div>
            </div>
            <div class="uk-margin-top">
                <label class="uk-form-label">内容:</label>
                <div class="uk-form-controls">
                    <textarea v-model="content" rows="12" name="content" placeholder="内容" class="uk-textarea" style="resize:none;"></textarea>
                </div>
            </div>
            <div class="uk-margin-top">
                <button type="submit" class="uk-button uk-button-primary"><i class="uk-icon-save"></i> 保存</button>
                <a href="/manage/blogs" class="uk-button"><i class="uk-icon-times"></i> 取消</a>
            </div>
        </form>
    </div>
    </div>

{% endblock %}

~~~

此模板除了用于创建新的日志内容，也是编辑修改现有日志的模板，只是编辑修改时会预先载入先前的日志内容在输入栏中。



#### 日志列表页面

和以上其他页面的创建方法类似，在`www/templates`下创建`blogs.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}凹大卜{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}

{% endblock %}

<!--jinja2 content 块内容替换-->
{% block content %}
	<!--uk-visible@m是大于中等尺寸屏幕时显示的UI-->
	<!--日志列表内容-->
    <div class="uk-grid  uk-visible@m">
    <div class="uk-width-3-4">
    {% for blog in blogs %}
        <article class="uk-article">
            <h3><a href="/blog/{{ blog.id }}">{{ blog.name }}</a></h3>
            <p class="uk-article-meta">发表于{{ blog.created_at|datetime }}</p>
            <p>{{ blog.summary }}</p>
            <p><a href="/blog/{{ blog.id }}">继续阅读 <i class="uk-icon-angle-double-right"></i></a></p>
        </article>
        <hr>
    {% endfor %}
    <!--分页导航栏，在父模板的开头定义过-->
    {{ pagination(page) }}
    </div>

    <!--uk-visible@m是大于中等尺寸屏幕时显示的UI-->
    <!--右边侧导航栏-->
    <div class="uk-width-1-4 uk-visible@m">
            <h4>站内搜索</h4>
            <FORM method=GET action="https://www.google.com/search">
                <div class="uk-inline">
                    <a class="uk-form-icon uk-form-icon-flip" href="#" uk-icon="search"></a>
                    <INPUT class="uk-input" TYPE=text name=q placeholder="站内搜索" size=31 maxlength=255 value="">
                </div>
                <font size=-1>
                    <INPUT TYPE=hidden name=domains value="aodabo.tech"><br>
                    <INPUT TYPE=hidden name=sitesearch value="aodabo.tech"></br>
                </font>
                <button TYPE=submit name=btnG class="uk-button uk-button-default"> SEARCH</button>

            </FORM>


            <h4>亲密链接</h4>
            <ul class="uk-list uk-list-divider">
                    <li><a target="_blank" href="http://www.davidzyang.com/">Secret/镜像</a></li>
                    <li><a target="_blank" href="http://lilidai-landscape.com/">Lilidai/景观</a></li>
                    <li><a target="_blank" href="https://www.jiumodiary.com/">Reading/读书</a></li>
            </ul>
    </div>
    </div>

    <!--uk-hidden@m是小于中等尺寸屏幕时显示的UI-->
    <!--移动屏幕时日志列表排版-->
    <div class="uk-hidden@m">
    {% for blog in blogs %}
        <article class="uk-article">
            <h5><a href="/blog/{{ blog.id }}">{{ blog.name }}</a></h5>
            <p class="uk-article-meta">发表于{{ blog.created_at|datetime }}</p>
            <p>{{ blog.summary }}</p>
            <p><a href="/blog/{{ blog.id }}">继续阅读 <i class="uk-icon-angle-double-right"></i></a></p>
        </article>
        <hr>
    {% endfor %}
    <!--分页导航栏，在父模板的开头定义过-->
    {{ pagination(page) }}
    </div>


{% endblock %}

~~~



#### 日志内容详情页面

和以上其他页面的创建方法类似，在`www/templates`下创建`blog.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}{{ blog.name }}{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交日志评论相关数据-->
<script>

var comment_url = '/api/blogs/{{ blog.id }}/comments';

$(function () {
    var $form = $('#form-comment');
    $form.submit(function (e) {
        e.preventDefault();
        $form.showFormError('');
        var content = $form.find('textarea').val().trim();
        if (content==='') {
            return $form.showFormError('请输入评论内容！');
        }
        $form.postJSON(comment_url, { content: content }, function (err, result) {
            if (err) {
                return $form.showFormError(err);
            }
            refresh();
        });
    });
});
</script>

{% endblock %}

<!--jinja2 content 块内容替换-->
{% block content %}
    <div class="uk-grid  uk-visible@m">
    <div class="uk-width-3-4">
        <!--日志内容详情-->
        <article class="uk-article">
            <h2>{{ blog.name }}</h2>
            <p class="uk-article-meta">发表于{{ blog.created_at|datetime }}</p>
            <p>{{ blog.html_content|safe }}</p>
        </article>

        <hr>

    <!--日志评论区-->
    {% if __user__ %}
        <h3>发表评论</h3>

        <article class="uk-comment">
            <header class="uk-comment-header">
                <img class="uk-comment-avatar uk-border-circle" width="50" height="50" src="{{ __user__.image }}">
                <h4 class="uk-comment-title">{{ __user__.name }}</h4>
            </header>
            <div class="uk-comment-body">
                <form id="form-comment" class="uk-form">
                    <fieldset class="uk-fieldset">
                        <div class="uk-alert uk-alert-danger uk-hidden"></div>
                        <div class="uk-margin">
                            <textarea class="uk-textarea" rows="6" placeholder="说点什么吧"></textarea>
                        </div>
                        <div class="uk-margin">
                            <button type="submit" class="uk-button uk-button-primary"> 发表评论</button>
                        </div>
                    </fieldset>
                </form>
            </div>
        </article>

        <hr>
    {% endif %}

        <h3>最新评论</h3>

        <ul class="uk-comment-list">
            {% for comment in comments %}
            <li>
                <article class="uk-comment">
                    <header class="uk-comment-header">
                        <img class="uk-comment-avatar uk-border-circle" width="50" height="50" src="{{ comment.user_image }}">
                        <h4 class="uk-comment-title">{{ comment.user_name }} {% if comment.user_id==blog.user_id %}(作者){% endif %}</h4>
                        <p class="uk-comment-meta">{{ comment.created_at|datetime }}</p>
                    </header>
                    <div class="uk-comment-body">
                        {{ comment.html_content|safe }}
                    </div>
                </article>
            </li>
            {% else %}
            <p>还没有人评论...</p>
            {% endfor %}
        </ul>

    </div>

    <div class="uk-width-1-4 uk-visible@m">
        <div class="uk-card uk-card-default">
            <div class="uk-card-body">
            <div class="uk-text-center">
                <img class="uk-border-circle" width="120" height="120" src="{{ blog.user_image }}">
                <h4>{{ blog.user_name }}</h4>
            </div>
            </div>
        </div>
    </div>
    </div>

    <div class="uk-hidden@m">
        <article class="uk-article">
            <h3>{{ blog.name }}</h3>
            <p class="uk-article-meta">{{ blog.user_name }} 发表于{{ blog.created_at|datetime }}</p>
            <p>{{ blog.html_content|safe }}</p>
        </article>

        <hr>

    {% if __user__ %}
        <h4>发表评论</h4>

        <article class="uk-comment">
            <header class="uk-comment-header">
                <img class="uk-comment-avatar uk-border-circle" width="50" height="50" src="{{ __user__.image }}">
                <h5 class="uk-comment-title">{{ __user__.name }}</h5>
            </header>
            <div class="uk-comment-body">
                <form id="form-comment" class="uk-form">
                    <fieldset class="uk-fieldset">
                        <div class="uk-alert uk-alert-danger uk-hidden"></div>
                        <div class="uk-margin">
                            <textarea class="uk-textarea" rows="6" placeholder="说点什么吧"></textarea>
                        </div>
                        <div class="uk-margin">
                            <button type="submit" class="uk-button uk-button-primary"> 发表评论</button>
                        </div>
                    </fieldset>
                </form>
            </div>
        </article>

        <hr>
    {% endif %}

        <h4>最新评论</h4>

        <ul class="uk-comment-list">
            {% for comment in comments %}
            <li>
                <article class="uk-comment">
                    <header class="uk-comment-header">
                        <img class="uk-comment-avatar uk-border-circle" width="50" height="50" src="{{ comment.user_image }}">
                        <h5 class="uk-comment-title">{{ comment.user_name }} {% if comment.user_id==blog.user_id %}(作者){% endif %}</h5>
                        <p class="uk-comment-meta">{{ comment.created_at|datetime }}</p>
                    </header>
                    <div class="uk-comment-body">
                        {{ comment.html_content|safe }}
                    </div>
                </article>
            </li>
            {% else %}
            <p>还没有人评论...</p>
            {% endfor %}
        </ul>

    </div>

{% endblock %}

~~~



#### 管理页面

管理页面模板共有三个，分别为日志、评论、用户，与以上页面创建方式类似。如日志的管理页面，在`www/templates`下创建`manage_blogs.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}日志{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交日志管理操作相关数据-->
<script>

function initVM(data) {
    var vm = new Vue({
        el: '#vm',
        data: {
            blogs: data.blogs,
            page: data.page
        },
        methods: {
            previous: function () {
                gotoPage(this.page.page_index - 1);
            },
            next: function () {
                gotoPage(this.page.page_index + 1);
            },
            edit_blog: function (blog) {
                location.assign('/manage/blogs/edit?id=' + blog.id);
            },
            delete_blog: function (blog) {
                if (confirm('确认要删除“' + blog.name + '”？删除后不可恢复！')) {
                    postJSON('/api/blogs/' + blog.id + '/delete', function (err, r) {
                        if (err) {
                            return alert(err.message || err.error || err);
                        }
                        refresh();
                    });
                }
            }
        }
    });
    $('#vm').show();
}

$(function() {
    getJSON('/api/blogs', {
        page: {{ page_index }}
    }, function (err, results) {
        if (err) {
            return fatal(err);
        }
        $('#loading').hide();
        initVM(results);
    });
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换-->
{% block content %}
    <div class="uk-grid">
    <div class="uk-width-1-1 uk-margin-bottom">
        <ul class="uk-breadcrumb">
            <li><a href="/manage/comments">评论</a></li>
            <li class="uk-active"><span>日志</span></li>
            <li><a href="/manage/users">用户</a></li>
        </ul>
    </div>

    <div id="error" class="uk-width-1-1">
    </div>

    <div id="loading" class="uk-width-1-1 uk-text-center">
        <span><i class="uk-icon-spinner uk-icon-medium uk-icon-spin"></i> 正在加载...</span>
    </div>

    <div id="vm" class="uk-width-1-1">
        <a href="/manage/blogs/create" class="uk-button uk-button-primary"><i class="uk-icon-plus"></i> 新日志</a>

        <table class="uk-table uk-table-divider">
            <thead>
                <tr>
                    <th class="uk-table-expand uk-text-left"> 标题</th>
                    <th class="uk-text-left">作者</th>
                    <th class="uk-text-left">标签</th>
                    <th class="uk-text-left">创建时间</th>
                    <th class="uk-text-left">操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-repeat="blog: blogs" >
                    <td>
                        <a target="_blank" v-attr="href: '/blog/'+blog.id" v-text="blog.name"></a>
                    </td>
                    <td>
                        <a target="_blank" v-attr="href: '/user/'+blog.user_id" v-text="blog.user_name"></a>
                    </td>
                    <td>
                        <a target="_blank" v-attr="href: '/blogs/'+blog.tag" v-text="blog.tag"></a>
                    </td>
                    <td>
                        <span v-text="blog.created_at.toDateTime()"></span>
                    </td>
                    <td>
                        <a href="#0" v-on="click: edit_blog(blog)">编辑</a>
                        <a href="#0" v-on="click: delete_blog(blog)">删除</a>
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="uk-width-1-1 uk-text-center">
        <ul class="uk-pagination">
            <li v-if="! page.has_previous" class="uk-disabled"><span><i uk-icon="chevron-left"></i></span></li>
            <li v-if="page.has_previous"><a v-on="click: previous()" href="#0"><i uk-icon="chevron-left"></i></a></li>
            <li class="uk-active"><span v-text="page.page_index"></span></li>
            <li v-if="! page.has_next" class="uk-disabled"><span><i uk-icon="chevron-right"></i></span></li>
            <li v-if="page.has_next"><a v-on="click: next()" href="#0"><i uk-icon="chevron-right"></i></a></li>
        </ul>
        </div>
    </div>
    </div>

{% endblock %}

~~~



评论的管理页面，在`www/templates`下创建`manage_comments.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}评论{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交评论管理操作相关数据-->
<script>

function initVM(data) {
    $('#vm').show();
    var vm = new Vue({
        el: '#vm',
        data: {
            comments: data.comments,
            page: data.page
        },
        methods: {
            previous: function () {
                gotoPage(this.page.page_index - 1);
            },
            next: function () {
                gotoPage(this.page.page_index + 1);
            },
            delete_comment: function (comment) {
                var content = comment.content.length > 20 ? comment.content.substring(0, 20) + '...' : comment.content;
                if (confirm('确认要删除评论“' + comment.content + '”？删除后不可恢复！')) {
                    postJSON('/api/comments/' + comment.id + '/delete', function (err, r) {
                        if (err) {
                            return error(err);
                        }
                        refresh();
                    });
                }
            }
        }
    });
}

$(function() {
    getJSON('/api/comments', {
        page: {{ page_index }}
    }, function (err, results) {
        if (err) {
            return fatal(err);
        }
        $('#loading').hide();
        initVM(results);
    });
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换-->
{% block content %}

    <div class="uk-width-1-1 uk-margin-bottom">
        <ul class="uk-breadcrumb">
            <li class="uk-active"><span>评论</span></li>
            <li><a href="/manage/blogs">日志</a></li>
            <li><a href="/manage/users">用户</a></li>
        </ul>
    </div>

    <div id="error" class="uk-width-1-1">
    </div>

    <div id="loading" class="uk-width-1-1 uk-text-center">
        <span><i class="uk-icon-spinner uk-icon-medium uk-icon-spin"></i> 正在加载...</span>
    </div>

    <div id="vm" class="uk-width-1-1">
        <table class="uk-table uk-table-justify uk-table-divider">
            <thead>
                <tr>
                    <th class="uk-text-left uk-width-small">作者</th>
                    <th class="uk-text-left">内容</th>
                    <th class="uk-text-left uk-table-expand">创建时间</th>
                    <th class="uk-text-left uk-width-small">操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-repeat="comment: comments" >
                    <td>
                        <span v-text="comment.user_name"></span>
                    </td>
                    <td>
                        <span v-text="comment.content"></span>
                    </td>
                    <td>
                        <span v-text="comment.created_at.toDateTime()"></span>
                    </td>
                    <td>
                        <a href="#0" v-on="click: delete_comment(comment)">删除</a>
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="uk-width-1-1 uk-text-center">
        <ul class="uk-pagination">
            <li v-if="! page.has_previous" class="uk-disabled"><span><i uk-icon="chevron-left"></i></span></li>
            <li v-if="page.has_previous"><a v-on="click: previous()" href="#0"><i uk-icon="chevron-left"></i></a></li>
            <li class="uk-active"><span v-text="page.page_index"></span></li>
            <li v-if="! page.has_next" class="uk-disabled"><span><i uk-icon="chevron-right"></i></span></li>
            <li v-if="page.has_next"><a v-on="click: next()" href="#0"><i uk-icon="chevron-right"></i></a></li>
        </ul>
        </div>
    </div>
{% endblock %}

~~~



用户的管理页面，在`www/templates`下创建`manage_users.html`：

~~~ html
<!-- 继承父模板 '__base__.html' -->
{% extends '__base__.html' %}
<!--jinja2 title 块内容替换-->
{% block title %}用户{% endblock %}
<!--jinja2 beforehead 块内容替换-->
{% block beforehead %}
<!--script中构建vue,向后端API提交用户管理操作相关数据-->
<script>

function initVM(data) {
    $('#vm').show();
    var vm = new Vue({
        el: '#vm',
        data: {
            users: data.users,
            page: data.page
        },
        methods: {
            previous: function () {
                gotoPage(this.page.page_index - 1);
            },
            next: function () {
                gotoPage(this.page.page_index + 1);
            },
            delete_user: function (user) {
                if (confirm('确认要删除用户“' + user.name + '”？删除后不可恢复！')) {
                    postJSON('/api/users/' + user.id + '/delete', function (err, r) {
                        if (err) {
                            return error(err);
                        }
                        refresh();
                    });
                }
            }
        }
    });
}

$(function() {
    getJSON('/api/users', {
        page: {{ page_index }}
    }, function (err, results) {
        if (err) {
            return fatal(err);
        }
        $('#loading').hide();
        initVM(results);
    });
});

</script>

{% endblock %}

<!--jinja2 content 块内容替换-->
{% block content %}
    <div class="uk-grid">
    <div class="uk-width-1-1 uk-margin-bottom">
        <ul class="uk-breadcrumb">
            <li><a href="/manage/comments">评论</a></li>
            <li><a href="/manage/blogs">日志</a></li>
            <li class="uk-active"><span>用户</span></li>
        </ul>
    </div>

    <div id="error" class="uk-width-1-1">
    </div>

    <div id="loading" class="uk-width-1-1 uk-text-center">
        <span><i class="uk-icon-spinner uk-icon-medium uk-icon-spin"></i> 正在加载...</span>
    </div>

    <div id="vm" class="uk-width-1-1">
        <table class="uk-table uk-table-divider">
            <thead>
                <tr>
                    <th class="uk-table-expand uk-text-left">名字</th>
                    <th class="uk-text-left">电子邮件</th>
                    <th class="uk-text-left">注册时间</th>
                    <th class="uk-text-left">操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-repeat="user: users">
                    <td>
                        <span v-text="user.name"></span>
                        <span v-if="user.admin" style="color:#d05">管理员</span>
                    </td>
                    <td>
                        <a v-attr="href: 'mailto:'+user.email" v-text="user.email"></a>
                    </td>
                    <td>
                        <span v-text="user.created_at.toDateTime()"></span>
                    </td>
                    <td>
                        <a href="#0" v-on="click: delete_user(user)">删除</a>
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="uk-width-1-1 uk-text-center">
        <ul class="uk-pagination">
            <li v-if="! page.has_previous" class="uk-disabled"><span><i uk-icon="chevron-left"></i></span></li>
            <li v-if="page.has_previous"><a v-on="click: previous()" href="#0"><i uk-icon="chevron-left"></i></a></li>
            <li class="uk-active"><span v-text="page.page_index"></span></li>
            <li v-if="! page.has_next" class="uk-disabled"><span><i uk-icon="chevron-right"></i></span></li>
            <li v-if="page.has_next"><a v-on="click: next()" href="#0"><i uk-icon="chevron-right"></i></a></li>
        </ul>
        </div>
    </div>
    </div>

{% endblock %}

~~~



####部署网站到远程服务器

下一步就要将我们的网站部署到Linux服务器上。可以选国内的阿里云，腾讯云等服务器。推荐Amazon的 [AWS EC 2](http://aws.amazon.com/) 虚拟机，只要申请就可以免费使用一年。在本地部署的话安装虚拟机即可，VMware, VirtualBox 等都可以。我们拿AWS的服务器来部署网站。

服务器的操作系统请选择最新版的Ubuntu系统。服务器准备完成后，我们就可以从本地通过ssh连接远程服务器了。请下载服务器的秘钥`xxxxxx.pem`, 复制服务器实例的ID`ec2-user@xx.xx.xx.xx`。然后可以使用PuTTY, SecureCRT等工具进行连接，具体教程请参考[aws官方指南](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)或自行百度（例如：ssh连接aws ec2服务器）。注意pem文件的的权限要改为 400：`chmod 400 /path/my-key-pair.pem`。

连接上了远程服务器后，我们就可以完全用Linux命令行来控制操作服务器了。一些简单基本的Linux命令(主要会用到cd, ls, mkdir, rm, cp, mv)请参考[[这里](https://blog.csdn.net/FungLeo/article/details/78488656)]。

首先我们在服务器上安装必要的一些服务：

```
$ sudo apt-get install nginx supervisor python3 mysql-server
```

- Nginx：高性能Web服务器+负责反向代理；
- Supervisor：监控服务进程的工具，可以随系统启动而启动服务，它还时刻监控服务进程，如果服务进程意外退出，Supervisor可以自动重启服务。
- MySQL：数据库服务。

然后安装Python需要的第三方库：

```
$ sudo pip3 install jinja2 aiomysql aiohttp markdown
```

在服务器上创建目录`/srv/awesome/`以及相应的子目录。

```
/
+- srv/
   +- awesome/       <-- Web App根目录
      +- www/        <-- 存放Python源码
      |  +- static/  <-- 存放静态资源文件
      +- log/        <-- 存放log
```

在服务器上初始化MySQL数据库，把数据库初始化脚本`schema.sql`复制到服务器上执行：

```
$ mysql -u root -p < schema.sql
```

上传文件到远程服务器可以安装`Irzsz`使用`sz`和`rz`命令来发送和接受文件，具体教程参考[[这里](http://blog.51cto.com/skypegnu1/1538371)]。

为了更高效率的部署本地文件到服务器我们需要用 [Fabric](http://www.fabfile.org/) 一个自动化部署工具。请安装在本地：

```
$ pip install fabric
```

然后将部署脚本`fabfile.py`放在本地`awesome-website`目录下，与`www`平级：

~~~ python
import os, re
from datetime import datetime

# 导入Fabric API:
from fabric.api import *

# 服务器登录用户名和秘钥:
env.hosts = ['ubuntu@xxxx.xxxx.compute.amazonaws.com']
env.key_filename = '/xxx/xxx/awsKeyPair.pem'

# 服务器MySQL用户名和口令:
db_user = 'root'
db_password = 'MySQL的root用户密码'

_TAR_FILE = 'dist-awesome.tar.gz'
_REMOTE_TMP_TAR = '/tmp/%s' % _TAR_FILE
_REMOTE_BASE_DIR = '/srv/awesome'

def deploy():
    newdir = 'www-%s' % datetime.now().strftime('%y-%m-%d_%H.%M.%S')
    # 删除已有的tar文件:
    run('rm -f %s' % _REMOTE_TMP_TAR)
    # 上传新的tar文件:
    put('dist/%s' % _TAR_FILE, _REMOTE_TMP_TAR)
    # 创建新目录:
    with cd(_REMOTE_BASE_DIR):
        sudo('mkdir %s' % newdir)
    # 解压到新目录:
    with cd('%s/%s' % (_REMOTE_BASE_DIR, newdir)):
        sudo('tar -xzvf %s' % _REMOTE_TMP_TAR)
        # 需要添加权限浏览器才能访问
        sudo('chmod -R 775 static/')
        sudo('chmod 775 favicon.ico')
        # # 由于app.py的文件格式有问题，转换一下
        # run('app.py')
    # 重置软链接:
    with cd(_REMOTE_BASE_DIR):
        sudo('rm -rf www')
        sudo('ln -s %s www' % newdir)
        sudo('chown ubuntu:ubuntu www')
        sudo('chown -R ubuntu:ubuntu %s' % newdir)
    # 重启Python服务和nginx服务器:
    with settings(warn_only=True):
        sudo('supervisorctl stop awesome')
        sudo('supervisorctl start awesome')
        sudo('/etc/init.d/nginx reload')


def build():
    includes = ['static', 'templates', 'transwarp', 'favicon.ico', '*.py', '*.txt']
    excludes = ['test', '.*', '*.pyc', '*.pyo']
    local('rm -f dist/%s' % _TAR_FILE)
    with lcd(os.path.join(os.path.abspath('.'), 'www')):
        cmd = ['tar', '--dereference', '-czvf', '../dist/%s' % _TAR_FILE]
        cmd.extend(['--exclude=\'%s\'' % ex for ex in excludes])
        cmd.extend(includes)
        local(' '.join(cmd))
~~~

在`awesome-website`目录下运行：

```
$ fab build
```

看看是否在`dist`目录下创建了`dist-awesome.tar.gz`的文件。如果提示tar打包错误，请忽视不用担心。接下来使用`deploy`命令将打包文件tar部署到服务器并解压，重置`www`软链，重启相关服务（相关服务还未配置，重启会提示错误）:

```
$ fab deploy
```

##### 配置Supervisor

接下来我们需要编写一个Supervisor的配置文件`awesome.conf`, 存放到`/etc/supervisor/conf.d/`目录下：

~~~
[program:awesome]
command     = /srv/awesome/www/app.py
directory   = /srv/awesome/www
user        = www-data
startsecs   = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 50MB
stdout_logfile_backups  = 10
stdout_logfile          = /srv/awesome/log/app.log
~~~

然后重启Supervisor后，就可以随时启动和停止Supervisor管理的服务了：

```
$ sudo supervisorctl reload
$ sudo supervisorctl start awesome
$ sudo supervisorctl status
```

##### 配置Nginx

Supervisor只负责运行`app.py`，我们还需要配置Nginx。如果申请了域名，nginx可以将域名解析到服务器，以下设置包含了域名的解析。（域名绑定服务器的教程请自行百度，需要给aws实例再申请一个免费固定IP地址—Elastic IP）把配置文件`awesome`放到`/etc/nginx/sites-available/`目录下：

~~~
server {
    # 监听80端口，作用是将用户http的请求转发到https
    listen      80;
    # 绑定的域名, 换成你注册的域名，将带www和不带www都写上，以防疏漏
    server_name aodabo.tech www.aodabo.tech;
    rewrite ^(.*)$  https://aodabo.tech permanent;
}

# 监听443端口
server {
    listen 443;
    server_name aodabo.tech www.aodabo.tech;
    ssl on;
    #ssl认证秘钥，填写两个秘钥文件在服务器上的绝对路径
    ssl_certificate   /etc/nginx/cert/xxxxxxxx.pem;
    ssl_certificate_key  /etc/nginx/cert/xxxxxxxx.key;
    
    #以下的设置要根据你域名供应商提供的设置信息进行更换,此处用的是阿里云域名的设置
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    root       /srv/awesome/www;
    access_log /srv/awesome/log/access_log;
    error_log  /srv/awesome/log/error_log;

    client_max_body_size 1m;

    gzip            on;
    gzip_min_length 1024;
    gzip_buffers    4 8k;
    gzip_types      text/css application/x-javascript application/json;

    sendfile on;

    location /favicon.ico {
        root /srv/awesome/www;
    }

    location ~ ^\/static\/.*$ {
        root /srv/awesome/www;
    }

    location / {
        proxy_pass       http://127.0.0.1:9000;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

}
~~~

然后在`/etc/nginx/sites-enabled/`目录下创建软链接：

```
$ cd /etc/nginx/sites-enabled
$ sudo ln -s /etc/nginx/sites-available/awesome .
```

让Nginx重新加载配置文件，网站就可以正常运行：

```
$ sudo /etc/init.d/nginx reload
```

如果有任何错误，都可以在`/srv/awesome/log`下查找Nginx和App本身的log。如果Supervisor启动时报错，可以在`/var/log/supervisor`下查看Supervisor的log。

至此，如果顺利，在浏览器打开你的域名地址，或者服务器的ip地址就可以看见你的网站啦！



#### Python搭建网站的其他可能性

除了本教程讲解的建站方法，Python建站还有很多其他的路径和可能性。比如网站的类型和除了可以是个人博客外，还可以是论坛，社交网站，作品集网站，在线聊天网站，视频网站等。不同网站的建站结构和思路也会有所不同，所需要的数据库结构也会随之有些改变。可以先从一些典型网站的案例做参考和学习。

Python建站所用的工具可以有很多变化，从后端数据库软件，到前端css框架，js框架，DOM等。工具的不同也会导致网站的视觉效果和处理性能的一些差别。可以先搜索对比后再决定用哪种工具更适合自己要建的网站。

**HTML / CSS 框架**

- Bootstrap
- Materialize
- Bulma
- Pure CSS

**JS框架**

- React: 目前最流行，用的人最多
- Vue: 简单易用，潜力很大
- Angular: 曾经很流行，现在有点衰退

**Python Web框架**

除了像我们自己手动码出一个Python Web框架，也可以用成熟的第三方Python Web框架，使我们的Web开发更加简单高效:

- [Django](https://www.djangoproject.com/)：全能型框架
- [Tornado](https://www.tornadoweb.org/en/stable/)：高性能框架
- [Flask](http://flask.pocoo.org/)：轻型框架，简单流行

**Javascript组件,特效,交互**

使用合适的Javascript代码会使页面的展示效果翻倍。尤其是增加动画特效，及交互等。在网站[凹大卜](https://aodabo.tech)使用了几个有意思的JS代码，包括：

- Valine无后台留言系统（这样就不用建立留言的相关数据库及页面了）；
- 博蒜子访问统计；
- 动态粒子连接特效。 

除了这些读者可以自行百度探索一些有意思的JS代码加到自己的网站中，使你的网站变得酷炫有趣。



**外链相关文章**

[[2019简易Web开发指南](https://segmentfault.com/a/1190000017525953)]

[[2018前端值得关注的技术](https://segmentfault.com/a/1190000012740426)]

[[2017热门前端技术总结](https://www.jianshu.com/p/16503dd472ff)]


# Python3 面向对象编程

## 1. python3 面向对象介绍

### 1.1 类与对象的关系

假如现在有一堆事物，他们拥有这些**共同特征**：

1. 直立行走
2. 会用工具
3. 喜吃熟食

现在我们将这些事物定义成：**人类**，人类拥有一些共同的动作或技能

1. 吃饭
2. 睡觉
3. 工作

这些动作或技能又组成了“人类”共有的**方法**



而小明是“人类”的一员，他在有“人类”特征的基础上，还有这些额外**属性**

1. 身高：1.70m
2. 体重：100g
3. 性别：男

**总结：**对象是**数据**与**行为**的集合，其中，数据对应的就是**属性**（做名词），行为对应的就是**方法**（做动词）

## 2. Python 面向对象

### 2.1 更多参数

在Python面向对象编程中，我们可以使用将**一个类作为参数传给另一个类**的方法来做到类与类之间的交互，在这里`self`代表的则是对象

```python
# 通过勾股定理计算两个点的距离
import math


class Point:
    def move(self, x, y):
        self.x = x
        self.y = y

    def reset(self):
        self.move(0, 0)

    def calculate_distance(self, other_point):
        return math.sqrt(
            (self.x - other_point.x) ** 2 +
            (self.y - other_point.y) ** 2
        )


point1 = Point()
point2 = Point()

point1.reset()
point2.move(5, 0)
# 通过对象中的某个方法来调用另一个对象，并直接将该对象的属性作为数据计算，达到类与类之间的交互
print(point2.calculate_distance(point1))
print(point1.calculate_distance(point2))
```

### 2.2 组织模块

一个包（package）是一个目录下模块的集合。包的名字就是目录的名字。我们只需要在目录下添加一个名为`__init__.py`的文件（通常是空文件）就可以告诉python这个目录是一个包

![image-20231013213120169](python3%20%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B.assets/image-20231013213120169.png)

### 2.3 相对模块导入

从当前的`database`模块导入`db`类

```py
from .database import db
```

从上层的`database`模块导入`db`类

```py
from ..database import db
```

### 2.4 谁可以访问我的数据

在python中，属性的私有化是通过在属性名之前加上`__`实现的，即：`__属性名`，此时外部对象就无法直接访问私有数据

```py
class Private:
    def __init__(self):
        self.__age = 18

        
p = Private()
print(p.__age)

# 输出
Traceback (most recent call last):
  File "D:\Python\test3.py", line 7, in <module>
    print(p.__age)
AttributeError: 'Private' object has no attribute '__age'
```

但是如果我们在**私有化属性名**之前加上`_类名`就可以突破这层限制

```py
class Private:
    def __init__(self):
        self.__age = 18


p = Private()
print(p._Private__age)

# 输出
18
```

这是因为当我们将属性私有化之后，python会自动将该属性的名字改为`_类名__属性名`，当我们在内部访问该属性的时候，python就会自动删除`_类名`，以保证在类的内部成功访问。但是在全局访问时，则不会做这一操作，导致python解释器无法找到`__age`

### 2.5 python 虚拟环境

不同的项目常常会依赖于特定库不同的版本是很常见的，此时如果我们将库升级或降级处理的话就会显得很麻烦，那么我们可以使用python虚拟环境，保证库与项目之间的互相隔离

### 2.6 综合案例

#### `notebook.py`

`notebook.py`的主要功能是通过初始化来创建一个笔记本，并且需要拥有搜索功能

```py
import datetime

# 为所有新笔记存储下一个可用id
last_id = 0


class Note:
    """
    在笔记本中表示一个笔记，在搜索中与字符串匹配，并为每个笔记存储标签
    """

    def __init__(self, memo, tags=''):
        """
        用备忘录和选项初始化一个笔记,自动设置笔记的创建数据和唯一的id
        """
        self.memo = memo
        self.tags = tags
        self.creation_data = datetime.date.today()
        global last_id
        last_id += 1
        self.id = last_id

    def match(self, filter):
        """
        确定此filter是否与过滤器文本匹配，如果匹配则返回true，否则返回false,搜索区分大小写，匹配memo和tags
        """
        return filter in self.memo or filter in self.tags
```

`test.py`测试结果

```py
from notebook import Note

n1 = Note("Hello first")
n2 = Note("Hello again")

print(n1.id)
print(n2.id)

print(n1.match('Hello'))
print(n2.match('Second'))

# 输出
1
2
True
False
```

在`notebook.py`下继续添加代码

```py
class Notebook:
    """
    表示可以标记、修改和搜索的笔记本目录
    """

    def __init__(self):
        """
        初始化一个笔记本目录
        """
        self.notes = []

    def new_note(self, memo, tags=''):
        """
        通过实例化Note对象来创建一个新的笔记本对象并将他添加到目录中
        """
        self.notes.append(Note(memo, tags))

    def modify_memo(self, note_id, memo):
        """
        通过ID来定位具体的笔记本，并修改其内容
        """
        for note in self.notes:
            if note.id == note_id:
                note.memo = memo
                break

    def modify_tags(self, note_id, tags):
        """
        通过ID来定位具体的笔记本，并修改他的 标签值(tags)
        """
        for note in self.notes:
            if note.id == note_id:
                note.tags = tags
                break

    def search(self, filter):
        """
        通过全局搜索来匹配笔记本
        """
        return [note for note in self.notes if note.match(filter)]
```

`test.py`测试结果

```py
from notebook import Note, Notebook

n = Notebook()

n.new_note('hello world')
n.new_note('hello again')

## notes是一个列表，内容是 Note 对象，所以返回的是 Note 对象的内存地址
## n对象 -> notes属性 [Note对象,Note对象] -> Note对象.属性
print(n.notes)		
#[<notebook.Note object at 0x0000029FE8E78400>, <notebook.Note object at 0x0000029FE90F2518>]

print(n.notes[0].id)	# 1
print(n.notes[0].memo)	# hello world

## return note：返回的是note对象
print(n.search("world"))		# [<notebook.Note object at 0x0000029FE8E78400>]
n.modify_memo(1, 'hi world')
print(n.notes[0].memo)			# hi world
```

由于`modify_memo`和`modify_tags`方法几乎一样，所以需要进一步改进。新增方法`_find_note`以做内部使用

```py
    def _find_note(self, note_id):
        """
        通过给定的ID来定位笔记本
        """
        for note in self.notes:
            if note == note_id:
                return note_id
            else:
                return Note

    def modify_memo(self, note_id, memo):
        """
        通过ID来定位具体的笔记本，并修改其内容
        """
        self._find_note(note_id).memo = memo

    def modify_tags(self, note_id, tags):
        """
        通过ID来定位具体的笔记本，并修改他的 标签值(tags)
        """
        self._find_note(note_id).tags = tags
```

#### `menu.py`

```py
import sys
from notebook import Note, Notebook


class Menu:  sou'suo
    """
    显示菜单并在运行时响应选项
    """

    def __init__(self):
        self.notebook = Notebook()
        self.choices = {
            "1": self.show_notes,
            "2": self.search_notes,
            "3": self.add_note,
            "4": self.modify_note,
            "5": self.quit

        }

    def display_menu(self):
        print("""
            笔记菜单
            
            1. 显示所有笔记
            2. 查找笔记
            3. 添加笔记
            4. 修改笔记
            5. 退出
        """)

    def run(self):
        """
        查看菜单并响应选择，程序入口
        """
        while True:
            self.display_menu()
            choice = input("输入你的选择：")
            action = self.choices.get(choice)
            if action:
                action()
            else:
                print("{} 是一个无效选项!".format(choice))

    def show_notes(self, notes=None):
        """
        列表展示方法，如果没有笔记本，则实例化一个笔记本列表，找到则展示这个笔记本对象的所有属性
        """
        if not notes:
            notes = self.notebook.notes
        for note in notes:
            print("{0}: {1}\{2}".format(note.id, note.tags, note.memo))

    def search_notes(self):
        """
        通过search函数搜索笔记本，返回一个notes对象 return object
        """
        filter = input("关键字搜索：")
        notes = self.notebook.search(filter)
        self.show_notes(notes)

    def add_note(self):
        memo = input("输入你的笔记：")
        self.notebook.new_note(memo)
        print("你的笔记已添加")

    def modify_note(self):
        id = input("输入笔记本的ID：")
        memo = input("输入新笔记：")
        tags = input("输入你的标签：")
        if memo:
            self.notebook.modify_memo(id, memo)
        if tags:
            self.notebook.modify_tags(id, tags)

    def quit(self):
        print("谢谢支持！")
        sys.exit(0)


if __name__ == '__main__':
    Menu().run()
```

## 3. 对象相似时

### 3.1 基本的继承

+ 在创建一个类时，如果我们不指定这个类的父类，则默认继承`object`类

#### 类属性

```py
class Contact:
    # 类变量
    all_contacts = []

    def __init__(self, name, mail):
        self.name = name
        self.mail = mail
        Contact.all_contacts.append(self)
```

**注意：**

+ `all_contacts = []` 不等于 `self.all_contacts = []`
+ ``self.all_contacts = []`只允许当前对象访问；而`all_contacts = []`可以在任何位置以` Contact.all_contacts`访问

### 3.2 重写和super

如果子类要继承父类，并且需要在`__init__`上做其他配置，可以使用`super()`函数先继承父类的`__init__`后再添加额外属性

**任何方法都可以使用`super()`来重写**

```py
class Contact:
    all_contact = ContactList()

    def __init__(self, name, email):
        self.name = name
        self.email = email
        self.all_contact.append(self)


class Friend(Contact):
    def __init__(self, name, email, phone):
        # 输入父类要输入的参数
        super().__init__(name, email)
        self.phone = phone
```

### 3.3 多重继承

在python开发中，大多数情况下，==如果你要用到多重继承，那么大多数是因为你的架构有问题==

多重继承的关键问题时：有时会让某个父类或基类的方法被执行了**多次**，这样是会存在一定的安全隐患的

```py
class BaseClass:
    num_base_calls = 0

    def call_me(self):
        print("调用了 BaseClass")
        self.num_base_calls += 1


class LeftSubclass(BaseClass):
    num_left_calls = 0

    def call_me(self):
        BaseClass.call_me(self)
        print("调用了 LeftSubClass")
        self.num_left_calls += 1


class RightSubclass(BaseClass):
    num_right_calls = 0

    def call_me(self):
        BaseClass.call_me(self)
        print("调用了 RightSubclass")
        self.num_right_calls += 1


class Subclass(LeftSubclass, RightSubclass):
    num_sub_calls = 0

    def call_me(self):
        LeftSubclass.call_me(self)
        RightSubclass.call_me(self)
        print("调用了 Subclass")
        self.num_sub_calls += 1


s = Subclass()
s.call_me()
print(s.num_sub_calls, s.num_left_calls, s.num_right_calls, s.num_base_calls) 


# 输出
调用了 BaseClass
调用了 LeftSubClass
调用了 BaseClass
调用了 RightSubclass
调用了 Subclass
1 1 1 2
```

查看上面的代码可以看到，`BaseClass`的`call_me`方法被执行了多次，如果这个方法是用于银行取款的，那么就会产生隐患



此时如果我们使用`super()`方法来继承的话，就会是下面这种结果：

```py
class BaseClass:
    num_base_calls = 0

    def call_me(self):
        print("调用了 BaseClass")
        self.num_base_calls += 1


class LeftSubclass(BaseClass):
    num_left_calls = 0

    def call_me(self):
        super().call_me()
        print("调用了 LeftSubClass")
        self.num_left_calls += 1


class RightSubclass(BaseClass):
    num_right_calls = 0

    def call_me(self):
        super().call_me()
        print("调用了 RightSubclass")
        self.num_right_calls += 1


class Subclass(LeftSubclass, RightSubclass):
    num_sub_calls = 0

    def call_me(self):
        super().call_me()
        print("调用了 Subclass")
        self.num_sub_calls += 1


s = Subclass()
s.call_me()
print(s.num_sub_calls, s.num_left_calls, s.num_right_calls, s.num_base_calls)


# 输出
调用了 BaseClass
调用了 RightSubclass
调用了 LeftSubClass
调用了 Subclass
1 1 1 1
```

可以看到`Baseclass`的`call_me`方法只被执行了一次，这里的执行顺序如下：

```
Subclass.call_me -> LeftSubclass.call_me -> RightSubclass.call_me -> Subclass.call_me
```

为什么会先执行 `LeftSubclass`的`call_me`方法？

​		在子类出现多重继承的时候，MRO遵循C3线性化原则，也就是子类永远在父类前面，如果有多个父类，会根据它们在列表中的顺序被检查，第一个被找到的父类中的方法将被使用。

### 3.4 不同集合的参数

以下面代码为例子

```py
class Subclass(Contact, AddressHolder):
    	def __init__(self. name, email, street, city, state, code)
        	Contact.__init__(self, name, email)
            AddressHoloder.__init__(self, street, city, state, code)
```

这段代码将两个父类分别做了两次初始化，是非常笨重的，但是因为每个父类他所需要传入的参数都不一样，又不能直接以`super().__init__()`的方式来重构，所以只能使用下面这种写法

```py
class Contact:
    all_contact = []

    def __init__(self, name='', email='', **kwargs):
        super().__init__(**kwargs)
        self.name = name
        self.email = email
        self.all_contact.append(self)
        print(f"Contact say name: {name}, email: {email}, all_contact: {self.all_contact}")
        print(f"Contact say **kwargs: {kwargs}")


class AddressHolder:
    def __init__(self, street='', city='', state='', code='', **kwargs):
        super().__init__(**kwargs)
        self.street = street
        self.city = city
        self.state = state
        self.code = code
        print(f"AddressHolder say street: {self.street}, city: {self.city}, state: {self.state}, code: {self.code}")
        print(f"AddressHolder say **kwargs: {kwargs}")


class Friend(Contact, AddressHolder):
    def __init__(self, phone='', **kwargs):
        # 输入父类要输入的参数
        super().__init__(**kwargs)
        self.phone = phone
        print(f"Friend say phone：{self.phone}")
        print(f"Friend say **kwargs: {kwargs}")


f = Friend(name='John Doe', email='johndoe@example.com', street='123 Main St', city='Anytown', state='Anystate', code='12345', phone='123456')


# 输出
AddressHolder say street: 123 Main St, city: Anytown, state: Anystate, code: 12345
AddressHolder say **kwargs: {}
Contact say name: John Doe, email: johndoe@example.com, all_contact: [<__main__.Friend object at 0x0000025C9235D2B0>]
Contact say **kwargs: {'street': '123 Main St', 'city': 'Anytown', 'state': 'Anystate', 'code': '12345'}
Friend say phone：123456
Friend say **kwargs: {'name': 'John Doe', 'email': 'johndoe@example.com', 'street': '123 Main St', 'city': 'Anytown', 'state': 'Anystate', 'code': '12345'}
```

在使用`super()`重构的同时，使用`**kwargs`来传参，为了保证代码的健壮性，还需要将参数做一个默认赋值，最后使用`**kwargs`来传入

```
super().__init__(**kwargs)。这个调用会将**kwargs传递给上一级的父类，并且在每个类的__init__方法中，你都从**kwargs中取出了一些键值对来初始化该类的实例变量。

例如，在Friend类的__init__方法中，你没有从**kwargs中删除任何键值对，所以打印出的**kwargs包含了所有你传入的参数。然后，当你在Friend类的__init__方法中调用了super().__init__(**kwargs)，这个调用将会把所有的参数传递给它的父类，也就是Contact和AddressHolder。

然后，在Contact类的__init__方法中，你从**kwargs中取出了’name’和’email’两个键值对来初始化’name’和’email’两个实例变量。所以，当你打印出来的时候，'name’和’email’已经不再字典中了。

同样，在 AddressHolder 类的 __init__() 方法中，你从 **kwargs 中取出了 ‘street’, ‘city’, ‘state’, ‘code’ 四个键值对来初始化相应的实例变量。所以，当你在 AddressHolder.__init__() 中打印 **kwargs, 它已经是空字典了。
```

## 4. 异常处理

### 4.1 抛出异常

**本质上，异常只是一个对象**

在异常处理时，我们可以抛出一个异常，然而，在异常抛出之后，后续的代码就无法执行

```py
class EvenOnly(list):
    def append(self, integer):
        if not isinstance(integer, int):
            raise TypeError("只允许添加整数")
            print("永远不会执行")
        if integer % 2:
            raise ValueError("只允许添加偶数")
            print("永远不会执行")
        super().append(integer)


e = EvenOnly()
e.append(3)
print(e)
```

### 4.2 异常处理

#### 4.2.1 捕获多个异常一起处理

```py
def funny_division2(anumber):
    try:
        if anumber == 13:
            raise ValueError("13 是一个不幸运的数字")
    except (ZeroDivisionError, TypeError, ValueError):
        return "输入一个大于0的数字"
```

#### 4.2.2 捕获多个异常单独处理

```py
def funny_division2(anumber):
    try:
        if anumber == 13:
            raise ValueError
    except ZeroDivisionError:
        return "请输入一个非0的整数"
    except TypeError:
        return "请输入一个数字"
    except ValueError:
        return "13 是一个不幸运的数字"
```

#### 4.2.3 不管是否有异常都执行特定代码

+ `else`：子句会在`try`子句中的代码正常执行完毕之后执行
+ `finally`：子句会在`try`子句执行完毕之后执行（不管是否遇到异常），这在某些特定情况非常有用
  + 清除打开的数据库连接
  + 关闭打开的文件
  + 向网络发送一次关闭握手

**注意：如果`try`子句中有`return`语句，`else`子句则不会执行**

```py
def funny_division2(anumber):
    try:
        if anumber == 13:
            raise ValueError
        print("try 语句正常执行")
        # return True
    except ZeroDivisionError:
        return "请输入一个非0的整数"
    except TypeError:
        return "请输入一个数字"
    except ValueError:
        return "13 是一个不幸运的数字"
    else:
        print("else 语句执行完毕")
    finally:
        print("代码最后执行")
```

### 4.3 异常的层级

==python 异常的层级关系：==

+ BaseException
  + SystemExit
  + Keyboardinterrupt
  + Exception
    + More Other Expection

`SystemExit`会在程序自然退出时抛出，通常是因为我们执行了`sys.exit`函数

`Keyboardinterrupt`会在用户执行依赖于系统的按键组合（通常是 Ctrl+C）中成勋时会抛出这个异常

```py
import sys


def exception_dispose():
    str = input('请输入：')

    try:
        if str == 'quit':
            sys.exit()
    except SystemExit:
        print("函数 sys.exit() 退出程序")
    except KeyboardInterrupt:
        print("Ctrl+C 组合键退出程序")
```

### 4.4 自定义异常

一个类继承了`Exception`或`BaseException`就可以做为异常类来处理

```py
class InvaildWithdrawal(Exception):
    def __init__(self, balance, amount):
        super().__init__(f"你的账户余额：{amount}")
        self.amount = amount
        self.balance = balance

    def overage(self):
        return self.amount - self.balance


try:
    raise InvaildWithdrawal(25, 50)
# e 是一个异常对象，通过实例化 InvaildWithdrawal 获取，所以可以调用 InvaildWithdrawal 的方法
except InvaildWithdrawal as e:
    print(f"对不起， 您的余额不足。超过余额{e.overage()}")
```

### 4.5 什么时候使用自定义异常

大多数的时候，我们都会使用`if`语句来判断用户的输入是否正确来躲避程序异常，但是这时一种非常愚蠢的方法，程序没有必要为了一些少数情况而去浪费CPU资源来做`if`判断。

在我们遇到下面代码的时候就应该使用异常处理来处理程序，这样在保证异常程序顺利执行的同时还能在异常处理的时候添加更多的灵活处理

```py
def divide_with_exception(number, divisor):
    if divisor == 0:
        print("被除数不能为0")
        return

    print(f"{number} / {divisor} = {number / divisor}")


divide_with_exception(2, 1)
```

## 5. 何时使用面向对象编程

### 5.1 property关键字的使用

返回 property 属性。

*fget* 是获取属性值的函数。 *fset* 是用于设置属性值的函数。 *fdel* 是用于删除属性值的函数。并且 *doc* 为属性对象创建文档字符串。

一个典型的用法是定义一个托管属性 `x`:

```
class C:
    def __init__(self):
        self._x = None

    def getx(self):
        return self._x

    def setx(self, value):
        self._x = value

    def delx(self):
        del self._x

    x = property(getx, setx, delx, "I'm the 'x' property.")
```

如果 *c* 是 *C* 的实例，`c.x` 将调用getter，`c.x = value` 将调用setter， `del c.x` 将调用deleter。

### 5.2 通过属性向类数据添加行为

python中的`property`关键字用于将方法变得看起来像属性，当我们对类或者方法进行私有化操作的时候，可以通过`property`关键字做到让外部直接进行访问

```bash
class Color:
    def __init__(self, rgb_value, name):
        self.rgb_value = rgb_value
        self.name = name

    def _set_name(self, name):
        if not name:
            raise Exception("Invalid Name")
        self._name = name

    def _get_name(self):
        return self._name

    name = property(_get_name, _set_name)


c = Color('#0000ff', 'red')
print(c.name)		# red

c.name = 'blue'
print(c.name)		# blue
```

### 5.3 属性的细节

```bash
class Silly:
    def _get_silly(self):
        print("你越来越傻了")
        return self._silly

    def _set_silly(self, value):
        print(f"你在胡闹 {value}")
        self._silly = value

    def _del_silly(self):
        print("哇，你杀了个傻瓜！")
        del self._silly

    silly = property(_get_silly, _set_silly, _del_silly, "这是一个愚蠢的性质")


s = Silly()
s.silly = "funny"			# 输出：你在胡闹 funny
del s.silly					# 输出：哇，你杀了个傻瓜！
```

### 5.4 装饰器——另一种创建属性的方法

着段代码等价于上面的代码

```py
class Silly:
    @property
    def silly(self):
        """
        将该函数设置为 property 属性，等同于 silly = property(getter)
        """
        print("你越来越傻了")
        return self._silly

    @silly.setter
    def silly(self, value):
        """
        设置 silly property 的 setter 方法
        """
        print(f"你在胡闹 {value}")
        self._silly = value

    @silly.deleter
    def silly(self):
        """
        设置 silly property 的 deleter 方法
        """
        print("哇，你杀了个傻瓜！")
        del self._silly


s = Silly()
s.silly = 'bob'  # 输出：你在胡闹 bob
s.silly  # 输出：你越来越傻了

```

### 5.5 决定何时使用property

事实上，方法只是可调用的属性。方法的名字通常都是动词

**属性与property之间的区别在于，当property被读取、赋值或删除时，自动执行某些特定的操作**

一个常见的例子：缓存某个较难计算或者访问需要耗时的值。目标在于，将这个值存于本地以避免重复获取

```python
import requests
import time


class WebPage:
    def __init__(self, url):
        self.url = url
        self._content = None

    @property
    def content(self):
        if not self._content:
            print("重新获取网页内容中......")
            self._content = requests.get(self.url).content.decode()
        return self._content


if __name__ == '__main__':                                                                                                                                                                                                                                                                       
    web = WebPage('http://www.baidu.com')
    start = time.time()
    print(web.content)
    end = time.time() - start
    print("第一次用时：", end)        # 0.035973548889160156
    start = time.time()
    print(web.content)
    end = time.time() - start
    print("第二次用时：", end)        # 0.0024101734161376953
```

## 6. python 的数据结构

### 6.1 命名元组

命名元组相较于元组又更强的“可读性”和“便捷性”，可为元组中的每个值赋予一个名字

**命名元组非常适合只有数字的情况**

```python
from collections import namedtuple

stock = namedtuple('stock', 'symbol current high low')
stock = stock('FB', '95', '99', '90')
print(stock)				# 输出：stock(symbol='FB', current='95', high='99', low='90')
print(stock.current)		# 95
```

### 6.2 字典

对象通过字典来表示属性，其值为属性的值或对象的方法

#### 6.2.1 字典常用方法

**get: 获取字典的指定值**

第一个参数是键名，第二个参数是当键名不存在时所返回的默认值

```python
dict1 = {'name': 'jack', 'age': 18}

print(dict1)                                # {'name': 'jack', 'age': 18}
print(dict1.get('phone', 'not Found'))      # not Found
```

**setdefault: 当键名不存在时，赋值并创建**

如果之前没有设定过值，才将这一键名设定为这一默认值

```python
dict1 = {'name': 'jack', 'age': 18}

dict1.setdefault('phone', '123456')         # {'name': 'jack', 'age': 18, 'phone': '123456'}
print(dict1)
```

**keys: 获取所有的键名**

返回一个包含所有键名的迭代器

```python
dict1 = {'name': 'jack', 'age': 18, 'phone': '123456', 'email': '123@11.com'}

for key in dict1.keys():
    print(key)
```

**values: 获取所有的值**

放回一个包含所有值的迭代器

```python
dict1 = {'name': 'jack', 'age': 18, 'phone': '123456', 'email': '123@11.com'}

for value in dict1.values():
    print(value)
```

**items: 返回键值对**

返回`(key, value)`元组所组成的迭代器

```python
dict1 = {'name': 'jack', 'age': 18, 'phone': '123456', 'email': '123@11.com'}

for key,value in dict1.items():
    print(f"key: {key}, value: {value}")
```


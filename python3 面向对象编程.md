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

### 6.3 集合

集合和字典一样，是无序的。它们都是利用底层的基于哈希值的数据结构来保证高效。因为它们都是无序的。故集合无法通过索引来查找元素

从下面的方法可以看出，集合是用于和其他集合进行原酸的，而不仅仅是一个容器。如果有一些来自两个不同源的集合，补需要快速以某种形式组合起来，以确定数据是否重合或不同，我们可以利用集合操作来快速比较它们

```python
set1 = {'三角形', '正方形', '平行四边形'}
set2 = {'圆形', '三角形', '梯形'}

print(set1.union(set2))                     # 并集        {'圆形', '三角形', '梯形', '正方形', '平行四边形'}
print(set1.intersection(set2))              # 交集        {'三角形'}
print(set1.symmetric_difference(set2))      # 补集        {'平行四边形', '圆形', '梯形', '正方形'}
print(set1.issubset(set2))                  # 判断 set1 是不是 set2 的子集      False
print(set1.issuperset(set2))                # 判断 set1 是不是 set2 的父集      False
print(set1.difference(set2))                # 返回存在于 set2 中但又不在 set1 中的元素        {'正方形', '平行四边形'}
```

在列表中，会经常遇到遍历所有对象直到找到相同的值，而对于集合来说，只需要求出哈希值并检查值是否存在即可。这意味着无论集合有多大，查找一个值是否存在所需要的时间是固定的，但是对于列表来说列表长度越长搜索一个值所需要的时间就越长

在后续的编程中，如果遇到要遍历一个大列表的情况，可以写成下面这样

```python
list1 = [1, 2, 3, 4, 5, 6]
set2 = {3}

print(set2.issubset(set(list1)))		# 输出：True
```

### 6.4 队列

**先进先出原则**

Queue 的容量是“无限”的（直到计算机的内存耗尽为止），不过通常会限定在一个最大的范围。主要的方法有`put()`和`get()`，用于向队列最后添加元素和从队列开头取出元素。如果队列为空（无法取出）或已满（无法添加），无法执行这些方法，那么它们都接收有一个可选参数，用于在这种情况下执行相应的操作**。默认的行为是阻止或阻塞（直到等到 Queue 对象拥有足够的数据或空间来完成这些操作**）。如果传递`block=False`参数，就可以**抛出异常**，或者可以通过传递`timeout`参数来定义一定的等待时间，超时之后才抛出异常

```python
from queue import Queue

lineup = Queue(maxsize=3)           # 创建队列，最大容量为 3
# lineup.get(block=False)           # 由于 block=False 所以会抛出异常：_queue.Empty

lineup.put('1')
lineup.put('2')
lineup.put('3')

print(lineup.full())                # 输出：True

print(lineup.get())					# 输出：3
print(lineup.get())					# 输出：2
print(lineup.get())					# 输出：1

print(lineup.empty())               # 输出：True
```

### 6.5 栈

**先进后出原则**

用法与`Queue`相同

```python
from queue import LifoQueue

stack = LifoQueue(maxsize=3)

stack.put('1')
stack.put('2')
stack.put('3')
# stack.put('4', block=False)       # 异常：queue.Full

print(stack.get())                  # 输出：3
print(stack.get())                  # 输出：2
print(stack.get())                  # 输出：1

print(stack.empty())
```

### 6.6 优先级队列

通常是将一个元组存储到优先级队列中，其中元组的第一个元素代表其优先级，第二个元素是数据。优先级队列元素的返回是按照元素的优先级返回的**（优先级越小越大）**

优先级队列总是返回当前队列中最重要的元素，如果队列是空的，`get()`方法将会阻塞（默认情况下）；如果队列非空，就不会阻塞，也不会等待优先级更高的元素添加进来

```python
import heapq

# 创建一个空的优先级队列
priority_queue = []

# 向优先级队列中添加元素
heapq.heappush(priority_queue, (2, 'code'))
heapq.heappush(priority_queue, (1, 'eat'))
heapq.heappush(priority_queue, (3, 'sleep'))

# 从优先级队列中移除并返回最小的元素
print(heapq.heappop(priority_queue))  # 输出：(1, 'eat')
print(heapq.heappop(priority_queue))  # 输出：(2, 'code')
print(heapq.heappop(priority_queue))  # 输出：(3, 'sleep')
```

## 7. Python 面向对象编程

### 7.1 `len()`函数

调用`len()`函数用的是底层类的`__len__`函数，因此`len(myobj)`实际值向的是`MyObj.__len__(myobj)`

### 7.2 反转函数

`reversed()`函数以任何序列作为输入，并返回一个该序列倒叙的复制品。通常用于for循环语句，来从后向前遍历项目

和`len`类似，`reversed`调用传入参数所属类的`__reversed__()`函数。如果这一方法不存在，`reversed`会通过调用`__len__`和`__getitem__`自己构建一个倒序序列

```python
normal_list = [1,2,3,4,5]

class CustomSequence:
    def __len__(self):
        return 5

    def __getitem__(self, item):
        return f"x{item}"

class FunkyBackwards:
    def __reversed__(self):
        return "BACKWORADS"

for seq in normal_list, CustomSequence(), FunkyBackwards():
    print(f"\n: {seq.__class__.__name__}", end='')
    for item in reversed(seq):
        print(item, end='')
```

### 7.3 枚举

在Python中，`enumerate()`是一个内置函数，用于将一个可迭代的数据对象（如列表、元组或字符串）组合为一个索引序列，同时列出数据和数据下标，一般用在`for`循环当中

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
print(list(enumerate(seasons)))  # 输出：[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
```

```python
filename = 'D:\Python\python 3 面向对象编程\第六章案例\index.py'

with open(filename) as file:
    for index, line in enumerate(file):
        print(f"{index+1} : {line}", end='')
        
# 输出：
1 : from urllib.parse import urlparse
2 : import requests
3 : import re
4 : import sys
```

+ all 和 any，均接收可迭代对象作为参数，如果所有（all）或任一（any）元素为真（例如非空字符串或列表、非零的数字、不是None的对象，或者是文本True时，返回False）
+ `eval exec complie`，可以在解释器中将字符串当作代码执行（特别注意：`complie`也可以）
+ `hasattr getattr setattr delattr` 可以通过字符串名字修改对象的属性
+ zip，以两个或更多序列作为参数并返回一个新的由元组构成的序列，其中每个元组包含每个参数序列中的值

### 7.4 变量参数列表

**`*args`**：接收任意数量的参数并且将它们全部放进一个名为`args`的列表。如果只提供一个参数，它将会是一个只有一个元素的列表；如果不提供任何参数，它将是一个空列表

**`**kwargs`**：接收任意关键字参数，通常用于配置设定

```python
class Options:
    default_options = {
        'port': 21,
        'host': 'localhost',
        'username': None
    }

    def __init__(self, **kwargs):
        self.options = dict(Options.default_options)
        self.default_options.update(kwargs)
```

### 7.5 参数解包

`*`操作符：用于对列表进行解构，得到列表中的每一个元素

`**`操作符：用于对字典进行解构，得到字典中的每一个键值对

```python
def show_args(arg1, arg2, arg3="THREE"):
    print(arg1, arg2, arg3)


some_args = range(3)
more_args = {
    'arg1': 'ONE',
    'arg2': "TWO"
}

print("Unpacking a sequence: ", end='')
show_args(*some_args)                       # 输出：Unpacking a sequence: 0 1 2

print("Unpacking a dict: ", end='')
show_args(**more_args)                      # 输出：Unpacking a dict: ONE TWO THREE 	
```

## 8. 字符串与序列化

### 8.1 格式化字符串

```python
orders = [('burger', 2, 5), ('fries', 3.5, 1), ('cola', 1.75, 3)]

print("PRODUCT\tQUANTITY\tPRICE\tSUBTOTAL")

for product, price, quantity in orders:
    subtotal = price * quantity
    print("{0:10s}{1: ^9d}    ${2: <8.2f}${3: >7.2f}".format(product, quantity, price, subtotal))
```

- `{0:10s}`：这表示第一个参数 `product` 是一个字符串（`s`），并且它应该被格式化为宽度为 10 的字段。
- `{1: ^9d}`：这表示第二个参数 `quantity` 是一个整数（`d`），并且它应该被格式化为宽度为 9 的字段，值在字段中居中（`^`）。
- `{2: <8.2f}`：这表示第三个参数 `price` 是一个浮点数（`f`），并且它应该被格式化为宽度为 8 的字段，小数点后有两位数字，值在字段中左对齐（`<`）。
- `{3: >7.2f}`：这表示第四个参数 `subtotal` 是一个浮点数（`f`），并且它应该被格式化为宽度为 7 的字段，小数点后有两位数字，值在字段中右对齐（`>`）。

### 8.2 将文本转换成字节

**`print()`函数默认会以ASCII编码来输出字节对象**

在使用`str.encode()`或`str.decode()`的过程中，可能会遇到错误编码导致报错的情况，可以使用`error`关键字参数来处理

`str.decode('utf-8', error='replace')`

error 四种状态如下：

+ `strict`：默认规则，当遇到无法编码或解码的字符时，抛出异常
+ `replace`：当遇到无法编码或解码的字符时，使用另一个字符代替（ASCII编码中使用的时`?`）
+ `ignore`：当遇到无法编码或解码的字符时，保留原样输出
+ `xmlcharrefreplace`：创建一个xml实体表示Unicode字符，这样可以根据XML文档来转换字符串

**`str.encode()和``str.decode()`方法在不指定编码时，默认使用当前平台的编码方法**

## 9. 迭代器模式

### 9.1 迭代器

一个类如果拥有`__next__`和`__iter__`方法就可以封装成一个迭代器，并使用`iter()` 函数来获取这个迭代器对象，当我们使用`next()`来获取迭代器的下一个元素时，实际上调用的就是迭代器对象中的`__next__`方法

```python
class CapitalIterable:
    def __init__(self, string):
        self.string = string

    def __iter__(self):
        return CapitalIterator(self.string)


class CapitalIterator:
    def __init__(self, string):
        self.words = [w.capitalize() for w in string.split()]
        self.index = 0

    def __next__(self):
        if self.index == len(self.words):
            raise StopIteration()

        word = self.words[self.index]
        self.index += 1
        return word

    def __iter__(self):
        return self

if __name__ == '__main__':
    iterable = CapitalIterable('the quick brown fox jumps over the lazy dog')
    iterator = iter(iterable)			# 获取迭代器对象
    while True:
        try:
            print(next(iterator))
        except StopIteration:
            break
            
    # 也可以直接使用for循环来遍历迭代器
     for i in iterable:
        print(i)
```

### 9.2 列表、集合、字典推导式

具体是哪一类的推导式是根据推导式的特征来判断的，如列表推导式使用的是`[]`，而字典和集合推导式则使用的是`{}`

```python
from collections import namedtuple
Book = namedtuple('Book', 'author title genre')
books = [
    Book('Pratchett', 'Nightwatch', 'fantasy'),
    Book('Pratchett', 'Thief Of Time', 'Fantasy'),
    Book('Le Guin', 'The Dispossessed', 'scifi')
]

fantasy_genre = [b.genre for b in books]                                    # 列表推导式
print(fantasy_genre)

fantasy_author = {b.author for b in books if b.genre == 'fantasy'}          # 集合推导式
print(fantasy_author)

fantasy_titles = {b.title: b for b in books if b.genre == 'fantasy'}        # 字典推导式
print(fantasy_titles)
```

### 9.3 生成器表达式

如果我们要遍历一个巨大对象，如：文件，巨大列表的时候，就可以使用生成器，这样的好处是：python不会一口气将整个文件加载到内存中，而是用多少加载多少

生成器表达式使用的是`()`

```python
gen = (i for i in [1, 2, 3, 4, 5, 6, 7, 8] if i == 4)
for j in gen:
    print(j)
```

### 9.4 生成器

生成器本身就属于迭代器的一种

生成器使用的是`yield`关键字，它会推出当前函数并返回一行内容，**且再次调用时会返回`yield`的语句的下一行——而不是函数开始的位置**

**函数本身也是对象，只不过他是顶格对象，所以可以被全局调用**

**生成器本身就是对象，所以要做实例化`()`**

```python
def test():
    list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in list1:
        yield i


class test1:
    def test(self):
        list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]
        for i in list1:
            yield i


g = test()				# 实例化生成器
print(next(g))
print(next(g))
print(next(g))
print(next(g))

t = test1().test()		# 实例化生成器
print(next(t))
print(next(t))
print(next(t))
print(next(t))
```

### 9.5 另一个可迭代对象中产生

`yield from()`语法可以创建一个生成器且从另一个生成器中获取数据并返回

```python
def test():
    list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in list1:
        yield i


class test1:
    def test(self):
        yield from (l for l in test())

t = test1().test()
print(next(t))
print(next(t))
print(next(t))

# 输出：
1
2
3
```

### 9.6 协程

这里实际上跟协程有什么关系其实我还不能理解，但是先记录一下这里的语法

由于函数内部有`yield`语句，因此python会将这个简单的函数转换为一个对象

`send()`方法几乎和`next()`方法一样，不过，处理让生成器继续执行到下一个yield语句外，你还可以向生成器中传入一个值。这个值将会赋予`yield`语句左侧的变量

+ `yield`出现和生成器暂停
+ 在函数外执行`send()`方法，且激活了生成器
+ 发送的值赋给了`yield`语句左侧的变量
+ 生成器继续执行，直至遇到下一个`yield`语句

`yield`语句返回一个值，这个值作为最近一次`send`调用的返回值。`sned()`方法不仅仅时提交一个值给生成器，其同时也会从`yield`语句返回值

```python
def tally():
    score = 0
    while True:
        increment = yield score
        if increment is None:
            increment = 0
        score += increment



white_sox = tally()
blue_jays = tally()

print(next(white_sox))
print(next(blue_jays))

print(white_sox.send(5))
print(blue_jays.send(3))

# 输出：
0
0
5
3
```

## 10. python设计模式 I

### 10.1 装饰器

在 Python 中，装饰器是一种特殊类型的函数，它可以修改其他函数的行为。装饰器的工作原理是，它接收一个函数作为输入，然后返回一个新的函数，这个新函数通常会包含一些额外的行为。

下面是一个最简单的装饰器

```python
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")

    return wrapper


def say_hello():
    print("Hello!")


say_hello = my_decorator(say_hello)     # my_decorator(say_hello) = wrapper()
say_hello()
```

### 10.2 python 中的装饰器

python 可以对装饰器做一些特定的语法操作来调用装饰器，这段代码等同于上面的代码

```python 
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")

    return wrapper


@my_decorator
def say_hello():
    print("Hello!")


say_hello()
```

### 10.3 观察者模式

观察者模式在状态监控和事件处理的情况中很有用。用这一模式可以让指定的对象被未知一组“管擦者”对象所监控

**核心对象中的值一旦被修改，就会自动调用观察者来执行任务**

### 10.4 观察者模式例子

观察者模式可以用于被反系统。

`property`的用法忘记的话可以往上面翻翻

代码思路：核心对象的值一旦被修改，就会调用`__update_observer()`函数，`__update_observer()`会遍历所有的观察者，并通过`__call__`方法直接调用，即观察者的接口在`__call__`函数中，后续的逻辑操作就可以在这里面扩展

```python
class Inventory:
    def __init__(self):
        self.observer = []
        self.__product = None
        self.__quantity = 0

    def attach(self, observer):
        self.observer.append(observer)

    @property
    def product(self):
        return self.__product

    @product.setter
    def product(self, value):
        self.__product = value
        self.__update_observer()

    @property
    def quantity(self):
        return self.__quantity

    @quantity.setter
    def quantity(self, value):
        self.__quantity = value
        self.__update_observer()

    def __update_observer(self):
        for observer in self.observer:
            observer()


class ConsoleObserver:
    def __init__(self, inventory):
        self.inventory = inventory

    # 当类被当成函数执行的时候，自动调用
    def __call__(self, *args, **kwargs):
        print(self.inventory.product)
        print(self.inventory.quantity)


if __name__ == '__main__':
   i = Inventory()
   c = ConsoleObserver(i)
   i.attach(c)
   i.product = "widget"

   i.quantity = 5

# 输出：
widget
0
widget
5
```

## 11. 测试面向对象程序

### 11.1 单元测试

上下文管理器让我们可以用正常的方式来写代码（直接调用函数或执行代码）。而不需要用另外一个函数来打包

```py
import unittest


def average(seq):
    return sum(seq / len(seq))


class TestAverage(unittest.TestCase):
    # 这两个方法写法不一样，但是本质上是相同的，可以选择自己喜欢的方式来写
    def test_zero(self):
        self.assertRaises(ZeroDivisionError, average, [])

    # 上下文管理器写法
    def test_with_zero(self):
        with self.assertRaises(ZeroDivisionError):
            average([])


if __name__ == '__main__':
    unittest.main()

```

其他常用断言方法：

| 方法                                                         |                             说明                             |
| :----------------------------------------------------------- | :----------------------------------------------------------: |
| assertGreater<br />assertGreaterEqual<br />assertLess<br />assertLessEqual<br /> |        接收两个可比较对象，并按照方法名的含义进行比较        |
| assertIn<br />assertNotIn                                    |             确保元素存在（或不存在）于容器对象中             |
| assertIsNone<br />assertIsNotNone                            |         确保元素是（或不是）None（而不是其他非真值）         |
| assertSameElements                                           |           确保两个容器对象包含相同的元素，不计顺序           |
| assertSequenceEqual<br />assertDictEqual<br />assertSetEqual<br />assertListEqual<br />assertTupleEqual | 确保两个容器拥有同样的元素和顺序。如果不同则列出不同之处。后面4个方法还会比较元素容器类型 |


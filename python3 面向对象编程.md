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

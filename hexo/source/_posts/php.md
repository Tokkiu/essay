---
title: 听说php是最好的语言？
date: 2016-08-03 21:36:38
tags:
---
# php你为何这么吊
## $
变量就是$加字母
所以在一个php里会看到很多$
有种jquery即视感
一个php文件的格式如下

    <?php
        phpinfo()
    ?>

## "."
"."可以表示字符串的相加

    $a="m";

那么

    $a."n" =>"mn"

## function(){}

作为一门脚本语言，这方面php和js很像，直接声明

    function myfunc(){
      dothings();
    }

但有一点不同的是，php的一个全局变量要被子作用域里的函数访问时，要加

    global $x;

比较奇特的是，php里只要是不在函数里定义的变量，都是全局的。
所有的全局变量被保存在一个数组中$GLOBALS[index]

用static的话，执行该函数时，里面的变量不会被重新赋值，延用上一次的值，就不需要递归传值了

    static $m=0;
    $m++;

## echo
echo是php里输出东西，用来调试的一个东西
echo后面加变量或直接加字符串都行
尤其在今天的nginx里，
不知道怎么用log_format来格式化我要输出的变量，
直接echo
然后这个变量的值就被扔到response里面了，
也是一个看log偏方

    $m=0;
    $cars=array("a","b");
    echo "m=$m";
    echo "car is {$cars[0]}";
## 读写文件
> 读取／创建

    $myfile = fopen("newfile.txt", "w");

如果这个文件不存在，那么就会创建这样一个文件
> 写入

    $txt = "Bill Gates\n";
    fwrite($myfile, $txt);
    $txt = "Steve Jobs\n";
    fwrite($myfile, $txt);

2次写入不会覆盖，加\n就会把指针指到下一行
但如果这个文件之前有东西，那么这个文件之前的内容会被重写。

> 读入

fread函数读取打开的文件，第一个参数是文件名，第二个参数规定读多少，如下会全部读进来

    fread($myfile,filesize("webdictionary.txt"));

fgets()会从文件读取单行，调用fgets函数后，文件指针会移动到下一行。

    $myfile = fopen("webdictionary.txt", "r") or die("Unable to open file!");
    echo fgets($myfile);

feof()会检查是否已经到达文件的最后

    while(!feof($myfile)) {
    echo fgets($myfile) . "<br>";
    }

fgetc函数读取单字符

    echo fgetc($myfile);

> 删除文件

unlink函数
如果成功删除就返回true
如果失败就返回false

    if (file_exists($myfile)) {
      $result=unlink ($myfile);
      echo $result;
    }   

## 时间戳

    time()
## 随机数

    rand(min,max);

## 转换为string

    $mystr=(string)$mystr;
    
## 和js的基情
今天在做php发邮件的时候（是的，这个还没做完），老大说可以用escape来做，一查发现卧槽这是js的方法啊，一查卧槽php和js还可以交互的，同是脚本，不过网上的例子都很丑

    <script type="text/javascript">
      function myfunc(){
        alert("i'm js");
      }
    </script>

    <?php echo "<script type="text/javascript>myfunc()</script>"?>

听老朱一说才恍然大悟，这其实不能算正经交互，“这个原理是服务器解析php代码渲染成html代码”，“等js跑的时候php已经结束工作了”，“已经到网页交给浏览器了”，“这就跟用eval（）运行js代码一样丑陋”，一波吐槽以后，搞明白了，果然语言之间交互还是不那么容易的

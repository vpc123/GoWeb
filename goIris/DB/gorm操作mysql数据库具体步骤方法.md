### gorm操作mysql的原子操作

作者:流火夏梦                     时间:2019-07-25

##### 1   下载gorm：

> $go get -u github.com/jinzhu/gorm

##### 2    在项目中引入gorm：

> `import` `(`
> 
> `"github.com/jinzhu/gorm"`
> 
> `_` `"github.com/jinzhu/gorm/dialects/mysql"`
> 
> `)`

##### 3    定义db连接信息

> //测试数据库连接操作  
> db, err := gorm.Open("mysql", "root:123456@tcp(192.168.126.10:3306)/gogs?charset=utf8")  
> //一个坑，不设置这个参数，gorm会把表名转义后加个s，导致找不到数据库的表  
> db.SingularTable(true)
> 
> if err != nil {  
>    fmt.Println(err)  
>    return  
> }else {  
>    fmt.Println("connection succedssed")  
> }  
> 
> //关闭数据库的连接
> 
> defer db.Close()

由于grom是使用的orm映射，所以需要定义要操作的表的model，在go中需要定义一个struct， struct的名字就是对应数据库中的表名，注意gorm查找struct名对应数据库中的表名的时候会默认把你的struct中的大写字母转换为小写并加上“s”，所以可以加上 db.SingularTable(true) 让grom转义struct名字的时候不用加上s。我是提前在数据库中创建好表的然后再用grom去查询的，也可以用gorm去创建表，我感觉还是直接在数据库上创建，修改表字段的操作方便，grom只用来查询和更新数据。

假设数据库中的表已经创建好，下面是数据库中的建表语句：

> ``CREATE TABLE `xz_auto_server_conf` (``
> 
> `` ` ```id``` ` int(11) NOT NULL AUTO_INCREMENT,``
> 
> `` `group_zone` varchar(32) NOT NULL COMMENT`` `'大区例如：wanba,changan,aiweiyou,360'``,`
> 
> `` `server_id` int(11) DEFAULT`` `'0'` `COMMENT` `'区服id'``,`
> 
> `` `server_name` varchar(255) NOT NULL COMMENT`` `'区服名称'``,`
> 
> `` `open_time` varchar(64) DEFAULT NULL COMMENT`` `'开服时间'``,`
> 
> `` `service` varchar(30) DEFAULT NULL COMMENT`` `'环境，test测试服，formal混服，wb玩吧'``,`
> 
> `` `username` varchar(100) DEFAULT NULL COMMENT`` `'data管理员名称'``,`
> 
> `` `submit_date` datetime DEFAULT NULL COMMENT`` `'记录提交时间'``,`
> 
> `` `status` tinyint(2) DEFAULT`` `'0'` `COMMENT` `'状态，0未处理，1已处理，默认为0'``,`
> 
> ``PRIMARY KEY (` ```id``` `)``
> 
> `) ENGINE=InnoDB DEFAULT CHARSET=utf8;`

定义model，即struct, 定于struct时我们可以只定义我们需要从数据库中取回的特定字段：

gorm在转义表名的时候会把stuct的大写字母(首字母除外) 替换成“_”，所以下面的"XzAutoServerConf "会转义成数数据库中对应“xz_auto_server conf”的表名, 对应的字段名的查找会先按照tag里面的名称去里面查找，如果没有定义标签则按照struct定义的字段查找，查找的时候struct字段中的大写会被转义成“ ”,例“GroupZone”会去查找表中的group_zone字段

> `//``定义struct`
> 
> `type` `XzAutoServerConf struct {`
> 
> ``GroupZone string `gorm:```"column:group_zone"``` ` ``
> 
> `ServerId int`
> 
> `OpenTime string`
> 
> `ServerName string`
> 
> `Status int`
> 
> `}`
> 
> `//``定义数据库连接`
> 
> `type` `ConnInfo struct {`
> 
> `MyUser string`
> 
> `Password string`
> 
> `Host string`
> 
> `Port int`
> 
> `Db string`
> 
> `}`
> 
> `func main () {`
> 
> `cn := ConnInfo{`
> 
> `"root"``,`
> 
> `123456",`
> 
> `"127.0.0.1"``,`
> 
> `3306,`
> 
> `"xd_data"``,`
> 
> `}`
> 
> `db := DbConn(cn.MyUser,cn.Password,cn.Host,cn.Db,cn.Port)`
> 
> `defer db.Close()` `//` `关闭数据库链接，defer会在函数结束时关闭数据库连接`
> 
> `var rows []api.XzAutoServerConf`
> 
> `//select`
> 
> `db.Where(``"status=?"``, 0).Select([]string{``"group_zone"``,` `"server_id"``,` `"open_time"``,` `"server_name"``}).Find(&rows)`
> 
> `//update`
> 
> `err := db.Model(&rows).Where(``"server_id=?"``, 80).Update(``"status"``, 1).Error`
> 
> `if` `err !=nil {`
> 
> `fmt``.Println(err)`
> 
> `}`
> 
> `fmt``.Println(rows)`
> 
> `}`

更多grom操作可以参考：[https://jasperxu.github.io/gorm-zh/](https://jasperxu.github.io/gorm-zh/)

**下面是Golang GORM使用**

**1  gorm**

gorm是go语言中实现数据库访问的ORM（对象关系映射）库。使用这个库，我们可以利用面向对象的方法，更加方便的对数据库中的数据进行CRUD(增删改查)。

**2 基本使用**

下载依赖

> `go get github.com``/jinzhu/gorm`
> 
> `go get github.com``/go-sql-driver/mysql`

第一个是核心库。  
第二个是mysql驱动包。

**3 连接数据库**

> `packae main`
> 
> `import` `(`
> 
> `"github.com/jinzhu/gorm"`
> 
> `_` `"github.com/jinzhu/gorm/dialects/mysql"`
> 
> `"fmt"`
> 
> `)`
> 
> `func main() {`
> 
> `db, err := gorm.Open(``"mysql"``,`
> 
> `"root:root@/test?charset=utf8&parseTime=True&loc=Local"``)`
> 
> `if` `err != nil {`
> 
> `fmt``.Println(err)`
> 
> `return`
> 
> `}``else` `{`
> 
> `fmt``.Println(``"connection succedssed"``)`
> 
> `}`
> 
> `defer db.Close()`

**4  新增数据**

> `type` `User struct {`
> 
> ``ID  int   `gorm:```"primary_key"``` ` ``
> 
> ``Name string   `gorm:```"not_null"``` ` ``
> 
> `}`
> 
> `func add() {`
> 
> `user := &User{Name:``"zhangsan"``}`
> 
> `db.Create(user)`
> 
> `}`

**5  删除数据**

> `user := &User{ID:1}`
> 
> `db.delete(user)`

**6  更新数据**

> `user := &User{ID:1}`
> 
> `db.Model(user).update(``"Name"``,``"lisi"``)`

**7  查询数据**

> `//` `query all`
> 
> `var` `users` `[]User`
> 
> `db.Find(&``users``)`
> 
> `fmt``.Println(``users``)`
> 
> `//` `query one`
> 
> `user := new (User)`
> 
> `db.First(user,1)`
> 
> `fmt``.Println(user)`

**8  其它**

判断数据库中是否有结构体对应的表：

> db.HasTable(User{})

**9  创建表**

> db.CreateTable(User{})

以上就是gorm基本的用法。

**总结**

创建连接的mysql的连接方式，通常会在一般情况下尽快的进行数据库的连接操作我们原子化数据库操作以后通过操作数据库进行项目的业务代码的开发集成设计，数据的增删改查，业务代码的架构逻辑通常都会体现在项目的开发实现过程中。代码的数据操作是非常重要的项目构建过程！！！！！！

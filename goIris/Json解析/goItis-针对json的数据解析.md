### Iris解析前台Post的Json数据



代码示例：

```
package main

import( "github.com/kataras/iris"
    "github.com/kataras/iris/middleware/logger"
    "github.com/kataras/iris/middleware/recover" ) 
    
type User struct{
    Name    string `json:"name"`
    Age        int    `json:"age"`
}

func main(){
    app := iris.New()

    app.Logger().SetLevel("debug")
    
    app.Use(recover.New())
    app.Use(logger.New())
    
    app.Post("/user", func(ctx iris.Context){
        c := &User{} if err := ctx.ReadJSON(c); err != nil{
            panic(err.Error())
        }else{
            ctx.JSON(c)
        }
    })
    
    app.Run(iris.Addr(":8080"), iris.WithoutServerError(iris.ErrServerClosed))
}

```

逻辑解释问题:

定义需要解析的json数据结构，每次接受的参数不同需要传递的模板数据结构也要根据实际情况进行相应的调整！通过Post的方式提交json的数据格式，在后台进行数据参数的解析和数据库的读写。



前台提交的数据格式:

> {"name":"天宝资源人力1","age":22}



#### 总结:

一般情况下数据的开发测试，前后端的参数对接展示一般都使用json数据格式进行展示和数据格式的表现展示和沟通，我们在数据的表现展示层页面进行数据的传递和数据层面展示。json数据格式的对接针对标准化的数据接口至关重要，所以我们通过标准化层面的接口测试就至关重要了。

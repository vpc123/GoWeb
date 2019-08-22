#### 将结构体转为json串

```
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
// 这里的两个字段名，首字母都要大写，否则无法转换
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    // 创建一个结构体对象
    person := Person{"小明", 18}
    result, err := json.Marshal(&person)
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(string(result))
}
```

#### 将json串转为结构体

```
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    // 初始化一个json串
    jsonStr := `{"name":"小明","age":18}`
    var person Person
    json.Unmarshal([]byte(jsonStr), &person)
    fmt.Println(person)
    // 将name和age都改为首字母大写
    jsonStr2 := `{"Name":"小明","Age":18}`
    var p2 Person
    json.Unmarshal([]byte(jsonStr2), &p2)
    fmt.Println(p2)
}
```

>  总结:

goWeb开发的过程中，json和结构体格式的互相转换通常需要进行大量的操作进行数据格式的操作从而进行数据通信的构造和通信传输，json数据的解析也是非常重要的一种参考形式。在web开发过程中需要熟练掌握json文件的解读和使用过程。

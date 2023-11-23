---
title: go常用文件操作 1
---

### 创建空文件

{% codeblock lang:golang %}
package main

import (
    "log"
    "os"
)

var (
    newFile *os.File
    err     error
)

func main() {
    newFile, err = os.Create("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    log.Println(newFile)
    newFile.Close()
}
{% endcodeblock %}

### Truncate裁剪文件

{% codeblock lang:golang %}
package main

import (
    "log"
    "os"
)

func main() {
// 裁剪一个文件到100个字节。
// 如果文件本来就少于100个字节，则文件中原始内容得以保留，剩余的字节以null字节填充。
// 如果文件本来超过100个字节，则超过的字节会被抛弃。
// 这样我们总是得到精确的100个字节的文件。
// 传入0则会清空文件。

    err := os.Truncate("test.txt", 100)
    if err != nil {
        log.Fatal(err)
    }
}

{% endcodeblock %}

### 获取文件信息

{% codeblock lang:golang %}
package main

import (
    "fmt"
    "log"
    "os"
)

var (
    fileInfo os.FileInfo
    err      error
)

func main() {
// 如果文件不存在，则返回错误
    fileInfo, err = os.Stat("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("File name:", fileInfo.Name())
    fmt.Println("Size in bytes:", fileInfo.Size())
    fmt.Println("Permissions:", fileInfo.Mode())
    fmt.Println("Last modified:", fileInfo.ModTime())
    fmt.Println("Is Directory: ", fileInfo.IsDir())
    fmt.Printf("System interface type: %T\n", fileInfo.Sys())
    fmt.Printf("System info: %+v\n\n", fileInfo.Sys())
}

{% endcodeblock %}

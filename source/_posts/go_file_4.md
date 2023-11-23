---
title: go常用文件操作 2
---

### 创建硬链接和软链接
#### 一个普通的文件是一个指向硬盘的inode的地方。硬链接创建一个新的指针指向同一个地方。只有所有的链接被删除后文件才会被删除。硬链接只在相同的文件系统中才工作。你可以认为一个硬链接是一个正常的链接。

symbolic link，又叫软连接，和硬链接有点不一样，它不直接指向硬盘中的相同的地方，而是通过名字引用其它文件。他们可以指向不同的文件系统中的不同文件。并不是所有的操作系统都支持软链接。

{% codeblock lang:golang %}
package main

import (
    "os"
    "log"
    "fmt"
)

func main() {
// 创建一个硬链接。
// 创建后同一个文件内容会有两个文件名，改变一个文件的内容会影响另一个。
// 删除和重命名不会影响另一个。
    err := os.Link("original.txt", "original_also.txt")
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("creating sym")
// Create a symlink
    err = os.Symlink("original.txt", "original_sym.txt")
    if err != nil {
        log.Fatal(err)
    }

// Lstat返回一个文件的信息，但是当文件是一个软链接时，它返回软链接的信息，而不是引用的文件的信息。
// Symlink在Windows中不工作。
    fileInfo, err := os.Lstat("original_sym.txt")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Link info: %+v", fileInfo)

//改变软链接的拥有者不会影响原始文件。
    err = os.Lchown("original_sym.txt", os.Getuid(), os.Getgid())
    if err != nil {
        log.Fatal(err)
    }
}
{% endcodeblock %}

### 复制文件

{% codeblock lang:golang %}
package main

import (
    "os"
    "log"
    "io"
)

func main() {
// 打开原始文件
    originalFile, err := os.Open("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer originalFile.Close()

// 创建新的文件作为目标文件
    newFile, err := os.Create("test_copy.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer newFile.Close()

// 从源中复制字节到目标文件
    bytesWritten, err := io.Copy(newFile, originalFile)
    if err != nil {
        log.Fatal(err)
    }
    log.Printf("Copied %d bytes.", bytesWritten)

// 将文件内容flush到硬盘中
    err = newFile.Sync()
    if err != nil {
        log.Fatal(err)
    }
}

{% endcodeblock %}

### 跳转到文件指定位置(Seek)

{% codeblock lang:golang %} 
package main

import (
    "os"
    "fmt"
    "log"
)

func main() {
    file, _ := os.Open("test.txt")
    defer file.Close()

// 偏离位置，可以是正数也可以是负数
    var offset int64 = 5

// 用来计算offset的初始位置
// 0 = 文件开始位置
// 1 = 当前位置
// 2 = 文件结尾处
    var whence int = 0
    newPosition, err := file.Seek(offset, whence)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Just moved to 5:", newPosition)

// 从当前位置回退两个字节
    newPosition, err = file.Seek(-2, 1)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Just moved back two:", newPosition)

// 使用下面的技巧得到当前的位置
    currentPosition, err := file.Seek(0, 1)
    fmt.Println("Current position:", currentPosition)

// 转到文件开始处
    newPosition, err = file.Seek(0, 0)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Position after seeking 0,0:", newPosition)
}

{% endcodeblock %}

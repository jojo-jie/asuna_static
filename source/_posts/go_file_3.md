---
title: go常用文件操作 3
---

### 检查文件是否存在

{% codeblock lang:golang %}
package main

import (
    "log"
    "os"
)

var (
    fileInfo *os.FileInfo
    err      error
)

func main() {
// 文件不存在则返回error
    fileInfo, err := os.Stat("test.txt")
    if err != nil {
        if os.IsNotExist(err) {
            log.Fatal("File does not exist.")
        }
    }
    log.Println("File does exist. File information:")
    log.Println(fileInfo)
}

{% endcodeblock %}

### 检查读写权限

{% codeblock lang:golang %}
package main

import (
    "log"
    "os"
)

func main() {
// 这个例子测试写权限，如果没有写权限则返回error。
// 注意文件不存在也会返回error，需要检查error的信息来获取到底是哪个错误导致。
    file, err := os.OpenFile("test.txt", os.O_WRONLY, 0666)
    if err != nil {
        if os.IsPermission(err) {
            log.Println("Error: Write permission denied.")
        }
    }
    file.Close()

// 测试读权限
    file, err = os.OpenFile("test.txt", os.O_RDONLY, 0666)
    if err != nil {
        if os.IsPermission(err) {
            log.Println("Error: Read permission denied.")
        }
    }
    file.Close()
}

{% endcodeblock %}

### 改变权限、拥有者、时间戳

{% codeblock lang:golang %}
package main

import (
    "log"
    "os"
    "time"
)

func main() {
// 使用Linux风格改变文件权限
    err := os.Chmod("test.txt", 0777)
    if err != nil {
        log.Println(err)
    }

// 改变文件所有者
    err = os.Chown("test.txt", os.Getuid(), os.Getgid())
    if err != nil {
        log.Println(err)
    }

// 改变时间戳
    twoDaysFromNow := time.Now().Add(48 * time.Hour)
    lastAccessTime := twoDaysFromNow
    lastModifyTime := twoDaysFromNow
    err = os.Chtimes("test.txt", lastAccessTime, lastModifyTime)
    if err != nil {
        log.Println(err)
    }
}

{% endcodeblock %}

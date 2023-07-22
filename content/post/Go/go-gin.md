# Gin

[Gin官网](https://gin-gonic.com/zh-cn/)

Gin 是一个用 Go (Golang) 编写的 HTTP Web 框架

## 安装

使用 `go get -u` 下载最新的 gin 包, 同时把依赖信息写入 go.mod

```bash
 $ go env -w GOPROXY=https://goproxy.cn,direct             # 设置 go 下载源为国内源
 $ go get -u github.com/gin-gonic/gin                      # -u 强制使用网络下载安装 Gin 依赖包
 
 $ go list -m all | grep gin
 > github.com/gin-gonic/gin v1.9.1
```

## 示例

```bash
 $ go mod init web                                         # 初始化 web 项目
 $ touch main.go                                           # 创建项目入口文件
 $ go get -u github.com/gin-gonic/gin                      # 安装 gin 框架

 $ ls -l
 total 24
 > -rw-r--r-- 1 root root  2265 Jul 19 02:45 go.mod
 > -rw-r--r-- 1 root root 15259 Jul 19 02:45 go.sum
 > -rw-r--r-- 1 root root   234 Jul 19 03:18 main.go
```

编辑 main.go 文件，添加代码

```go
package main

import (
    "github.com/gin-gonic/gin"                             // 引入 gin 
    "net/http"
)

func main() {
    engine := gin.Default()                                // 实例化 engine 对象
    engine.GET("/", webRoot)                               // 注册路由 / 及其处理函数
    engine.GET("/index", webIndex)                         // 注册路由 /index 及其处理函数
    engine.Run(":8080")                                    // 启动服务, 监听 8080 端口
}

func webRoot(context *gin.Context) {
    context.String(http.StatusOK, "this is root page")
}

func webIndex(context *gin.Context) {
    context.String(http.StatusOK, "this is index page")
}
```

执行 `main.go`

```bash
 $ go run main.go
 > ......
 > [GIN-debug] GET    /                         --> main.webRoot (3 handlers)
 > [GIN-debug] GET    /index                    --> main.webIndex (3 handlers)
 > ......
 > [GIN-debug] Listening and serving HTTP on :8080
```

## 路由参数

Github 个人主页 `http://github.com/{username}`, 替换不同用户名即可进入不同用户的 Github 主页
网址中的 `username` 就是路由参数, 后台服务拿到 `username` 的实际值然后返回该用户的信息

Gin 通过 Param() 或 Params 获取路由中的参数

```go
func (c *Context) Param(key string) string                 // Param 返回 string 类型

type Params []Param
func (ps Params) ByName(name string) (va string)           // Params 的参数查找方法
func (ps Params) Get(name string) (string, bool)           // Params 的参数查找方法
```

```go
func main() {
    ...
    engine.GET("/index/:id", routeParam)                   // 注册路由和对应函数
    engine.GET("/group/:name", routeParams)
    ...
}

func routeParam(c *gin.Context) {                          // index 任意子界面
    id := c.Param("id")                                    // Param 获取路由参数的值
    c.String(200, "id: %s", id)
}

func routeParams(c *gin.Context) {                         // group 任意子界面
    n, err := c.Params.Get("name")                         // Params.Get 获取路由参数值
    m := c.Params.ByName("name")                           // Params.ByName 获取路由参数值
    c.String(200, "n: %s  m: %s", n, m)
}
```

## Query 参数

网址 `https://github.com/facsert?tab=repositories` 中 `?` 后的便是 Quary 参数, 参数形式 `key=value`, 通过 & 分隔

Gin 通过以下方法获取 query 参数

```go
func (c *Context) GetQuery(key string) (string, bool)
func (c *Context) Query(key string) string
func (c *Context) DefaultQuery(key, defaultValue string) string

func (c *Context) GetQueryArray(key string) ([]string, bool)
func (c *Context) QueryArray(key string) []string
```

```go
func main() {
    ...
    engine.GET("/index/", routeQuerys)                     // 注册路由和对应函数
    engine.GET("/group/", routeQuery)
    ...
}
                                                   
func routeQuerys(c *gin.Context) {                         // http://localhost:8080/index?id=4
    id := c.QueryArry("id")                                // 获取 queray 参数值列表
    c.String(200, "id: %v", id)                            // id: [4]
}

func routeQuery(c *gin.Context) {                          // http://localhost:8080/group?name=jack 
    n, err := c.DefaultQuery("name", "nobody")             // 未获取到则使用默认值 nobody
    m := c.Query("name")                                   // query 获取参数值
    c.String(200, "n: %s  m: %s", n, m)                    // n: jack  m: jack
}
```

---
date: 2018-10-10T00:11:02+01:00
title: 入门
weight: 10
---

## 网络应用快速入门

This section will show you how to create and run a simple hiboot application in 3 steps. Let’s get started!

### 获取代码

```bash
go get -u https://github.com/hidevopsio/hiboot-web-app-demo

cd $GOPATH/src/github.com/hidevopsio/hiboot-web-app-demo

```

You can checkout each commit to see the exact stop,

```bash
commit 63cfe8eae52046b92dbf2d0bb7290ab0c45bb823
Author: John Deng <john.deng@qq.com>
Date:   Fri Oct 26 19:47:46 2018 +0800

    Step 3, adding Hiboot controller

commit 346d1546a27ae5755e89f6e9251a9d3a4f1b7f5c
Author: John Deng <john.deng@qq.com>
Date:   Fri Oct 26 18:53:13 2018 +0800

    Step 2, adding some starters

commit 620631e17d567f96169c32e7cec7a7c9ed3139ce
Author: John Deng <john.deng@qq.com>
Date:   Fri Oct 26 18:36:24 2018 +0800

    step 1: Writing the first Hiboot web application
```

## - 第一步, 开始我们第一个Hiboot网络应用程序

To write Hiboot application, as we know, the executable commands must always use package main, so we need to create the main package first.

See [Effective GO](https://golang.org/doc/effective_go.html#names) to learn more about Go's naming conventions.

```bash
git reset --hard 620631e17d567f96169c32e7cec7a7c9ed3139ce
```

## - 第二步, 添加 Starter

Here we are going to add starter [actuator](https://github.com/hidevopsio/hiboot/tree/master/pkg/starter/actuator) and [logging](https://github.com/hidevopsio/hiboot/tree/master/pkg/starter/logging).

```bash
git reset --hard 346d1546a27ae5755e89f6e9251a9d3a4f1b7f5c
```

## - 第三步, 添加 Controller

```bash
git reset --hard 63cfe8eae52046b92dbf2d0bb7290ab0c45bb823
```

### 编写代码

通过以上三步，你可以看到如何快速来编写第一个Hiboot网络应用程序

```go

package main

import (
	"github.com/hidevopsio/hiboot/pkg/app/web"
	"github.com/hidevopsio/hiboot/pkg/app"
	"github.com/hidevopsio/hiboot/pkg/starter/actuator"
	"github.com/hidevopsio/hiboot/pkg/starter/logging"
	"github.com/hidevopsio/hiboot/pkg/at"
)

// Controller Rest Controller with path /
// RESTful Controller, derived from web.Controller. The context mapping of this controller is '/' by default
type Controller struct {
	// at.RestController or web.Controller must be embedded here
	at.RestController
}

// Get GET /
// Get method, the context mapping of this method is '/' by default
// the Method name Get means that the http request method is GET
func (c *Controller) Get() string {
	// response
	return "My first Hiboot web application"
}

func main()  {
	web.NewApplication(new(Controller)).
		SetProperty(app.ProfilesInclude, actuator.Profile, logging.Profile).
		Run()
}

```

This section will show you how to create and run a simple hiboot application. Let’s get started!

### 获取代码

```bash
go get -u github.com/hidevopsio/hiboot

cd $GOPATH/src/github.com/hidevopsio/hiboot/examples/web/helloworld/

```

### 示例

Below is the simplest web application in Go.

```go

// Package helloworld provides the quick start web application example
// main package
package main

// import web starter from hiboot
import (
	"github.com/hidevopsio/hiboot/pkg/app/web"
	"github.com/hidevopsio/hiboot/pkg/at"
)

// Controller Rest Controller with path /
// RESTful Controller, derived from web.Controller. The context mapping of this controller is '/' by default
type Controller struct {
	// at.RestController or web.Controller must be embedded here
	at.RestController
}

// Get GET /
// Get method, the context mapping of this method is '/' by default
// the Method name Get means that the http request method is GET
func (c *Controller) Get() string {
	// response
	return "hello"
}

// main function
func main() {
	// create new web application and run it
	web.NewApplication(new(Controller)).Run()
}

```

#### 运行应用

```bash
dep ensure

go run main.go
```

#### 测试接口

```bash
curl http://localhost:8080/
```

Output:

```bash
Hello, world
```

## 命令行应用快速入门

Writing Hiboot cli application is as simple as web application, you can take the advantage of dependency injection introduced by Hiboot.

```go

// import cli starter and fmt
import (
	"fmt"
	"github.com/hidevopsio/hiboot/pkg/app"
	"github.com/hidevopsio/hiboot/pkg/app/cli"
)

// define the command
type rootCommand struct {
	// embedding cli.RootCommand in each command
	cli.RootCommand

	// persistant flag to
	to string
}

func newRootCommand() *rootCommand {
	c := new(rootCommand)
	c.Use = "hello"
	c.Short = "hello command"
	c.Long = "run hello command for getting started"
	c.Example = `
hello -h : help
hello -t John : say hello to John
`
	c.PersistentFlags().StringVarP(&c.to, "to", "t", "world", "e.g. --to=world or -t world")
	return c
}

// Run run the command
func (c *rootCommand) Run(args []string) error {
	fmt.Printf("Hello, %v\n", c.to)
	return nil
}

// main function
func main() {
	// create new cli application and run it
	cli.NewApplication(newRootCommand).
		SetProperty(app.PropertyBannerDisabled, true).
		Run()
}

```

### 运行命令行应用

```bash
dep ensure

go run main.go
```

```bash
Hello, world
```

### 编译及运行

```bash
go build
```

Let's get help

```bash
./hello --help
```

```bash
run hello command for getting started

Usage:
  hello [flags]

Flags:
  -h, --help        help for hello
  -t, --to string   e.g. --to=world or -t world (default "world")

```

Greeting to Hiboot

```bash
./hello --to Hiboot
```

```bash
Hello, Hiboot
```

---
date: 2018-10-10T00:11:02+01:00
title: 入门
weight: 10
---

## 网络应用快速入门

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
	// embedding cli.BaseCommand in each command
	cli.BaseCommand
	To string
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
	c.PersistentFlags().StringVarP(&c.To, "to", "t", "world", "e.g. --to=world or -t world")
	return c
}

// Run run the command
func (c *rootCommand) Run(args []string) error {
	fmt.Printf("Hello, %v\n", c.To)
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

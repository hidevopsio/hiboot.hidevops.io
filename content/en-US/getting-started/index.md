---
date: 2018-10-10T00:11:02+01:00
title: Getting started
weight: 10
---

## Quick start web application

This section will show you how to create and run a simple hiboot application in 3 steps. Let’s get started!

### Get the source code

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

## - Step 1, writing the first Hiboot web application

To write Hiboot application, as we know, the executable commands must always use package main, so we need to create the main package first.

See [Effective GO](https://golang.org/doc/effective_go.html#names) to learn more about Go's naming conventions.

```bash
git reset --hard 620631e17d567f96169c32e7cec7a7c9ed3139ce
```

## - Step 2, adding some starters

Here we are going to add starter [actuator](https://github.com/hidevopsio/hiboot/tree/master/pkg/starter/actuator) and [logging](https://github.com/hidevopsio/hiboot/tree/master/pkg/starter/logging).

```bash
git reset --hard 346d1546a27ae5755e89f6e9251a9d3a4f1b7f5c
```

## - Step 3, adding Hiboot controller

```bash
git reset --hard 63cfe8eae52046b92dbf2d0bb7290ab0c45bb823
```

### Writing the code

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
		SetProperty(app.PropertyAppProfilesInclude, actuator.Profile, logging.Profile).
		Run()
}

```

#### Run web application

```bash
dep ensure

go run main.go
```

#### Testing the API by curl

```bash
curl http://localhost:8080/
```

Output:

```bash
My first Hiboot web application
```

## Quick start cli application

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
	// embedding cli.RootCommand
	cli.RootCommand
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

### Run cli application

```bash
dep ensure

go run main.go
```

```bash
Hello, world
```

### Build the cli application and run

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

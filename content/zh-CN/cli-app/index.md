---
date: 2018-10-10T00:11:02+01:00
title: 命令行应用
weight: 30
tags: ["cli", "web", "application"]
---

## 关于命令行应用

Hiboot cli application is built based on top of [cobra](https://github.com/spf13/cobra) with Hiboot dependency injection and auto configuration.

## 功能列表

* Dependency Injection
* Sub command handler

## 命令行应用项目结构

```bash
.
├── cmd
│   ├── bar.go
│   ├── foo.go
│   ├── root.go
│   ├── root_test.go
│   └── second.go
├── config
│   ├── autoconfigure.go
│   └── autoconfigure_test.go
├── main.go
├── main_test.go
└── model
    ├── foo.go
    └── foo_test.go

```

Just like web application, Hiboot cli application is designed to be simpler and easier.

```go

package main

import (
	"github.com/hidevopsio/hiboot/examples/cli/advanced/cmd"
	"github.com/hidevopsio/hiboot/examples/cli/advanced/config"
	"github.com/hidevopsio/hiboot/pkg/app"
	"github.com/hidevopsio/hiboot/pkg/app/cli"
)

func main() {
	// create new cli application and run it
	cli.NewApplication(cmd.NewRootCommand).
		SetProperty(app.PropertyAppProfilesInclude, config.Profile).
		Run()
}

```

## 根命令

```go

package cmd

import (
	"github.com/hidevopsio/hiboot/pkg/app/cli"
	"github.com/hidevopsio/hiboot/pkg/log"
)

// RootCommand is the root command
type RootCommand struct {
	cli.RootCommand

	profile string
	timeout int
}

// NewRootCommand the root command
func NewRootCommand(second *secondCommand) *RootCommand {
	c := new(RootCommand)
	c.Use = "first"
	c.Short = "first command"
	c.Long = "Run first command"
	c.ValidArgs = []string{"baz"}
	pf := c.PersistentFlags()
	pf.StringVarP(&c.profile, "profile", "p", "dev", "e.g. --profile=test")
	pf.IntVarP(&c.timeout, "timeout", "t", 1, "e.g. --timeout=1")
	c.Add(second)
	return c
}

// Run root command handler
func (c *RootCommand) Run(args []string) error {
	log.Infof("handle first command: profile=%v, timeout=%v", c.profile, c.timeout)
	return nil
}

```

## 子命令

```go


package cmd

import (
	"github.com/hidevopsio/hiboot/pkg/app"
	"github.com/hidevopsio/hiboot/pkg/app/cli"
	"github.com/hidevopsio/hiboot/pkg/log"
)

type secondCommand struct {
	cli.SubCommand
}

func init() {
	app.Register(newSecondCommand)
}

func newSecondCommand(foo *fooCommand, bar *barCommand) *secondCommand {
	c := new(secondCommand)
	c.Use = "second"
	c.Short = "second command"
	c.Long = "Run second command"
	c.Add(foo, bar)
	return c
}

func (c *secondCommand) Run(args []string) error {
	log.Info("handle second command")
	return nil
}


```
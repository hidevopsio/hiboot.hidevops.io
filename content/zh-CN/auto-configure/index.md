---
title: "自动配置"
date: 2018-10-22T16:22:23+08:00
tags: ["auto-configuration", "starter", "go"]
menu:
  docs:
    parent: "Hiboot云原生应用框架"
    weight: 5
    title: "自动配置"
---

## 自动配置 Starter

Hiboot 自动配置会根据你在项目中添加的依赖项来自动配置Hiboot应用，例如：如果在 main.go 中导入了 bolt 依赖，并且你没有手动配置任何数据库连接，这时Hiboot 会自动配置一个bolt数据库以供服务层自动注入。

为了实现自动配置，你需要将 app.Configuration 插入到你的配置中，并且在配置包的 init 函数中调用app.Register() 方法，如下：

详情请参考: https://godoc.org/hidevops.io/hiboot/pkg/starter

## 创建你自己的 Starter

一个完整的 Hiboot starter 库应包含以下结构体：
- autoconfigure - 处理自动配置代码的对象
- properties - 一些属性集的对象，这些属性被设置为可配置的默认值或者用户指定的值
如果你所在的公司需要开发团队共享的go包，或者你有自己的开源或者商业项目，你可能会需要开发一个你自己的自动配置 starter。starter 可以在外部包中实现，然后导入到任何的go应用中使用。

认识自动配置 Starter

自动配置在底层使用标准的结构体和额外嵌入的app.Configuration 实现。AutoConfiguration 常常会约束什么时候使用自动配置，通常auto-configuration
结构体使用`after:"fooConfiguration"` or `missing:"fooConfiguration"` 标记来保证在有关的配置被加载后或者没有手动相关配置时进行自动配置

## 代码示例

下面的例子说明了怎样创建一个自定义的自动配置
详情请参考: https://hidevops.io/hiboot-data/blob/master/starter/bolt/autoconfigure.go

```go
package bolt

import (
	"hidevops.io/hiboot/pkg/app"
	"os"
)

// properties
type properties struct {
	Database string      `json:"database" default:"hiboot.db"`
	Mode     os.FileMode `json:"mode" default:"0600"`
	Timeout  int64       `json:"timeout" default:"2"`
}

type Properties struct {
	at.ConfigurationProperties `value:"bolt"`  // 这里使用来注解 at.ConfigurationProperties

	Database string      `json:"database"`
	Mode     os.FileMode `json:"mode"`
	Timeout  int64       `json:"timeout"`
}

// declare boltConfiguration
// 声明 boltConfiguration
type boltConfiguration struct {
	at.Configuration
    
	BoltProperties *properties
}

// BoltRepository
type BoltRepository struct {
}

func init() {
    // 将 boltConfiguration 注册为 AutoConfiguration
	app.Register(boltConfiguration)
}

// boltConfiguration constructor
func newBoltConfiguration() *boltConfiguration {
	return &boltConfiguration{}
}

func (c *boltConfiguration) BoltRepository() *BoltRepository {

	repo := &BoltRepository{}

    // 根据 c.BoltProperties 配置仓库
	return repo
}

```
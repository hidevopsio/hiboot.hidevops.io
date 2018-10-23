---
date: 2018-10-10T00:11:02+01:00
title: 网络应用
weight: 20
tags: ["web", "application"]
---

## 主要功能

* MVC架构 (Model-View-Controller).
* 自动配置, 事先配置好的依赖包可以被注入到任何你指定的构造函数参数中或者结构体变量
* 依赖注入， 使用标签 \`inject:""\` 后构造函数实现.

## MVC架构介绍

Hiboot点MVC架构采用约定俗成的原则，尽量隐藏和业务无关的代码，开发者只需遵循少数几个简单的规则即可。

* 运行时动态配置及配置值注入
* 注册机制
* 依赖注入

在[入门](/cn/getting-started/)这个章节我们了解到了简单Hiboot网络应用，现在我们以[hiboot-data gorm 示例代码](https://github.com/hidevopsio/hiboot-data/tree/master/examples/gorm)为例，来详细讲解任何有效的基于Hiboot来编程。

## MVC项目结构详解

下面是典型的hiboot MVC项目结构，接下来我们来对每个文件一一做详细说明。

```bash
.
├── config
│   ├── application-gorm.yml
│   ├── application-local.yml
│   ├── application.yml
│   ├── i18n
│   │   ├── en-US.ini
│   │   └── zh-CN.ini
│   ├── keygen.sh
│   └── ssl
│       ├── app.rsa
│       └── app.rsa.pub
├── main.go
├── main_test.go
├── controller
│   ├── user.go
│   └── user_test.go
├── entity
│   ├── user.go
│   └── user_test.go
└── service
    ├── user.go
    └── user_test.go
```

Hiboot应用分两部分，外部配置及源代码。

### config

Hiboot要做可用于生产的应用框架，在设计之初就考虑到了需要适应不同的环境。config文件夹包含了不同环境下的配置文件

### config/application.yml

`application.yml`定义应用项目、名称等基本信息

```yaml
app:
  project: examples  
  name: gorm-demo
  profiles:
    include:
    - actuator
    - locale
    - logging
    - gorm

logging:
  level: info

```

#### application.yml字段说明

|字段|描述|合法值|示例|
|---|---|---|---|
|app.project|项目名称|任意字符串|examples|
|app.name|应用名称|任意字符串|gorm-demo|
|profiles.include|starter的开关功能，⚠️ 如果没有包含进来，则该starter不会被初始化，相关依赖不能被注入|starter包名|actuator, locale, logging, gorm|
|logging.level|定义日志级别|debug,info,warn,error,fatal|info|

**任何以某个字符名称为后缀的配置文件，有两种情况:**

1. 事先定义了相关环境变量，Hiboot会认为当前应用运行在不同的环境下，如`config/application-local.yml`为本地开发环境，在本地电脑需要设置环境变量`APP_PROFILES_ACTIVE` 为 `local`
2. 引用了starter，可以将starter的属性值配置到`application.yml`, 也可以将其配置到以starter包名为后缀的配置文件中，如本例的`application-gorm.yml`

### config/application-local.yml

```yaml

server:
  port: 8081

logging:
  level: debug

```

#### application-local.yml 字段说明

|字段|描述|合法值|示例|
|---|---|---|---|
|server.port|监听端口, 通常在本地开发应用时可能有多个应用同时运行，为例防止端口冲突，可以定义不同的端口|任意数字|8081|
|logging.level|定义日志级别|debug,info,warn,error,fatal|info|

### config/application-gorm.yml

```yaml
gorm:
  type: mysql
  host: mysql-${app.profiles.active}
  port: 3306
  database: ${app.name:test}
  username: demo
  password: fafUJCsVXf2Thj0d4n6UqNdX2PfI08fMyaNlrZhbJVVkghnZ+Zc/WCdITXflJpHZjYH5+LbLviy/6j9etPNwtdyAOXiqKI62itC6nDgp0Xlzu0qX8MwMIIAosUwaYpnflg23hRZnueKrq6SrEpkx4X+LWluDgHb2O5VfGGvHliE=
  charset: utf8
  parseTime: true
  loc: Asia/Shanghai
  config:
    decrypt: true
```

#### application-gorm.yml字段说明

|字段|描述|合法值|示例|
|---|---|---|---|
|gorm.type|数据库类型, 支持的有多种数据库|mysql,postgres,sqlite3,mssql|mysql|
|gorm.host|数据库地址，允许使用变量 `mysql-${app.profiles.active}` 如在本地开发环境，设置环境变量`APP_PROFILES_ACTIVE` 为 `local`，则实际值为`mysql-local`，添加一条DNS的A记录即可|任何合法DNS域名或IP地址（字符串类型）|`mysql-${app.profiles.active}`|
|gorm.port|数据库端口|任何有效端口|3306|
|gorm.database|数据库名称|字符或变量|`${app.name:test}`|
|gorm.username|数据库用户名|合法的数据库用户名|dbuser|
|gorm.password|数据库密码，如果gorm.config.decrypt为true，则为[crypto](https://github.com/hidevopsio/crypto)加密过的数据库密码|字符串|fafUJ ... O5VfGGvHliE=|
|gorm.charset|数据库字符集|utf8,ascii|utf8|
|gorm.parseTime|是否解析时间|true,false|true|
|gorm.loc|本地时区|参考[标准时区](https://www.worldtimezone.com/index_cn.php)|Asia/Shanghai|
|gorm.config.decrypt|是否加密密码|true,false|true|

### main.go

和任何Go语言应用一样，Hiboot的程序入口为main包，包含两部分：引人用到的依赖包以及一个main函数。

1. 为了解耦包与包之间的依赖关系，hiboot规定，依赖项采用注册，依赖注入的方式来解耦，故在main包里想要匿名引入MVC控制器`github.com/hidevopsio/hiboot-data/examples/gorm/controller`, 如果使用到了其它第三方自动配置包（这里一般是指starter），而代码没有显式使用的，也要匿名引人，如：`github.com/hidevopsio/hiboot/pkg/starter/actuator`, `github.com/hidevopsio/hiboot/pkg/starter/locale`, `github.com/hidevopsio/hiboot/pkg/starter/logging`

2. main函数非常简单，只有一行代码 `web.NewApplication().Run()`, `web`包引自`github.com/hidevopsio/hiboot/pkg/app/web`

```go
package main

import (
	_ "github.com/hidevopsio/hiboot-data/examples/gorm/controller"
	"github.com/hidevopsio/hiboot/pkg/app/web"
	_ "github.com/hidevopsio/hiboot/pkg/starter/actuator"
	_ "github.com/hidevopsio/hiboot/pkg/starter/locale"
	_ "github.com/hidevopsio/hiboot/pkg/starter/logging"
)

func main() {
	web.NewApplication().Run()
}
```

#### mian_test.go

main函数单元测试，`TestRunMain`第一行代码是 `go main()`, 我们起一个go routine来无阻塞的测试main函数，后面代码`time.Sleep(200 * time.Millisecond)`做个简单延时，可以用于main函数代码覆盖测试。

```go
package main

import (
	"testing"
	"time"
)

func TestRunMain(t *testing.T) {
	go main()
	time.Sleep(200 * time.Millisecond)
}
```

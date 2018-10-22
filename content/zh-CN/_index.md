---
date: 2018-10-08T21:07:13+01:00
title: Hiboot 框架
type: index
tags: ["hiboot", "framework"]
weight: 1
---

<p align="center">
  <a href="https://travis-ci.org/hidevopsio/hiboot?branch=master">
    <img src="https://travis-ci.org/hidevopsio/hiboot.svg?branch=master" alt="Build Status"/>
  </a>
  <a href="https://codecov.io/gh/hidevopsio/hiboot">
    <img src="https://codecov.io/gh/hidevopsio/hiboot/branch/master/graph/badge.svg" />
  </a>
  <a href="https://opensource.org/licenses/Apache-2.0">
      <img src="https://img.shields.io/badge/License-Apache%202.0-green.svg" />
  </a>
  <a href="https://goreportcard.com/report/github.com/hidevopsio/hiboot">
      <img src="https://goreportcard.com/badge/github.com/hidevopsio/hiboot" />
  </a>
  <a href="https://godoc.org/github.com/hidevopsio/hiboot">
      <img src="https://godoc.org/github.com/golang/gddo?status.svg" />
  </a>
  <a href="https://gitter.im/hidevopsio/hiboot">
      <img src="https://img.shields.io/badge/GITTER-join%20chat-green.svg" />
  </a>
</p>

## 关于 

Hiboot 是一高性能网络及命令行工具应用框架，通过Go语言实现的类似Spring Boot的网络或命令行客户端的应用框架，Hiboot提供了Web MVC框架，支持依赖注入，依赖自动配置，提供类似Spring Boot提供的Starter。

Hiboot并不打算重复造轮子，故使用了自动配置的机制，很轻松的集成第三依赖包，使得其更方便使用，如连接数据库，不需要在业务代码中创建数据库连接，你只要依赖[hiboot-data/starter/gorm](https://github.com/hidevopsio/hiboot-data/tree/master/starter/gorm)即可直接实现数据库查询。

## 总览

* 网络应用 MVC (Model-View-Controller).
* 自动配置, 事先配置好的依赖可以注入到任何你指定的构造函数参数中或者结构体变量
* 依赖注入， 使用标签 \`inject:""\` 后构造函数.

## 功能列表

* Apps - 应用
    * cli - 命令行应用
    * web - 网络应用
* Starters - 自动配置
    * actuator - 健康检测
    * locale - 国际化
    * logging - 日志
    * jwt - JWT 令牌
    * grpc - 服务间通讯gRPC
* Tags - 标签
    * inject - 注入依赖
    * default - 注入默认值
    * value - 注入常量，环境变量或引用值
* Utils - 工具
    * cmap - 支持并发的map
    * copier - 复制struct工具
    * crypto - aes, base64, md5, and rsa 加／解密工具
    * gotest - go test 工具
    * idgen - twitter snowflake 唯一 id 生成器
    * io - 文件操作工具
    * mapstruct - 转换map 到 struct
    * replacer - 替换struct中的值为引用或环境变量
    * sort - 切片排序
    * str - 字符串处理工具
    * validator - 参数校验
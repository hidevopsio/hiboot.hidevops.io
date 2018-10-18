---
date: 2016-03-08T21:07:13+01:00
title: Hiboot Framework
type: index
weight: 0
---

## Hiboot - High performance web and cli application framework with dependency injection

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


Hiboot is a cloud native web and cli application framework written in Go.

Hiboot is not trying to reinvent everything, it integrates the popular libraries but make them simpler, easier to use. It borrowed some of the Spring features like dependency injection, aspect oriented programming, and auto configuration. You can integrate any other libraries easily by auto configuration with dependency injection support.

## Overview

* Web MVC (Model-View-Controller).
* Auto Configuration, pre-create instance with properties configs for dependency injection.
* Dependency injection with struct tag name `inject:""` or Constructor func.

## Features


* **Apps**
    * cli - command line application
    * web - web application

* **Starters**
    * actuator - health check
    * locale - locale starter
    * logging - customized logging settings
    * jwt - jwt starter
    * grpc - grpc application starter

* **Tags** 
    * inject - inject generic instance into object
    * default - inject default value into struct object 
    * value - inject string value or references / variables into struct string field

* **Utils** 
    * cmap - concurrent map
    * copier - copy between struct
    * crypto - aes, base64, md5, and rsa encryption / decryption
    * gotest - go test util
    * idgen - twitter snowflake id generator
    * io - file io util
    * mapstruct - convert map to struct
    * replacer - replacing stuct field value with references or environment variables
    * sort - sort slice elements
    * str - string util enhancement util
    * validator - struct field validation 
       


---
date: 2018-10-10T00:11:02+01:00
title: Web Application
weight: 20
tags: ["cli", "web", "application"]
---

## Features

* *Web MVC (Model-View-Controller).
* Auto Configuration, pre-created instance with properties configs for dependency injection.
* Dependency injection with the struct tag \`inject:""\` or the constructor.
  
## Introduction to Hiboot MVC

Hiboot prefers to hide and business-independent code, so that the developers will concentrate the business logic.

unlike most of the Go web frameworks, Hiboot does not need to setup routes. Hiboot use reflection to construct the routes.

## Project structure

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

Hiboot application include source code and config (config is optional)

### config

### config/application.yml

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

### config/application-local.yml

```yaml

server:
  port: 8081
logging:
  level: debug

```

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

### main.go

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

### mian_test.go

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

```go

package controller

import (
	"github.com/hidevopsio/hiboot-data/examples/gorm/entity"
	"github.com/hidevopsio/hiboot-data/examples/gorm/service"
	"github.com/hidevopsio/hiboot/pkg/app/web"
	"github.com/hidevopsio/hiboot/pkg/model"
	"github.com/hidevopsio/hiboot/pkg/utils/copier"
	"net/http"
	"github.com/hidevopsio/hiboot/pkg/app"
)

type userRequest struct {
	model.RequestBody
	Id       uint64 `json:"id"`
	Name     string `json:"name" validate:"required"`
	Username string `json:"username" validate:"required"`
	Password string `json:"password" validate:"required"`
	Email    string `json:"email" validate:"required,email"`
	Age      uint   `json:"age" validate:"gte=0,lte=130"`
	Gender   uint   `json:"gender" validate:"gte=0,lte=2"`
}

// RestController
type userController struct {
	web.Controller
	userService service.UserService
}

func init() {
	app.Register(newUserController)
}
// newUserController inject userService automatically
func newUserController(userService service.UserService) *userController {
	return &userController{
		userService: userService,
	}
}

// Post POST /user
func (c *userController) Post(request *userRequest) (model.Response, error) {
	var user entity.User
	copier.Copy(&user, request)
	err := c.userService.AddUser(&user)
	response := new(model.BaseResponse)
	response.SetData(user)
	return response, err
}

// GetById GET /id/{id}
func (c *userController) GetById(id uint64) (response model.Response, err error) {
	user, err := c.userService.GetUser(id)
	response = new(model.BaseResponse)
	if err != nil {
		response.SetCode(http.StatusNotFound)
	} else {
		response.SetData(user)
	}
	return
}

// GetById GET /id/{id}
func (c *userController) GetAll() (response model.Response, err error) {
	users, err := c.userService.GetAll()
	response = new(model.BaseResponse)
	response.SetData(users)
	return
}

// DeleteById DELETE /id/{id}
func (c *userController) DeleteById(id uint64) (response model.Response, err error) {
	err = c.userService.DeleteUser(id)
	response = new(model.BaseResponse)
	return
}

```

### entity/user.go

```go

package entity

type User struct {
	Id       uint64 `json:"id"`
	Name     string `json:"name"`
	Username string `json:"username"`
	Password string `json:"password"`
	Email    string `json:"email"`
	Age      uint   `json:"age"`
	Gender   uint   `json:"gender"`
}

func (u *User) TableName() string {
	return "user"
}

```

### Model -  service/user.go

```go

package service

import (
	"errors"
	"github.com/hidevopsio/hiboot-data/examples/gorm/entity"
	"github.com/hidevopsio/hiboot-data/starter/gorm"
	"github.com/hidevopsio/hiboot/pkg/app"
	"github.com/hidevopsio/hiboot/pkg/utils/idgen"
)

type UserService interface {
	AddUser(user *entity.User) (err error)
	GetUser(id uint64) (user *entity.User, err error)
	GetAll() (user *[]entity.User, err error)
	DeleteUser(id uint64) (err error)
}

type userServiceImpl struct {
	// add UserService, it means that the instance of UserServiceImpl can be found by UserService
	UserService
	repository gorm.Repository
}

func init() {
	// register UserServiceImpl
	app.Register(newUserService)
}

// will inject BoltRepository that configured in github.com/hidevopsio/hiboot/pkg/starter/data/bolt
func newUserService(repository gorm.Repository) UserService {
	repository.AutoMigrate(&entity.User{})
	return &userServiceImpl{
		repository: repository,
	}
}

func (s *userServiceImpl) AddUser(user *entity.User) (err error) {
	if user == nil {
		return errors.New("user is not allowed nil")
	}
	if user.Id == 0 {
		user.Id, _ = idgen.Next()
	}
	err = s.repository.Create(user).Error()
	return
}

func (s *userServiceImpl) GetUser(id uint64) (user *entity.User, err error){
	user = &entity.User{}
	err = s.repository.Where("id = ?", id).First(user).Error()
	return
}

func (s *userServiceImpl) GetAll() (users *[]entity.User, err error) {
	users = &[]entity.User{}
	err = s.repository.Find(users).Error()
	return
}

func (s *userServiceImpl) DeleteUser(id uint64) (err error) {
	err = s.repository.Where("id = ?", id).Delete(entity.User{}).Error()
	return
}

```

### Run the sample code

```bash
go run main.go
```

Output:

```bash
______  ____________             _____
___  / / /__(_)__  /_______________  /_
__  /_/ /__  /__  __ \  __ \  __ \  __/
_  __  / _  / _  /_/ / /_/ / /_/ / /_     Hiboot Application Framework
/_/ /_/  /_/  /_.___/\____/\____/\__/     https://github.com/hidevopsio/hiboot
[INFO] 2018/10/23 23:37 Starting Hiboot web application gorm-demo on localhost with PID 28423
[INFO] 2018/10/23 23:37 Working directory: /Users/johnd/.gvm/pkgsets/go1.10/hidevops/src/github.com/hidevopsio/hiboot-data/examples/gorm
[INFO] 2018/10/23 23:37 The following profiles are active: local, [actuator locale logging gorm]
[INFO] 2018/10/23 23:37 Auto configure gorm starter
[INFO] 2018/10/23 23:37 Auto configure locale starter
[INFO] 2018/10/23 23:37 Auto configure logging starter
[INFO] 2018/10/23 23:37 The dependency graph resolved successfully
[INFO] 2018/10/23 23:37 connected to dataSource demo@mysql-local:3306/gorm_demo
[DBUG] 2018/10/23 23:36 GET: /health -> github.com/hidevops	io/hiboot-data/vendor/github.com/hidevopsio/hiboot/pkg/starter/actuator/controller/healthController.Get() and 2 more
[DBUG] 2018/10/23 23:36 DELETE: /user/id/{id} -> github.com/hidevopsio/hiboot-data/examples/gorm/controller/userController.DeleteById() and 2 more
[DBUG] 2018/10/23 23:36 GET: /user/id/{id} -> github.com/hidevopsio/hiboot-data/examples/gorm/controller/userController.GetById() and 2 more
[DBUG] 2018/10/23 23:36 GET: /user/all -> github.com/hidevopsio/hiboot-data/examples/gorm/controller/userController.GetAll() and 2 more
[DBUG] 2018/10/23 23:36 POST: /user -> github.com/hidevopsio/hiboot-data/examples/gorm/controller/userController.Post() and 2 more
Now listening on: http://localhost:8080
Application started. Press CMD+C to shut down.
```

### Make a request on the RESTful API GET /user/all

```bash
http GET localhost:8080/user/all
```

```bash

HTTP/1.1 200 OK
Content-Length: 307
Content-Type: application/json; charset=UTF-8
Date: Tue, 23 Oct 2018 15:38:41 GMT
Set-Cookie: app.language=; Path=/; Expires=Tue, 23 Oct 2018 17:38:41 GMT; Max-Age=7200; HttpOnly
{
    "code": 200,
    "data": [
        {
            "age": 18,
            "email": "john.doe@gmail.com",
            "gender": 0,
            "id": 209536579658580081,
            "name": "John Doe",
            "password": "poi321",
            "username": "johnd"
        },
        {
            "age": 25,
            "email": "mike.phil@gmail.com",
            "gender": 0,
            "id": 209536656246571121,
            "name": "Mike Phil",
            "password": "iutg039",
            "username": "mikep"
        }
    ],
    "message": "Success"
}

```
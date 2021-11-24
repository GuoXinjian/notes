
# 通用CURD方法 

database.go

```go
package database

import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)
var MysqlDB *gorm.DB

func MysqlInit() error {
    var err error
    MysqlDB, err = gorm.Open(mysql.Open(Host), &gorm.Config{})
    if err != nil {
        panic(err)
    }
    return err
}
```

basemodel.go

```go
package model

import (
    "app/database"
)

type TableNameAble interface {
    TableName() string
}

func Create(value TableNameAble) error {
    err := database.MysqlDB.Create(value).Error
    return err
}
```

user.go

```go
package model

import (
    "gorm.io/gorm"
)

type User struct {
    gorm.Model
    Name string
    Age int
}
func (User) TableName() string {
    return "User"
}
```

controller.go 以Gin为例

```go
package controller

import (
    "app/model"
    "github.com/gin-gonic/gin"
)

func Create(c *gin.Context) {
    var user model.User
    user.Name = "username"
    user.Age = age
    err := model.Create(&user)
    if err != nil {
        c.JSON(500, gin.H{
            "message": "create failed",
        })
        return
    }
    c.JSON(200, gin.H{
        "message": "create success",
    })
}
```

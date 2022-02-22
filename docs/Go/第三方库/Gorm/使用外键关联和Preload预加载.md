# 使用Preload进行预加载

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

user.go

```go
package model

import (
    "gorm.io/gorm"
)

type Class struct {
    gorm.Model
    Name string
}

type Student struct {
    gorm.Model
    Name string
    Age int
    ClassID uint
    Class Class `gorm:"foreignkey:ClassID"`
}

type Parent struct {
    gorm.Model
    Name string
    StudentID uint
    Student Student `gorm:"foreignkey:StudentID"`
}
```

controller.go 以Gin为例

```go
package controller

import (
    "app/model"
    "github.com/gin-gonic/gin"
)


func GetList(c *gin.Context) {
    var parent model.Parent
    parent.Name = "parentname"
    err := database.MysqlDB.Preload("Student").Preload("Student.Class").Find(&parent).Error
    if err != nil {
        c.JSON(500, gin.H{
            "message": "get list failed",
        })
        return
    }
    c.JSON(200, gin.H{"parent": parent})
```

结果

```json
{
    "parent": {
        "ID": 1,
        "Name": "parentname",
        "Student": {
            "ID": 1,
            "Name": "studentname",
            "Age": 18,
            "Class": {
                "ID": 1,
                "Name": "classname"
            }
        }
    }    
}


```

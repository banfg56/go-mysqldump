# Go MYSQL Dump
Create MYSQL dumps in Go without the `mysqldump` CLI as a dependancy.

## 改造内容

> 08.16.2022改造

1. 备份文件名传入时就确定，不再使用time 去格式化(因为某些表名带特殊数字，格式会被转义)
2. 对备份表史进行 ``引用，防止一些使用了关键词的表不能被正常操作

> 03.28.2023 改造

1. 备份表内容时，对单引号进行转义

### Simple Example
```go
package main

import (
	"database/sql"
	"fmt"

	"github.com/JamesStewy/go-mysqldump"
	_ "github.com/go-sql-driver/mysql"
)

func main() {
	// Open connection to database
  username := "your-user"
  password := "your-pw"
  hostname := "your-hostname"
  port := "your-port"
	dbname := "your-db"

  dumpDir := "dumps"  // you should create this directory
  dumpFilenameFormat := fmt.Sprintf("%s-20060102T150405", dbname)   // accepts time layout string and add .sql at the end of file

	db, err := sql.Open("mysql", fmt.Sprintf("%s:%s@tcp(%s:%s)/%s", username, password, hostname, port, dbname))
	if err != nil {
		fmt.Println("Error opening database: ", err)
		return
	}

	// Register database with mysqldump
	dumper, err := mysqldump.Register(db, dumpDir, dumpFilenameFormat)
	if err != nil {
		fmt.Println("Error registering databse:", err)
		return
	}

	// Dump database to file
	resultFilename, err := dumper.Dump()
	if err != nil {
		fmt.Println("Error dumping:", err)
		return
	}
	fmt.Printf("File is saved to %s", resultFilename)

	// Close dumper and connected database
	dumper.Close()
}

```

[![GoDoc](https://godoc.org/github.com/JamesStewy/go-mysqldump?status.svg)](https://godoc.org/github.com/JamesStewy/go-mysqldump)
[![Build Status](https://travis-ci.org/JamesStewy/go-mysqldump.svg?branch=master)](https://travis-ci.org/JamesStewy/go-mysqldump)

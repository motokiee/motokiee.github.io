---
layout: post
title:  "Goでルーティング"
date:   2017-02-06 20:30:00 +0900
categories: blog
tags: golang
---
DBと接続できたのでルーティングしてみる。

`/`と`/user`にクエリをつけて検索するだけの簡単なもの。

```golang
package main

import (
	"fmt"
	"net/http"
	"net/url"
	"os"

	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {

	initDB()

	http.HandleFunc("/", hello)
	http.HandleFunc("/user", handleUser)
	fmt.Println("listnening...")
	http.ListenAndServe(":"+os.Getenv("PORT"), nil)
}

func hello(res http.ResponseWriter, req *http.Request) {
	fmt.Fprintln(res, "Hello!!")
}
func handleUser(res http.ResponseWriter, req *http.Request) {
	m, _ := url.ParseQuery(req.URL.RawQuery)
	user := loadUser(m["user_name"][0])
	fmt.Fprintln(res, user.Name)
}

func loadUser(s string) User {
	databaseURL := os.Getenv("DATABASE_URL")
	db, err := gorm.Open("postgres", databaseURL)
	if err != nil {
		fmt.Println(err)
		panic("failed to connect database")
	}

	var user User
	db.Where("name = ?", s).First(&user)
	return user
}

func initDB() {
	databaseURL := os.Getenv("DATABASE_URL")
	db, err := gorm.Open("postgres", databaseURL)
	if err != nil {
		fmt.Println(err)
		panic("failed to connect database")
	}

	db.AutoMigrate(&User{})
	db.Create(&User{Name: "Motoki Narita"})
	db.Create(&User{Name: "Swift"})
	db.Create(&User{Name: "Gopher"})
}

type User struct {
	gorm.Model
	Name string
}
```

`https://hogehoge.com/user?user_name=Gopher`で`Gopher`とか表示されるようになった。単純なことしかできなかった。

とりあえずpostgresの環境がherokuにしかないので、次はローカルでpostgresの環境構築してデバッグが捗るようにする。

たぶんPG Commanderとか使う。
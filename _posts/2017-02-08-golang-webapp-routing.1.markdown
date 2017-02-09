---
layout: post
title:  "いいやり方なのか分からないけどGoでRouting"
date:   2017-02-08 19:00:00 +0900
categories: blog
tags: golang
---
Goでルーティングを書いてみた(この前やったのをもうちょっとマシにした)。

```golang
func main() {

	initDB()

	http.HandleFunc("/", rootHandler)
	http.HandleFunc("/users", userHandler)
	fmt.Println("listnening...")
	http.ListenAndServe(":"+os.Getenv("PORT"), nil)
}
```
`usersHandler`みたいなものを作ってみた。

中身はこんな感じで、`userHandler`の方はクエリがなければ全部取ってきて名前だけを返す。

```golang
func rootHandler(res http.ResponseWriter, req *http.Request) {
	fmt.Fprintln(res, "Hello!!")
}
func userHandler(res http.ResponseWriter, req *http.Request) {
	m, _ := url.ParseQuery(req.URL.RawQuery)
	if len(m) == 0 {
		t := ""
		us := loadAllUsers()
		for _, u := range us {
			t += u.Name + "\n"
		}
		fmt.Fprintln(res, t)
	} else {
		user := loadUser(m["user_name"][0])
		fmt.Fprintln(res, user.Name)
	}
}
```
Goのfor文を書く時は`range`が便利。Swiftで言うところの`enumrated()`と同じでインデックスと要素を返してくれる。`_`で変数を捨てられるところもSwiftと一緒で覚えやすかった。

今日やった方法が良いのかは分からないけど、ひとまずできた。
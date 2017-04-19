---
Description: bcrypt useage
Keywords:
- Development
- bcrypt
Tags:
- bcrypt
Title: bcrypt golang example
Topics:
- bcrypt
date: 2016-12-28
---


```
package main

import (
	"fmt"
	"golang.org/x/crypto/bcrypt"
)

func main() {
	password := []byte("test001")
	hashedPassword, err := bcrypt.GenerateFromPassword(password, bcrypt.DefaultCost)
	if err != nil {
		panic(err)
	}
	fmt.Println(string(hashedPassword))

	err = bcrypt.CompareHashAndPassword(hashedPassword, password)
	fmt.Println(err)

}
```

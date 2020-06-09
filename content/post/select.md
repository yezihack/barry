---
title: "golang select用法"
date: 2020-06-05T10:59:18+08:00
lastmod: 2020-06-05T10:59:18+08:00
draft: true
tags: ["golang", "select", "chan"]
categories: ["golang"]
author: "百里"
comment: true
toc: true
reward: true
# weight: 1
# description = ""
---

## select 

用于chan通道专用的控制结构

```
ch := make(chan bool)
select {
case c <- ch:
	fmt.Println("hello world")
default:
	return
}
```

## 使用误区

1. 39行, return 会一直阻塞? 希望大神解释下?
2. 如果return 换成break或continue就不会阻塞

```
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"log"
	"math/rand"
	"sync"
)

type Cache struct {
	ch chan int
}
var (
	_cache *Cache
	_once sync.Once
)
func NewCache() *Cache {
	_once.Do(func() {
		_cache = &Cache{
			ch: make(chan int, 10),
		}
		_cache.monitor()
	})
	return _cache
}
func (c *Cache) Push(x int) {
	c.ch <- x

}
func (c *Cache) monitor() {
	go func() {
		for {
			select {
			case result, ok := <-c.ch:
				n := c.N()
				log.Printf("result:%d, ok:%v, n:%d\n", result, ok, n)
				if n == 1 {
					return // 如果换成return 会一直阻塞. 这是为什么?
				}
				if ok {
					log.Println("-----------result:", result)
				}
			}
		}
	}()
}
func (c *Cache) N() int {
	return rand.Intn(2)
}

func main() {
	log.SetFlags(log.Lshortfile)
	r := gin.Default()
	r.GET("/add", func(c *gin.Context) {
		NewCache().Push(rand.Intn(1000))
		NewCache().Push(rand.Intn(1000))
		NewCache().Push(rand.Intn(1000))
		NewCache().Push(rand.Intn(1000))
		c.JSON(200, gin.H{
			"x":"ok",
		})
	})
	r.Run(":8080")
}

```





## 参考

1. [Go 语言设计与实现 5.2 select](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-select/)

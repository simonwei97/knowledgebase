---
date: 2024-01-02
categories:
  - Golang
search:
  boost: 2
hide:
  - footer
---

## 2 个 goroutine 交替打印奇数和偶数

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	wg := sync.WaitGroup{}
	ch1 := make(chan struct{})
	ch2 := make(chan struct{})
	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 1; i < 100; i += 2 {
			<-ch1
			fmt.Println("g1: ", i)
			ch2 <- struct{}{}
		}
		<-ch1 // 读走最后一个从协程2传入的，防止死锁
	}()

	go func() {
		defer wg.Done()
		for i := 2; i <= 100; i += 2 {
			<-ch2
			fmt.Println("g2: ", i)
			ch1 <- struct{}{}
		}
	}()

	ch1 <- struct{}{}
	wg.Wait()
}

```

## 2 个 goroutine 交替打印字母和数字

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var numChan, strChan = make(chan struct{}), make(chan struct{})
	var wg sync.WaitGroup

	wg.Add(2)

	// 打印数字
	go func() {
		defer wg.Done()
		for i := 1; i <= 26; i++ {
			<-numChan             // 阻塞直到字母被打印后 numChan写入
			fmt.Printf("%v", i)   /// 打印数字
			strChan <- struct{}{} // strChan写入，打印字母的协程的strChan取出，才会打印字母
		}
		<-numChan // 读走最后一个从协程2传入的，防止死锁
	}()

	// 打印字母
	go func() {
		defer wg.Done()
		for i := 65; i <= 90; i++ {
			<-strChan
			fmt.Printf("%v", string(rune(i)))
			numChan <- struct{}{}
		}
	}()

	numChan <- struct{}{}
	wg.Wait()
}
```

运行结果

```bash
1A2B3C4D5E6F7G8H9I10J11K12L13M14N15O16P17Q18R19S20T21U22V23W24X25Y26Z
```

## 2 个 goroutine 交替打印 AB

```go
package main

import (
	"fmt"
	"sync"
)

func main() {

	wg := sync.WaitGroup{}
	c1 := make(chan int, 1)
	c2 := make(chan int)
	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-c1
			fmt.Print("A")
			c2 <- 1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-c2
			fmt.Print("B")
			c1 <- 1
		}
	}()

	c1 <- 1
	wg.Wait()
}

```

结果输出

```bash
ABABABABABABABABABAB
```

## 3 个 goroutine 交替打印 ABC

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var (
		ch1 = make(chan struct{})
		ch2 = make(chan struct{})
		ch3 = make(chan struct{})
        wg  = sync.WaitGroup{}
	)
	wg.Add(3)
	go func(s string) {
		defer wg.Done()
		for i := 0; i <= 10; i++ {
			<-ch1
			fmt.Print(s)
			ch2 <- struct{}{}
		}
		<-ch1
	}("A")

	go func(s string) {
		defer wg.Done()
		for i := 0; i <= 10; i++ {
			<-ch2
			fmt.Print(s)
			ch3 <- struct{}{}
		}
	}("B")

	go func(s string) {
		defer wg.Done()
		for i := 0; i <= 10; i++ {
			<-ch3
			fmt.Print(s)
			ch1 <- struct{}{}
		}
	}("C")

	ch1 <- struct{}{}
	wg.Wait()
}

```

结果输出

```bash
ABCABCABCABCABCABCABCABCABCABCABC
```

## 使用多协程并发地按照顺序打印字母表

> [[Goroutine]使用多协程并发地按照顺序打印字母表](https://juejin.cn/post/7262243685898436667)

```go
package main

import (
	"fmt"
	"sync"
)

func printAlphabet(letter rune, prevCh, nextCh chan struct{}, wg *sync.WaitGroup) {
	defer wg.Done()
	for i := 0; i < 3; i++ {
		<-prevCh
		fmt.Printf("%c", letter)
		nextCh <- struct{}{}
	}
	// 第一个协程必须要退出，因为最后一个协程往管道里面写入数据了，需要破环而出不然就会死锁
	if letter == 'a' {
		<-prevCh
	}
}

func main() {
	var wg sync.WaitGroup
	wg.Add(26)

	var signals []chan struct{}
	for i := 0; i < 26; i++ {
		signals = append(signals, make(chan struct{}))
	}

	for letter, i := 'a', 0; letter <= 'z'; letter++ {
		if letter == 'z' {
			go printAlphabet(letter, signals[i], signals[0], &wg)
			break
		}
		go printAlphabet(letter, signals[i], signals[i+1], &wg)
		i++
	}

	// 启动第一个协程
	signals[0] <- struct{}{}
	wg.Wait()
}

```

```bash
abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyz
```

## N 个 groutine 交替打印 1-100

> [多个协程交替打印](https://juejin.cn/post/7134373047468818439)

1. 启动 N 个协程，共用一个外部变量计数器，计数器范围是 1 到 100
2. 开启 N 个有缓冲 chan，chans[i]塞入数据代表协程 i 可以进行打印了
3. 协程 i 一直阻塞，直到 chan[i]通道有数据可以拉，才打印

```go

package main

import "fmt"

func main() {
	goroutineNum := 5
	var chanSlice []chan int
	exitChan := make(chan int)

	for i := 0; i < goroutineNum; i++ { // 创建N个channel
		chanSlice = append(chanSlice, make(chan int, 1))
	}

	num := 1
	j := 0
	for i := 0; i < goroutineNum; i++ { // 创建N个协程
		go func(i int) {
			for {
				<-chanSlice[i] // 循环阻塞等待
				if num > 100 {
					exitChan <- 1
					break
				}

				fmt.Println(fmt.Sprintf("gorutine%v:%v", i, num))
				num++

				if j == goroutineNum-1 { // j来控制启动哪个协程来打印
					j = 0
				} else {
					j++
				}
				chanSlice[j] <- 1
			}
		}(i)
	}
	chanSlice[0] <- 1

	select {
	case <-exitChan:
		fmt.Println("end")
	}
}

```

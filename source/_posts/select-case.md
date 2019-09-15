---
title: 使用SelectCase来做不定数量的channel选择
date: 2016-05-10 00:13:33
tags: ["Golang"]
categories: ["Programming","Golang"]
---

> func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)

**Args**

- cases []SelectCase 使用此参数之前要设置SelectCase，告诉Select目的。

**Return**

- chosen int 返回选择的channel序号
- recv Value 返回信道的值，类型是reflect.Value
- recvOk bool 返回false，如果信道被关闭。

````golang
    package main
    import (
        "fmt"
        "reflect"
    )
    
    func main(){
        var n int = 1
        var chs = make([]chan int, n)
        
        var worker = func(n int, c chan int){
            for i:=0;i<n;i++{
                c<-i
            }
            close(c)
        }
        
        //不定数量的channel数组
        for i:=0;i<n;i++{
            chs[i]=make(chan int)
            go worker(3+i, chs[i])
        }
        

        var selectCase = make([]reflect.SelectCase, n)
        //将channel绑定到SelectCase
        for i:=0;i<n;i++{
            selectCase[i].Dir = reflect.SelectRecv //设置信道是接收,可以为下面值之一
            /*
            const (
                SelectSend    SelectDir // case Chan <- Send
                SelectRecv              // case <-Chan:
                SelectDefault           // default
            )
            */
            selectCase[i].Chan = reflect.ValueOf(chs[i])
        }
        
        numDone := 0
        //从所有channel中取出最先到达的N个值
        for numDone < n {
            chosen, recv, recvOk := reflect.Select(selectCase)
            if recvOk {
                fmt.Println(chosen, recv.Int(), recvOk)
            }else{
                numDone++
            }
        }
    }
````

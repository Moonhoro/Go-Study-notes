这是我参与「第三届青训营 -后端场」笔记创作活动的的第1篇笔记。

# 课前准备
[【Go 语言原理与实践学习资料】第三届字节跳动青训营-后端专场 - 掘金](https://juejin.cn/post/7093721879462019102)

[课程PPT](https://bytedance.feishu.cn/file/boxcnQnHXuDOdzd8CqVid7nQLmg?source_type=message&from=message)

[PPT中课程代码](https://github.com/wangkechun/go-by-example)
### 学习 Go 语言基础语法
[Go语言圣经](https://books.studygolang.com/gopl-zh/)

[Go by Example 中文版](https://gobyexample-cn.github.io/)
# GO环境配置
Go的环境配置网上有大量教程，照做即可，不甚了解原理，不加赘述，仅说一下遇到的问题。

在配置VScode环境时，安装插件之后，无法正常自动安装go工具，换源等教程均无果，最后自行从网上下载相关工具放入配置文件，程序能正常运行。近日学习后发现，可能是因为其他软件不具有修改c盘文件的权限，经尝试，原因并不是c盘权限，转战GoaLand(IDE天下第一)。
# 基础语法
孩子太菜，看得一知半解，还得看书，暂略，后续可能补充。
# 猜谜游戏
较简单，但是还是有问题。ppt中代码运行后，在windows系统中无法正常输入。根据万能群友，原因是在windows中换行符是/r/n。所以需要将第27行代码

`strings.TrimSuffix(input, "\n")`

改为

`strings.TrimSuffix(input, "\r\n")`

## 课后作业

```
package main

import (
   "fmt"
   "math/rand"
   //"strconv"
   //"strings"
   "time"
)

func main() {
   maxNum := 100
   rand.Seed(time.Now().UnixNano())
   secretNumber := rand.Intn(maxNum)

   fmt.Println("Please input your guess")
   //reader := bufio.NewReader(os.Stdin)
   for {
      var guess int
      _, err := fmt.Scanf("%d\n", &guess)
      if err != nil {
         fmt.Println("Invalid input. Please enter an integer value")
      }
      fmt.Println("You guess is", guess)
      if guess > secretNumber {
         fmt.Println("Your guess is bigger than the secret number. Please try again")
      } else if guess < secretNumber {
         fmt.Println("Your guess is smaller than the secret number. Please try again")
      } else {
         fmt.Println("Correct, you Legend!")
         break
      }
   }
}
```
`n, err := fmt.Scanf("%d\n", &guess)`会返回两个值n是scanf接收数值的数量，err为是否发生错误。且若不加\n ,输入时会把回车一并读入，导致接收两个数据（应该是吧，小白不是很懂）。

# 在线词典
将cURL命令转换为Go语言代码的网站[Convert curl commands to code](https://curlconverter.com/")

请求结果在Go中创建相应的结构体的工具[在线工具 - OKTools](https://oktools.net/json2go/")

按老师步骤操作即可。

## 课后作业

在经历多次尝试，我终于搞出了这个作业。并不是所有在线翻译都支持你去“白嫖”，于是它们的请求里面会要求有一个乱码（大概，个人猜测加理解），如果不含这个乱码，或者乱码不正确，均不会返回（这就是我尝试多次无法返回数据的原因，当然也是猜测），如果你付费合作，官方会给你这段乱码生成的方式（个人理解），于是，完成这个作业需要能被白嫖的在线翻译。

我使用的是[有道云翻译](https://ai.youdao.com/product-fanyi-text.s)

另一个问题，结构体转换的时候，老师演示的是复制preview的内容，这需要把小箭头全部打开，并且在第一行右键，copy object,还经常发生问题没法转换，实际上复制response的内容就可以了。

最后代码
```
func Youquery(word string) {
   client := &http.Client{}
   var data = strings.NewReader(fmt.Sprintf(`q=%s&from=en&to=zh-CHS`, word))
   req, err := http.NewRequest("POST", "https://aidemo.youdao.com/trans", data)
   if err != nil {
      log.Fatal(err)
   }
   req.Header.Set("authority", "aidemo.youdao.com")
   req.Header.Set("sec-ch-ua", `" Not A;Brand";v="99", "Chromium";v="99", "Google Chrome";v="99"`)
   req.Header.Set("accept", "application/json, text/javascript, */*; q=0.01")
   req.Header.Set("content-type", "application/x-www-form-urlencoded; charset=UTF-8")
   req.Header.Set("sec-ch-ua-mobile", "?0")
   req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36")
   req.Header.Set("sec-ch-ua-platform", `"Windows"`)
   req.Header.Set("origin", "https://ai.youdao.com")
   req.Header.Set("sec-fetch-site", "same-site")
   req.Header.Set("sec-fetch-mode", "cors")
   req.Header.Set("sec-fetch-dest", "empty")
   req.Header.Set("referer", "https://ai.youdao.com/")
   req.Header.Set("accept-language", "zh-CN,zh;q=0.9")
   resp, err := client.Do(req)
   if err != nil {
      log.Fatal(err)
   }
   defer resp.Body.Close()
   bodyText, err := ioutil.ReadAll(resp.Body)
   if err != nil {
      log.Fatal(err)
   }
   if resp.StatusCode != 200 {
      log.Fatal("bad StatusCode:", resp.StatusCode, "body", string(bodyText))
   }
   var dictResponse Jing
   err = json.Unmarshal(bodyText, &dictResponse)
   if err != nil {
      log.Fatal(err)
   }
   fmt.Println(word)
   for _, item := range dictResponse.Basic.Explains {
      fmt.Println(item)
   }
}
```

结构体不再复制。

两个翻译语言的并发，理论上在两函数前加上`go`即可，但是只有不加`go`的函数输出输出，两个都加`go`，无输出，看群中大佬代码，貌似需要建立缓存区，代码如下


```js
func main() {
	if len(os.Args) != 2 {
		fmt.Fprintf(os.Stderr, `usage: simpleDict WORD
example: simpleDict hello
		`)
		os.Exit(1)
	}
	word := os.Args[1]
	var wg sync.WaitGroup
	wg.Add(2)
	go func() {
		defer wg.Done()
		query(word)
	}()
	go func() {
		defer wg.Done()
		Youquery(word)
	}()
	wg.Wait()
}
```
完整代码可见[Github](https://github.com/Moonhoro/Go-Study-notes/blob/main/Day1%20work/simpledict.go)

# 代理服务器

孩子不懂这个是啥，但是跟着走了一遍。

遇到的问题如下图。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb79dbe973924f86a924a4a3509378dc~tplv-k3u1fbpfcp-watermark.image?)
然而没找到解决问题的方法，按理说应该是杀死某个进程，但是找不到，于是重启电脑解决。

来自万能群友的[[windows使用nc命令 - 林宇风 - 博客园]](https://www.cnblogs.com/linyufeng/p/13206252.html)

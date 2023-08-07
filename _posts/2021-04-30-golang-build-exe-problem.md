---
layout: post
title:  "记录Golang构建exe文件的两个技巧点"
date:   2021-04-30 23:13
categories: 开发 Golang
---

最近在做一个windows下定时任务的小项目，Golang写的代码，build生成exe文件，然后在windows任务计划程序中设置定时执行，发生两个问题：

# exe同目录下字体文件找不到
exe同目录下放置了一个字体文件font.ttf，在设置了定时任务执行后，输出找不到font.ttf文件，猜想是因为执行目录不在exe文件目录的原因。解决办法：

```golang
// Read the font data.
dir, _ := os.Executable()
exPath := filepath.Dir(dir)
fontBytes, err := ioutil.ReadFile(exPath + "\\font.ttf")
```

# 定时执行时，总会有一个CMD窗口闪一下
解决方法：在编译的时候，加个参数：
```shell
go build -ldflags "-H windowsgui"
```
记录一下，免得以后忘记。
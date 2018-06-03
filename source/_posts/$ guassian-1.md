---
title: 单点能(SPE)计算的结果
date: 2018-02-12 11:09:19
tags: 
- Computational Chemistry
- Gaussian
categories:
- 化学
- 量子化学计算
- 单点能
---

<font  size=4 face="黑体">更新日志</font> 

> 2018-02-12: 创建

---

> 从[上一节](https://liugaoyong.github.io/gaussian-0/)的 water 的例子我们计算了水分子，具体输入文件：![](http://p7b7this6.bkt.clouddn.com/18-6-3/82020918.jpg)
> 上面的输入就是计算了水分子的单点能（Single Point Energy，关键字 SP可省略），那么我们通过单点能计算可以得到那些结果呢？也即，从单点能计算的计算结果中，可以进行那些分析得到那些信息呢？

### 体系能量值
> 我们知道，在微观世界中，能量值得地位举足轻重。那么SPE计算得到的能量如下图示：
![](http://p7b7this6.bkt.clouddn.com/18-6-3/84397485.jpg)
> 因此，水分子的能量值 `E(RB3LYP, water) =  -76.4080189877 a.u.`
>
> 能量值为什么是负的呢？
> 我们常说，世界最高峰是珠穆朗玛峰，有多高？8844米。（参见[维基百科](https://en.wikipedia.org/wiki/Mount_Everest)）那么0米在哪里？海平面。我们不妨假设珠峰峰顶为0米，那么我们就在-8800米左右生活，我们就成了活在地底的人了。
> 因此，高度的绝对值是没有意义的，其相对值才是有意义的。
> 故而，当有其他分子与H2O比较才有意义，比如：C6H6的能量值`E(RB3LYP, benzene) =  -232.248028177 a.u.`。苯的能量低于水的，说明在一定的误差内，气态苯分子比气态水分子稳定。（该计算基于气态）
![](http://p7b7this6.bkt.clouddn.com/18-6-3/89646817.jpg)

### 



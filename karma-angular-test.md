# 使用karma进行angular测试


紧随前文[如何对Angular Controller进行单元测试](http://www.jackpu.com/ru-he-dui-angular-controllerjin-xing-dan-yuan-ce-shi/),
但是我们也提到了前文工作流程的不方便，简单总结上前文进行测试的一个流程：

+ 修改测试代码;
+ 刷新浏览器；
+ 查看测试结果；

该方法最大的缺陷就是，我们每次都得手动的去刷新我们的浏览器。但是如果我们需要测试不同的浏览器我们又应该如何处理呢？
而本文将提出一个自动化测试的方案，从而解决上诉问题。

### 关于Karma

 Karma是一个由AngularJS 团队创造的JavaScript测试工具。前面我们写过了Jasmine的一篇文章[《开始对Angular App进行单元测试》](http://www.jackpu.com/yi-kai-shi-dui-angular-appjin-xing-dan-yuan-ce-shi-1/)
而Karma提供了非常有用的工具去帮助我们进行Jasminede的测试。

### 安装 Karma

你在[这里](http://karma-runner.github.io/0.12/intro/installation.html)可以看到更为详细的安装
文章，而本文随后也会概括总结一些。首先你得安装node.js,如果安装了则跳过，没有安装的可以看[这里](https://nodejs.org/en/)

注意官方文档中有说明，Karma在某些版本的node工作不那么友好。然而作者本身的v0.12.x并没遇见什么问题。打开你的命令行软件，我们输入下面命令，

``` bash
mkdir CalculatorKarma
cd CalculatorKarma
echo {} >> package.json bash

```

接下来我么用npm安装karma

```  bash
npm install karma --save-dev
```






-----
原文地址: http://www.bradoncode.com/blog/2015/05/19/karma-angularjs-testing/
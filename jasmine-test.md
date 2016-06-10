# 使用Jasmine对你Angular App进行单元测试


原文地址:http://www.bradoncode.com/blog/2015/05/12/angularjs-testing-getting-started/

@ [Bradley Braithwaite ](http://www.bradoncode.com/about)

### 前言

在写测试之前，我们还得对基础的JS的单元测试框架和如何写测试用例等一些问题做一个简要的介绍。

先说说 [jasmine](http://jasmine.github.io/) ，它算作一个行为驱动开发(behaviour driven development,BBD)的 JavaSciprt的框架，会让人困惑的是，它也可以用测试驱动开发(test driven development,TBD)的思想来写测试。
BBD和TBD两种风格会有些区别，但是使用`jasmine`确实可以用TBD的方式。`jasmine`会提供一种结构去组织你的测试以及一些函数来进行代码输出的断言。究竟是TBD还是BBD实际取决于开发者自己的如何去组织自己的测试，当然下文我们仅仅用TBD来进行讲解。

### 开始你的第一个测试

先建一个项目文件比如`jasmine-test`,然后粘贴下面的代码到一个html文件中。
``` html
<html>
	<head>
		<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.3.3/jasmine.min.css">
		<script src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.3.3/jasmine.min.js"></script>
		<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.3.3/jasmine-html.min.js"></script>
		<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.3.3/boot.min.js"></script>
	</head>
	<body>
	</body>
	<script type="text/javascript">

		// Paste in the test code here.

	</script>
</html>
```

用浏览器打开该文件。界面如图:

<img src="https://lh6.googleusercontent.com/-Srsn43oTdzQ/VVF98lXFG_I/AAAAAAAABcs/Jn2gj6dG3u8/w636-h67-no/empty.png"/>

注意*specs*，它实际上指的是用例，它同样也是BBD的专业术语之一。截下来我们先来点简单的逻辑判断。

``` javascript
describe('calculator', function () {
	it('1 + 1 should equal 2');
});
```
将上面的代码粘贴到刚才的html文件中。

函数`describe`实际指的就是前文提到的`specs`（测试的逻辑分组），而函数`it`实际会展现这组测试。
运行结果如下图:

<img src="https://lh5.googleusercontent.com/-ZorUBMsLD_g/VVF98u9Z6sI/AAAAAAAABco/xCtUdnc68dI/w638-h95-no/pending.png" />

由于我们实际上没有添加测试的主体，因此状态上方显示的是`pending`,截下来我们会添加一段函数.

``` javascript
it('1 + 1 should equal 2', function() {
	expect(1 + 1).toBe(1);
});
```

<img src="https://lh3.googleusercontent.com/-bCmrEFvN_Uc/VVGAlPXF8JI/AAAAAAAABdQ/bVWXuq-4Jk0/w638-h251-no/failing.png"/>

上面的截图，实际上反应的就测试失败，因为`1+1`并不等于`2`。

上面出现了两个新函数`expect`以及`toBe`，`Expect`代表着预期，我们将需要使用的测试放进区，然后函数会进行运行结果的断言。而`toBe`呢则代表着匹配器,卡框架提供的匹配器还有很多，常用的大概有下面这些：

``` javascript
expect(true).toBe(true);
expect(false).not.toBe(true);
expect(1).toEqual(1);
expect('foo').toEqual('foo');
expect('foo').not.toEqual('bar');
```

我们再把这段代码改成正常的。
``` javascript
it('1 + 1 should equal 2', function() {
	expect(1 + 1).toBe(2);
});
```
刷新浏览器，我们就可以看到正确的结果了。
<img src="https://lh5.googleusercontent.com/-i0KjTQvIDRM/VVGAlFHHaqI/AAAAAAAABdM/tGASSMmbRFw/w638-h86-no/passing.png" />

前面我们大致了解了`jasmine`的基本用法，实际上测试驱动开发的的一个基本原则就是我们再写功能的同时写测试，花些时间去掌握这项技能有利于提升我们的工作效率的。

流程大致如图三个部分，我们所写的每个测试都英国是遵循这三个流程。

<img src="https://lh5.googleusercontent.com/-2avOSLXBtRo/VVF97CVqKlI/AAAAAAAABcc/wYjDPRC0CFk/w638-h86-no/Untitled%2Bdrawing.png" />

+ 红色部分:失败的测试，有利于我们消除bug;
+ 绿色部分：需要我们写足够的代码让我们的测试通过，透过前面的部分，我们可以了解这些bug;
+ 黄色部分，代码重构过程我们可以避免重蹈覆辙，并且提升安全性。


### Demo

记下来我们写一些代码去体会上面的流程

#### 写一个不通过的测试
``` js
describe('calculator', function () {
	
	it('1 + 1 should equal 2', function () {
		expect(calculator.sum(1, 1)).toBe(2);
	});

});
```
#### 2.使测试通过

如果我们开始写足够多的代码时候，其中一些可能类似下面：
``` js
var calculator = {
	sum: function(x, y) {
		return 2; // <-- note this is hardcoded
	}
}

describe('calculator', function () {
	
	it('1 + 1 should equal 2', function () {
		expect(calculator.sum(1, 1)).toBe(2);
	});

});
```
这样做有利于我们避免代码更多的错误

#### 3.重构
在我们确保我们的逻辑正常后，我们可以测试更多用例，直到结果能够一直和期望的一致。


``` js
var calculator = {
	sum: function(x, y) {
		return x + y;
	}
}

describe('calculator', function () {
	
	it('1 + 1 should equal 2', function () {
		expect(calculator.sum(1, 1)).toBe(2);
	});

});
```

### 小结
我们初步了解了使用`jasmine`来进行TBD测试，这样有利于我们接下来去理解 使用karama进行单元测试（翻译中...）


代码:https://github.com/JackPu/angular-test-tutorial/blob/master/index.html

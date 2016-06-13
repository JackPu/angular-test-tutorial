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
如果安装成功， package.json中将会有下面内容:
``` js
{
  "devDependencies": {
    "karma": "^0.12.31"
  }
}
```

接着我们最好安装下karma-cli,这样可以全局使用`karma`命令。

``` bash
npm install -g karma-cli
```

下一步我们需要安装一些karma的插件，这样可以Jasmine测试框架能够工作，并且设置Google Chrome 作为我们的测试浏览器。

``` bash
npm install karma-jasmine karma-chrome-launcher --save-dev
```
接下来，package.json会增加一些内容:
``` js
{
  "devDependencies": {
    "jasmine-core": "^2.3.4",
    "karma": "^0.12.31",
    "karma-chrome-launcher": "^0.1.12",
    "karma-jasmine": "^0.3.5"
  }
}
```

### 写测试用例

接下来我们可以开始进行测试了，我们在命令行工具里输入下面的命令:

``` bash
mkdir tests
touch tests/calculator.controller.test.js
```

我们将下面的代码粘贴到calculator.controller.test.js中去

``` js
describe('calculator', function () {

  beforeEach(module('calculatorApp'));

  var $controller;

  beforeEach(inject(function(_$controller_){
    $controller = _$controller_;
  }));

  describe('sum', function () {
		it('1 + 1 should equal 2', function () {
			var $scope = {};
			var controller = $controller('CalculatorController', { $scope: $scope });
			$scope.x = 1;
			$scope.y = 2;
			$scope.sum();
			expect($scope.z).toBe(3);
		});	
	});

});

```

### 让测试跑起来

在进行测试之前，我们创建一个配置文件用于进行karma的一些设置。关于配置的内容您可以阅读 [官方文档](http://www.bradoncode.com/blog/2015/05/19/karma-angularjs-testing/)。

接下来我们开始创建这个配置文件我们需要输入下面的命

``` bash
karma init karma.conf.js
```

然后回答命令行的问题就行了

Which testing framework do you want to use?(你所使用的测试框架？)

接受一个默认的值，比如:jasmine

Do you want to use Require.js ?(你希望使用 Require.js吗？)
接受一个默认的值,比如:no

Do you want to capture any browsers automatically ?(你想自动捕获浏览器吗？)

接受一个默认的值 比如：chrome

What is the location of your source and test files ?(你测试文件的地址)

输入下面的地址：
``` bash
tests/*.test.js
```
当然你并不需要担心你跳过哪些询问，稍后我们都可以手动修改配置文件的。

Should any of the files included by the previous patterns be excluded ?（是否有需要排除的符合前面格式的问文件）

Do you want Karma to watch all the files and run the tests on change ?(是否动态监听文件变化)

接受一个默认的值,比如:yes


接下来我们可以在根目录看到配置文件karma.conf.js创建成功。

我们输入下面的命令开始测试

``` bash
karma start karma.conf.js
```
测试后的输出就像下面这样：

``` bash
> @ test /Users/devuser/repos/CalculatorKarma
> ./node_modules/karma/bin/karma start karma.conf.js

INFO [karma]: Karma v0.12.31 server started at http://localhost:9876/
INFO [launcher]: Starting browser Chrome
INFO [Chrome 42.0.2311 (Mac OS X 10.10.3)]: Connected on socket 2absOkNfa1asasaX0fCJ with id 71338229
Chrome 42.0.2311 (Mac OS X 10.10.3) calculator encountered a declaration exception FAILED
	ReferenceError: module is not defined
	    at Suite.<anonymous> (/Users/devuser/repos/CalculatorKarma/tests/calculator.controller.test.js:3:13)
	    at /Users/devuser/repos/CalculatorKarma/tests/calculator.controller.test.js:1:1
Chrome 42.0.2311 (Mac OS X 10.10.3): Executed 1 of 1 (1 FAILED) ERROR (0.01 secs / 0.005 secs)
```
按住ctrl/command+c就可以停止这个进程。

*小提示: 我们可以在package.json中的scripts里加入测试的命令，方便实用 *

``` bash
{
	"scripts": {
    "test": "karma start karma.conf.js"
  },
  "devDependencies": {
    "jasmine-core": "^2.3.4",
    "karma": "^0.12.31",
    "karma-chrome-launcher": "^0.1.12",
    "karma-jasmine": "^0.3.5"
  }
}
```

加入折断代码后，我们可以输入`npm test`进行karma的测试了。如果我们要验证package.json是否
修改正确


### 通过测试并添加新功能

接下来我们添加一些controller的逻辑进去.

``` bash
mkdir app
touch app/calculator.controller.js
```

然后我们需要下载angular的类库文件和angular mock的文件

``` bash
mkdir lib
curl -o lib/angular.min.js https://code.angularjs.org/1.4.0-rc.2/angular.min.js
curl -o lib/angular-mocks.js https://code.angularjs.org/1.4.0-rc.2/angular-mocks.js
```
我们需要将新增的目录加入到配置中去，打开 `karma.conf.js `然后添加成下面内容:

``` bash
// list of files / patterns to load in the browser
files: [
  'lib/angular.min.js',
  'lib/angular-mocks.js',
  'app/*.js',
  'tests/*.js'
],
```
再次在命令行中输入命令:
``` bash
npm test
```
运行结果依旧是失败的，因为我们并没有添加我们的controller,接下来我们将下面的代码粘贴到` app/calculator.controller.js`

``` js
angular.module('calculatorApp', []).controller('CalculatorController', function CalculatorController($scope) {
  $scope.sum = function() {
    $scope.z = $scope.x + $scope.y;
  };
});
```
在命令行工具里面我们可以看到karma检测到了文件变化，重新进行测试，这个时候测试就会通过了。

接下来我们添加一个新的测试用例，我们将它增加到`calculator.controller.test.js`中去，把它放到`describe`作用域内。

``` js
it('z should have default value of zero', function () {
	var $scope = {};
	var controller = $controller('CalculatorController', { $scope: $scope });
	expect($scope.z).toBe(0);
});
```
<img src="http://img1.vued.vanthink.cn/vuedfda9dcd36e7e746d84e03e61f59c4942.png" />
命令行这个时候会输出测试不通过，这个时候我们需要更新我们的controller代码
```js
angular.module('calculatorApp', []).controller('CalculatorController', function CalculatorController($scope) {
  $scope.z = 0;
  $scope.sum = function() {
    $scope.z = $scope.x + $scope.y;
  };
});

```
测试通过的输出如下:

<img src="http://img1.vued.vanthink.cn/vued3ed1f606472c42aed5ff2e7c45dbd643.png" />

### 关于Karma

原文作者写过一篇[文章](http://www.bradoncode.com/blog/2015/02/27/karma-tutorial/)更为详细的介绍Karam,大家可以去深入的了解karma的使用。

karma支持不同的浏览器，我们可以在测试的时候设置这些浏览器：

+ [chrome](https://www.npmjs.com/package/karma-chrome-launcher)
+ [safari](https://www.npmjs.com/package/karma-safari-launcher)
+ [firefox](https://www.npmjs.com/package/karma-firefox-launcher)
+ [IE](https://www.npmjs.com/package/karma-ie-launcher)

除此之外，karma还有很多可供使用的插件，你可以在这里搜索到他们:https://www.npmjs.com/package/karma-ie-launcher



-----
原文地址: http://www.bradoncode.com/blog/2015/05/19/karma-angularjs-testing/

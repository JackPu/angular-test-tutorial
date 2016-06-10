# 如何对Angular Controller进行单元测试
原文地址:http://www.bradoncode.com/blog/2015/05/17/angularjs-testing-controller/

 [Bradley Braithwaite ](http://www.bradoncode.com/about)

上面一篇文章简单介绍了如何[使用 Jasmine 进行JavaScript的单元测试](http://www.jackpu.com/yi-kai-shi-dui-angular-appjin-xing-dan-yuan-ce-shi-1/)

我们用了一段简单的代码进行计算的测试。
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

		// Basic calculator logic
		var calculator = {
			sum: function (x, y) {
				return x + y;
			},
			subtract: function (x, y) {
				return x - y;
			},
			divide: function (x, y) {
				return (y === 0) ? 0 : x / y;
			}
		}

		// The tests
		describe('calculator', function () {
			
			describe('sum', function () {
				it('1 + 1 should equal 2', function () {
					expect(calculator.sum(1, 1)).toBe(2);
				});	
			});

			describe('subtract', function () {
				it('3 - 2 should equal 1', function () {
					expect(calculator.subtract(3, 2)).toBe(1);
				});
			});

			describe('divide', function () {
				it('10 / 5 should equal 2', function () {
					expect(calculator.divide(10, 5)).toBe(2);
				});

				it('zero divisor should equal 0', function () {
					expect(calculator.divide(10, 0)).toBe(0);
				});
			});
		});

	</script>
</html>
```

接下来我们将其延伸到我们对Angular Controller的测试中。如果你不太了解angular也没关系，下文也会提及关于Angular的一些知识。

### 写个简单的Angular App

在开始写测试之前，我们先写一个简单的计算App,它会计算两个数字之和。

<img src="http://www.bradoncode.com/assets/posts/2015/angular-calc.gif" />

代码如下：
``` js
<html>
	<head>
		<script type="text/javascript" src="https://code.angularjs.org/1.4.0-rc.2/angular.min.js"></script>
	</head>
	<body>
		<!-- This div element corresponds to the  CalculatorController we created via the JavaScript-->
		<div ng-controller="CalculatorController">
			<input ng-model="x" type="number">
			<input ng-model="y" type="number">
			<strong>{{z}}</strong>
			<!-- the value for ngClick maps to the sum function within the controller body -->
			<input type="button" ng-click="sum()" value="+">
		</div>
	</body>
	<script type="text/javascript">

		// Creates a new module called 'calculatorApp'
		angular.module('calculatorApp', []);

		// Registers a controller to our module 'calculatorApp'.
		angular.module('calculatorApp').controller('CalculatorController', function CalculatorController($scope) {
		  $scope.z = 0;
		  $scope.sum = function() {
		    $scope.z = $scope.x + $scope.y;
		  };
		});
		
		// load the app
		angular.element(document).ready(function() {
   			angular.bootstrap(document, ['calculatorApp']);
		});

	</script>
</html>
```
简单说说里面涉及的一些基本概念:

#### 创建一个 module
什么是angular.module？它是用于创建，回收模块的地方
。我们创建一个名为calculatorApp新的模块，我们并将组件添加到这个模块里。

``` js
angular.module('calculatorApp', []);

```

关于第二个参数？第二个参数必须的，表明我们正在创造一个新的模块。如果需要我们的应用程序有其他的依赖，我们可以将它们`['ngResource'，'ngCookies']`传入进去。
第二个参数的存在的表示这是一个请求返回的模块的实例。

从概念上讲，它本意是类似下面的意思:
``` js
* angular.module.createInstance(name, requires);
* angular.module.getInstance(name)
```

然而实际我们是这样写的:

``` js 
* angular.module('calculatorApp', []); // i.e. createInstance
* angular.module('calculatorApp'); // i.e. getInstance
```
关于module的更多信息 https://docs.angularjs.org/api/ng/function/angular.module

#### 2.给module添加controller

接着我们给angular module的示例添加一个controller

``` js
angular.module('calculatorApp').controller('CalculatorController', function CalculatorController($scope) {
  $scope.z = 0;
  $scope.sum = function() {
    $scope.z = $scope.x + $scope.y;
  };
});
```
控制器主要负责业务逻辑和视图绑定，`$scope`者是视图的控制器直线的信使。

#### 3.连接视图中的元素
在下面 HTML 中，我们需要计算input里面的值，而这些都包含在这个controller的div中。

``` html
<div ng-controller="CalculatorController">
	<input ng-model="x" type="number">
	<input ng-model="y" type="number">
	<strong>{{z}}</strong>
	<!-- the value for ngClick maps to the sum function within the controller body -->
	<input type="button" ng-click="sum()" value="+">
</div>
```

input 中的ng-model绑定的的值及时$scope上定义的比如`$scope.x`,我们还在button元素使用ng-click绑定了`$scope.sum`方法。

### 添加测试

接下来终于到了我们的主题，添加一些单元测试给controller,我们忽略代码中html部分，主要集中在controller的代码中。

``` html
angular.module('calculatorApp').controller('CalculatorController', function CalculatorController($scope) {
  $scope.z = 0;
  $scope.sum = function() {
    $scope.z = $scope.x + $scope.y;
  };
});
```

为了测试 controller，我们还得提及下面几点？
+ 如何创建一个controller实例
+ 如何get/set一个对象的属性
+ 如何调用$scope里面的函数

``` js
describe('calculator', function () {
		
	beforeEach(angular.mock.module('calculatorApp'));

	var $controller;

	beforeEach(angular.mock.inject(function(_$controller_){
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
开始前我们需要引入[ngMock](https://docs.angularjs.org/api/ngMock),我们在测试的代码加入`angular.mock`

,ngMock模块提供了一种机制进行诸如以及虚拟的service进行单元测试。

#### 如何获取controller的实例

使用ngMock我们可以注册一个calculator app实例。
``` js
beforeEach(angular.mock.module('calculatorApp'));
```
一旦calculatorApp初始化后，我们可以使用`inject`函数，这样可以解决controller的引用问题。

``` js 
beforeEach(angular.mock.inject(function(_$controller_) {
	$controller = _$controller_;
}));
```

一旦app加载完了，我们使用了`inject`函数，$controller service可以获取 CalculatorController 的实例。

``` js 
var controller = $controller('CalculatorController', { $scope: $scope });
```
#### 如何get/set一个对象的属性

在上篇代码中我们已经可以获取一个controller的实例，在括号的第二个参数实际是controller自己，我们的controller只有一个参数
`$scope`对象
``` js
function CalculatorController($scope) { ... }
```
在我们的测试中$scope代表的就是一个简单的JavaScript对象。


```js
var $scope = {};
var controller = $controller('CalculatorController', { $scope: $scope });
// set some properties on the scope object
$scope.x = 1;
$scope.y = 2;
```
我们设置x,y的值，模拟刚才的gif中的所展示的一样。我们同意也可以读取对象中的属性，就像下面这段测试的断言：
``` js
expect($scope.z).toBe(3);
```
#### 如何调用$scope里面的函数

最后一件事情就是我们如何模拟用户的点击，就像我们在绝大多数JS中使用的一致，，其实就是简单的调用函数就行，
``` js
$scope.sum();
```

### 小结

本篇文章简单的基本的介绍了如何对angular controller进行单元测试，但是这是建立在不停的刷新浏览器基础上，
而这些流畅可以再好，也是我们后面的一篇文章 如何使用karam进行 angular 测试 (翻译中...)的所要说的。

完整代码:



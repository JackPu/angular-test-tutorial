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
什么是angular.module？它是用于创建，回收模块的地方，。我们创建一个名为calculatorApp新的模块，我们并将组件添加到这个模块里。

``` js
angular.module('calculatorApp', []);

```







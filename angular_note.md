#angular学习

angular一切从模块开始

ng-app:启动angular项目的,appModule表示通过模块来启动项目,会产生一个根作用域（全局作用域），会调用angular中的run方法

```
<html ng-app="appModule"> //启动angular项目

var app=angular.module('appModule',[]) //定义模块：1.模块的名字 2.模块的依赖 3.配置
```

angular初始化完成后就有rootScope,在这里声明全局变量
```
app.run(function ($rootScope) {
        $rootScope.name = 100;
    })
```
    
ng-controller: 控制器,可以通过创建控制器产生独立的作用域, 控制器默认不会执行 需要在标签上增加ng-controller才能执行
```
<div ng-controller="myCtrl"></div>

app.controller('myCtrl',['$scope',function($scope){//$scope就是viewModel 我们将数据要挂载在vm上
    $scope.name = 100;
}])
```
ng-controller 特点
> 独立作用域,子继承父，父不能继承子
 控制器可以嵌套着写(嵌套带有ng-controller的html标签)
 控制器的作用范围和标签平齐
 不要在控制器中操作dom X

ng-model 
-- 适用范围：表单元素，具体指input select radio checkbox textarea
-- 实现双向数据绑定 ng-model="name", 如果作用域上有name变量会将name变量放到输入框中 MVVM M->V,如果没有则会在作用域上声明这个数据，如果更改数据会导致作用域上的数据发生变化。
```
<input type="text" ng-model="name">
```

ng-bind/{{}}
- 可以将作用域上的值取出来
- 可以赋值 ，可以做运算，可以做三元表达式

ng-cloak: 防止闪烁一片
```
[ng-cloak]{display:none}
```

ng-repeat: 循环对象 数组 
```
$scope.arr = [1,2,3]
$scope.obj = {name:'zfpx'}

<ul>
    <li ng-repeat="(key,value) in obj track by $index">
        {{value}}{{$index}} {{$odd}}  {{$even}} {{$first}} {{$last}} {{$middle}}
    </li>
</ul>
```

ng-if/ng-show/ng-hide
- ng-show/ng-hide操作dom元素的隐藏或显示(none/block)
- ng-if (removeChild/appendChild),会产生作用域

> 频繁切换用show ，用来判断数据是否存在,如果外层不存在内层代码不执行（判断数组存不存在arr.length）

ng-class/ng-style
- ng-class和原生的class不冲突
```
<div class="className3" ng-class="{'className1':true,'className2':false}"></div>
<div ng-class="{true:'className1',false:'className2'}[flag]"></div>
<div ng-style="{fontSize:'40px',backgroundColor:'red'}">
```

## 函数
- {{fn()}} 将执行的结果展示到页面上
```
$scope.fn = function(){
    return value
}
```
- ng-click(ng-事件名)
```
<div ng-click="fn($event)"></div>
```

## 表单属性
```
ng-disabled ng-readonly
```

## 过滤器
### 内置过滤器
- 不会改变原数据，只是改变展示方式
```
{{'abcd' | uppercase}}
{{'ABCD' | lowercase}}
{{100000 | number}}
<pre>{{ {name:1} | json:4}}</pre>
{{1486437459166 | date:'yy-MM-dd hh时mm分ss秒'}}
{{100 | currency:'?'}}
{{'欢迎你来xxx' | limitTo:3}}
{{[{name:1},{name:3},{name:2}] | orderBy:'name':true}}是否倒序
{{[{name:1},{name:3},{name:2}] | filter:3}}
```

### 自定义过滤器
过滤器和控制器没有关系，过滤器和控制器的声明方式是平齐的通过模块创建
```
app.filter('myFilter',function(){
    return function(){
        return '结果'
    }
})

{{'hello' | capitalize:'1':'3'}}
app.filter('capitalize',function () {
    return function (input,param1,param2) {
        return input.slice(0,param1)+input.slice(param1,param2).toUpperCase()+input.slice(param2);
        //return 'hELlo'
    }
});
```

## $sce编译html数据
```
<div ng-bind-html='str'></div>
app.controller('myCtrl',function($scope,$sce){
    $scope.str = $sce.trustAsHtml('<h1>1</h1>')
})
```

## 抽取编译html代码
```
<div ng-bind-html="str | asHtml">
app.filter('asHtml',function ($sce) {
    return function (data) {
        return $sce.trustAsHtml(data);
    }
});
``` 


## 指令
- 装饰型指令(link函数)给标签赋予功能，red drag
- 组件式(会有模板)

## 声明指令
```
//默认不会产生任何作用域
app.directive('direName',function(){
    return {
        require:'^?group', //^表示当前找不到则向上一级找，找不到则报错？找不到则不报错，注入进来的东西是null
        controller:function($scope){},//$scope代表当前的作用域
        restrict:'EA',//限制替换的范围 
        template:'',//要替换的模板/templateUrl:'tmpl/open.html'
        replace:false,//不替换外部的标签
        transclude:true,//保留指令中的内容插入到带有ng-transclude的标签中
        link:function(scope,element,attrs,ctrl){ //操作dom元素 
            //scope当前指令所在的作用域，自己没有作用域则向上查找
            //element jq对象（angular内置的），jquery要引入到angular之前，angular发现自己加载过了jquery，就不会加载自己的jquery了
            //attrs 当前指令所有属性的集合
            //ctrl代表的就是当前依赖的指令的控制器的实例
            //链接函数 链接作用域和视图的
        },
        scope:{
            title:'@',//通过属性传递 获取的是属性对应的字符串
            name:'=n',//通过属性传递 获取的是属性对应作用域上的变量 
            fn:'&say' //通过属性传递一个方法，必须以对象的方式传递 say({n:1});
        }/true
        //1.{} 完全和父作用域断绝关系
        //2.true 产生作用域但是不断绝作用域的关系
    }
});
```

## watch + apply
- watch监控数据变化
```
$scope.$watch('name',function(newVal,oldVal){});
$scope.$watch(function(){return result},function(newVal,oldVal){})
```
- apply应用数据，刷新视图
原生的js中操作作用域上的数据,是不会刷新视图的，自带的指令和服务自动刷新视图
```
$scope.$apply()
```


## $http,$interval,$timeout
```
$http.jsonp($sce.trustAsResourceUrl('url'),{jsonpCallbackParam:'cb'}).then(success,err);
``` 

## 自定义服务
- 单例 共用（$rootScope）
- provider factory service value constant
    - provider 最大的服务，并且可以配置
        ```
            app.config(function(MyProvider){
                MyProvider.aa = 200;
            })
            app.provider('My',function(){
                this.aa = 100;
                this.$get = function(){
                    return {name:1}
                }
            })
        ```
    - factory 声明后是一个$get函数
        ```
            app.factory('My',function(){
                return {name:1,age:8}
            })
        ```
    - service 是factory返回值的构造函数
        ```
            app.service('My',function(){
                this.name = 1;
                this.age= 9;
            })
        ```
    - value 是factory返回的值 constant同value用法
        ```
            app.value('My',{name:1,age:8})
        ```
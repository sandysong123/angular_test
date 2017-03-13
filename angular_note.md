#angularѧϰ

angularһ�д�ģ�鿪ʼ

ng-app:����angular��Ŀ��,appModule��ʾͨ��ģ����������Ŀ,�����һ����������ȫ�������򣩣������angular�е�run����

```
<html ng-app="appModule"> //����angular��Ŀ

var app=angular.module('appModule',[]) //����ģ�飺1.ģ������� 2.ģ������� 3.����
```

angular��ʼ����ɺ����rootScope,����������ȫ�ֱ���
```
app.run(function ($rootScope) {
        $rootScope.name = 100;
    })
```
    
ng-controller: ������,����ͨ����������������������������, ������Ĭ�ϲ���ִ�� ��Ҫ�ڱ�ǩ������ng-controller����ִ��
```
<div ng-controller="myCtrl"></div>

app.controller('myCtrl',['$scope',function($scope){//$scope����viewModel ���ǽ�����Ҫ������vm��
    $scope.name = 100;
}])
```
ng-controller �ص�
> ����������,�Ӽ̳и��������ܼ̳���
 ����������Ƕ����д(Ƕ�״���ng-controller��html��ǩ)
 �����������÷�Χ�ͱ�ǩƽ��
 ��Ҫ�ڿ������в���dom X

ng-model 
-- ���÷�Χ����Ԫ�أ�����ָinput select radio checkbox textarea
-- ʵ��˫�����ݰ� ng-model="name", �������������name�����Ὣname�����ŵ�������� MVVM M->V,���û�������������������������ݣ�����������ݻᵼ���������ϵ����ݷ����仯��
```
<input type="text" ng-model="name">
```

ng-bind/{{}}
- ���Խ��������ϵ�ֵȡ����
- ���Ը�ֵ �����������㣬��������Ԫ���ʽ

ng-cloak: ��ֹ��˸һƬ
```
[ng-cloak]{display:none}
```

ng-repeat: ѭ������ ���� 
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
- ng-show/ng-hide����domԪ�ص����ػ���ʾ(none/block)
- ng-if (removeChild/appendChild),�����������

> Ƶ���л���show �������ж������Ƿ����,�����㲻�����ڲ���벻ִ�У��ж�����治����arr.length��

ng-class/ng-style
- ng-class��ԭ����class����ͻ
```
<div class="className3" ng-class="{'className1':true,'className2':false}"></div>
<div ng-class="{true:'className1',false:'className2'}[flag]"></div>
<div ng-style="{fontSize:'40px',backgroundColor:'red'}">
```

## ����
- {{fn()}} ��ִ�еĽ��չʾ��ҳ����
```
$scope.fn = function(){
    return value
}
```
- ng-click(ng-�¼���)
```
<div ng-click="fn($event)"></div>
```

## ������
```
ng-disabled ng-readonly
```

## ������
### ���ù�����
- ����ı�ԭ���ݣ�ֻ�Ǹı�չʾ��ʽ
```
{{'abcd' | uppercase}}
{{'ABCD' | lowercase}}
{{100000 | number}}
<pre>{{ {name:1} | json:4}}</pre>
{{1486437459166 | date:'yy-MM-dd hhʱmm��ss��'}}
{{100 | currency:'?'}}
{{'��ӭ����xxx' | limitTo:3}}
{{[{name:1},{name:3},{name:2}] | orderBy:'name':true}}�Ƿ���
{{[{name:1},{name:3},{name:2}] | filter:3}}
```

### �Զ��������
�������Ϳ�����û�й�ϵ���������Ϳ�������������ʽ��ƽ���ͨ��ģ�鴴��
```
app.filter('myFilter',function(){
    return function(){
        return '���'
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

## $sce����html����
```
<div ng-bind-html='str'></div>
app.controller('myCtrl',function($scope,$sce){
    $scope.str = $sce.trustAsHtml('<h1>1</h1>')
})
```

## ��ȡ����html����
```
<div ng-bind-html="str | asHtml">
app.filter('asHtml',function ($sce) {
    return function (data) {
        return $sce.trustAsHtml(data);
    }
});
``` 


## ָ��
- װ����ָ��(link����)����ǩ���蹦�ܣ�red drag
- ���ʽ(����ģ��)

## ����ָ��
```
//Ĭ�ϲ�������κ�������
app.directive('direName',function(){
    return {
        require:'^?group', //^��ʾ��ǰ�Ҳ���������һ���ң��Ҳ����򱨴��Ҳ����򲻱���ע������Ķ�����null
        controller:function($scope){},//$scope����ǰ��������
        restrict:'EA',//�����滻�ķ�Χ 
        template:'',//Ҫ�滻��ģ��/templateUrl:'tmpl/open.html'
        replace:false,//���滻�ⲿ�ı�ǩ
        transclude:true,//����ָ���е����ݲ��뵽����ng-transclude�ı�ǩ��
        link:function(scope,element,attrs,ctrl){ //����domԪ�� 
            //scope��ǰָ�����ڵ��������Լ�û�������������ϲ���
            //element jq����angular���õģ���jqueryҪ���뵽angular֮ǰ��angular�����Լ����ع���jquery���Ͳ�������Լ���jquery��
            //attrs ��ǰָ���������Եļ���
            //ctrl����ľ��ǵ�ǰ������ָ��Ŀ�������ʵ��
            //���Ӻ��� �������������ͼ��
        },
        scope:{
            title:'@',//ͨ�����Դ��� ��ȡ�������Զ�Ӧ���ַ���
            name:'=n',//ͨ�����Դ��� ��ȡ�������Զ�Ӧ�������ϵı��� 
            fn:'&say' //ͨ�����Դ���һ�������������Զ���ķ�ʽ���� say({n:1});
        }/true
        //1.{} ��ȫ�͸�������Ͼ���ϵ
        //2.true �����������ǲ��Ͼ�������Ĺ�ϵ
    }
});
```

## watch + apply
- watch������ݱ仯
```
$scope.$watch('name',function(newVal,oldVal){});
$scope.$watch(function(){return result},function(newVal,oldVal){})
```
- applyӦ�����ݣ�ˢ����ͼ
ԭ����js�в����������ϵ�����,�ǲ���ˢ����ͼ�ģ��Դ���ָ��ͷ����Զ�ˢ����ͼ
```
$scope.$apply()
```


## $http,$interval,$timeout
```
$http.jsonp($sce.trustAsResourceUrl('url'),{jsonpCallbackParam:'cb'}).then(success,err);
``` 

## �Զ������
- ���� ���ã�$rootScope��
- provider factory service value constant
    - provider ���ķ��񣬲��ҿ�������
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
    - factory ��������һ��$get����
        ```
            app.factory('My',function(){
                return {name:1,age:8}
            })
        ```
    - service ��factory����ֵ�Ĺ��캯��
        ```
            app.service('My',function(){
                this.name = 1;
                this.age= 9;
            })
        ```
    - value ��factory���ص�ֵ constantͬvalue�÷�
        ```
            app.value('My',{name:1,age:8})
        ```
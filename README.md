#myView

myView框架是一套方便的由路由控制页面加载的框架。

原理是基于url的hashchange进行模块的切换和加载，根据hash规则中描述的module和action进行实例化和执行。是遵循modulejs规范的一个模块，所以依赖与modulejs运行，关于modulejs请点击https://github.com/TC-FE/modulejs 查看。同时依赖modulejs的一个事件模块inf，以及需要一个类jquery库(jquery需封装成modulejs规范，可参见example)，如jquery,zepto

## 如何使用



### 入口文件

```
<!--主容器id，定义myView配置的时候需要传入这个id -->
<div id="container"></div>
<!-- 用于modulejs引入以及显式定义所有用到的模块 -->
<script src="./module.js"></script>
<script type="text/javascript">
    modulejs.config({
        alias: {
            "myView": "../myView.js",
            'inf': "./inf.js",
            'jquery': "./jquery.js",
            'extender': "./extender.js",
            'page1': "./page1.js",
            'page2': "./page2.js"
        }

    });
    /**
     * [_mytouchConfig,myView的配置,参数见下面的解释]
     * @type {Object}
     */
    var _mytouchConfig = {
        "index": ['page1', 'first'], //当hash不存在的时候，默认请求的module和action,数组的第一项是module,第二项是action
        "extender": 'extender', //view方法扩展器
        "containerId": "container", //主容器id
        "moduleActionMap": {
            "module1": {
                "first": ['page1', 'first'],
                "second": ['page2', 'second']
            } //action路由映射，可以给某些action设置别名
        },
        "modelRouteMap":{
          "moduleTest":"page1"
        } //module路由映射
    }
    modulejs('myView', function(m) {
        m.init();
    }); //按照modulejs规范，引入myView模块，然后调用myView的init方法来初始化
</script>
  ```

### 路由映射

使用myView框架，可以在url后加入```#module=action```的hash，然后就可以对应访问遵循modulejs规范的module模块的action方法。action方法可以接收一个参数，为当前页面的实例。示例可查看下面的模块示例。

### 模块示例

```
define('page1', function(require, exports, module) {

    /**
     * [myView的action,会传入当前的实例化的view,view的api见下]
     * @param  {[type]} view [description]
     * @return {[type]}      [description]
     */
    exports.first = function(view) {

        /**
         * [setContent 给当前页面填充html]
         * @param {[type]} '<p>页面一的first方法</p><a href="#page1=second">跳转到页面一的second方法</a><br><br><a href="#page2=first">跳转到页面二的first方法</a>' [description]
         */
        view.setContent('<p>页面一的first方法</p><a href="#page1=second">跳转到页面一的second方法</a><br><br><a href="#page2=first">跳转到页面二的first方法</a>');

        //异步回调请使用view的callbackProxy封装，以防hash切换后异步回调才开始执行导致的问题
        setTimeout(view.callbackProxy(function() {
            console.log('调用view的callbackProxy方法来封装回调');
        }), 2000);

        //或者如果要使用setTimeout方法时，可使用myView提供的封装,在hash切换后会进行销毁
        view.setTimeout(function() {
            console.log('调用view的setTimeout方法');
        },3000)

    };

    exports.second = function(view) {
        view.setContent('<p>页面一的second方法</p><a href="#page1=first">跳转到页面一的first方法</a><br><br><a href="#page2=first">跳转到页面二的first方法</a>');

    }
});

```

## 配置

在初始化myView前，请使用全局变量```var _mytouchConfig={}```来配置myView

参数|描述|示例|默认值
---|---|---
index|当hash不存在的时候，默认请求的module和action,数组的第一项是module,第二项是action|["module","action"]|无
containerId|主容器id，myView填充html要用到的domId|"container"|body
extender|view方法扩展器，可以作为项目的基类，可不填|"extender"|""
moduleActionMap|action路由映射，可以给某些action设置别名| 参见上面的入口文件|{}
modelRouteMap|module路由映射，可以给某些module设置别名|参加上面的入口文件示例|{}

## API

### view.setContent(htmlString)

给当前页面填充html


### view.setTimeout(fn,timeouts)

view封装的安全setTimeout方法

### view.setInterval(fn,timeouts)

view封装的安全setInterval方法

### view.callbackProxy(fn,[thisArg])

view封装的安全回调方法，把你的回调函数用callbackProxy包装一下，以便于执行安全的回调函数

  ## Example

  参见:https://github.com/TC-FE/myView/tree/master/example/

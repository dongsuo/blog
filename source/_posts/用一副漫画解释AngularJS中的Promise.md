---
title: 用一副漫画解释AngularJS中的Promise
date: 2017-03-23 17:16:26
tags:
---

##### 一个早晨，父亲对孩子说：‘去给我把天气预报搞来，儿砸！’

每个周日的早晨，父亲都会让他儿子到房子旁边最高的山上用超级望远镜看向地平线来预报当天下午的天气。儿子则向他爸爸承诺他会去山上预报天气。于是，当他出门的时候，他在门口向他爸爸作出了一个承诺。

这个时候，父亲决定，如果是个好天气的话，他就准备一次钓鱼旅行，如果坏天气的话，他就不出去，而且如果儿子没能搞来天气预报，他也不出去。

大概半个小时之后，儿子回来了。一周一周地过去了，各种不一样的情况发生了。

<!--more-->

##### 结果A：成功预报天气！阳光明媚:-)

儿子成功地预报了天气，天气晴朗，阳光明媚！承诺兑现了（儿子保守了诺言），父亲决定收拾东西准备周日的钓鱼旅行。

![Promise fulfilled, and weather is good](http://andyshora.com/img/pages/promise1.png)

##### 结果B：成功预报天气！阴雨连绵:-(

儿子成功地预报了天气，但是看起来阴雨连绵。承诺兑现了，但由于是坏天气，父亲决定哪也不去。

![Promise fulfilled, and weather is good](http://andyshora.com/img/pages/promise1.png)

##### 结果C：没能预报天气:-/

儿子没能预报天气，出问题了；雾太浓导致没法在山顶上看清会有什么天气。儿子走时作出的承诺没能兑现——承诺被拒绝了（rejected）！父亲决定还是不要出去了，没必要冒这个险。

![Promise rejected](http://andyshora.com/img/pages/promise2.png)

##### 在代码里看起来是什么样的呢？

在这个情形中，父亲控制着逻辑，父亲跟儿子打交道，就好像儿子是一个服务一样。

我们已经说明了这个逻辑，父亲让儿子去搞来天气预报，由于儿子不能立刻告诉父亲天气预报，于是父亲在等待的时候先去做别的事情，儿子承诺他会带天气预报回来。父亲得到天气预报后，他要么打包收拾船，要么待在家里。重要的是要记住，孩子去山上预报天气的过程不应该‘阻塞’父亲做其他事情，所以这就是为什么这个情况能够完美地说明承诺的创建以及之后承诺的解决（兑现或拒绝）这一过程。

##### Controller:FatherCtrl

这是关于父亲控制这个情形的代码：

```javascript
// father-controller.js中某个地方的方法
var makePromiseWithSon = function(){
  // 这个服务的方法返回一个promise，我们稍后处理那部分
  sonService.getWeather()
  	//当儿子回来的时候then()就会被调用
  	.then(function(data){
      	//承诺兑现
    	if(data.forecast ==='good'){
          prepareFishingTrip();
    	}else{
          prepareSundayRoasterDinner();
    	}
  	},function(error){
      //承诺被拒绝（没能兑现），可以通过console.log('error',error);把错误信息记录下来
    prepareSundayRoasterDinner();
  	})
}
```

##### Service:SonService

儿子被用作一个服务，他爬上山顶尝试看清天气。我们假设儿子通过他的望远镜查找即将到来的天气时，类似于使用一个天气API，在某种意义上说它是一个异步操作，他可能得到很多种应答，可能会是一个错误（比如说一个500相应（译者注：内部服务器错误），大雾天气）。

如果承诺兑现了，‘钓鱼天气API’的响应会以｛“forecast”:"good"｝的格式，跟承诺一起被返回。

```javascript
app.factory('SonService', function ($http, $q) {
        return {
            getWeather: function() {
                // $http API基于$q服务暴露出来的deferred/promise APIs
                // 所以对我们来说他默认返回一个承诺（promise）
                return $http.get('http://fishing-weather-api.com/sunday/afternoon')
                    .then(function(response) {
                        if (typeof response.data === 'object') {
                            return response.data;
                        } else {
                            // 无效的响应
                            return $q.reject(response.data);
                        }
                    }, function(response) {
                        // 什么地方出错了
                        return $q.reject(response.data);
                    });
            }
        };
    });
```

##### 总结

这个类比说明了父亲对儿子请求天气预报的异步性质。当儿子出门的时候，父亲不想带着期待在门口等着，因为他还有其他的事情要做。他在门口跟儿子约定了一个承诺，并针对三种可能的情景（好天气/坏天气/没有预报）都作出了决定。儿子离开时立刻给了父亲一个承诺，将在回来时解决或者拒绝它。

儿子是在处理一个异步服务（用望远镜搜索天空/使用一个天气API）来获取数据，但是这一切都被正确地从他老爹那里抽象出来了，他老爹根本不需真正懂这个技术。

*原文地址：http://andyshora.com/promises-angularjs-explained-as-cartoon.html*
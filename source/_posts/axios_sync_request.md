---
title: axios如何执行并发请求
date: 2017-12-13 15:12:31
tags: 
  - axios
  - vue
---

#### 前言
这周都在开发同花顺广告平台，虽然自己从去年就开始使用vue，但是很多和vue绑定使用的包都只会些基本语法。所以只能用一次记录一次，希望帮助自己回忆，也能给别人一点提示。

#### zepto如何实现并发请求
zepto有一个deffer扩展，可以实现请求的并发，内部原理其实也是promise，源码请参考[这里](https://github.com/madrobby/zepto/blob/master/src/deferred.js)
```js
// 获取topic_id为592917b59e32cc84569a7458的主题信息
function getTopicInfo1(deferred) {
  $.ajax({
    type: 'GET',
    url: 'https://cnodejs.org/api/v1/topic/592917b59e32cc84569a7458',
    success: res => {
      deferred.resolve(res);
    },
    error: err => {
      deferred.resolve(err);
    }
  })
}
// 获取topic_id为58eee565a92d341e48cfe7fc的主题信息
function getTopicInfo1() {
  $.ajax({
    type: 'GET',
    url: 'https://cnodejs.org/api/v1/topic/58eee565a92d341e48cfe7fc',
    success: res => {
      deferred.resolve(res);
    },
    error: err => {
      deferred.resolve(err);
    }
  })
}

$(document).ready(() => {
  let topic1Deffer = $.Deferred();
  let topic2Deffer = $.Deferred();

  $.when(topic1Deffer, topic2Deffer).done(function(topic1, topic2) {
    console.log(topic1);
    console.log(topic2);
  });
})
```

#### axios实现
```js
function getUserAccount() {
  return axios.get('/user/12345');
}
 
function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}
 
axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // Both requests are now complete
  }));
```
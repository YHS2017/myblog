---
title: Event的几种坐标取值
date: 2018-03-02 18:56:23
tags: 前端
category: JS
---


#### 相对于整个页面的坐标

```javascript
event.pageX;
event.pageY;
//注：IE8不支持
```

#### 相对于当前浏览器窗口的坐标
```javascript
event.clientX;
event.clientY;
//注：所有浏览器都支持
```

#### 相对于当前屏幕的坐标
```javascript
event.screenX;
event.screenY;
//注：和浏览器窗口大小无关
```

#### 相对于触发事件的对象的坐标
```javascript
event.offsetX;
event.offsetY;
//注：和浏览器窗口大小无关
```
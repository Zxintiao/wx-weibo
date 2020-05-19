# 简介


一个采用微信小程序开发平台开发的一个博客类的小程序开发

使用nodejs搭建中控服务器，来操作微信小程序的云空间，使用vue-cli2搭建的后台管理系统页面：
https://github.com/Zxintiao/wx-weibo-

## 效果图

![home](E:\wx-weibo-master\微信小程序\home.png)

## 目录结构

```
.
├── miniprogram               # 项目文件         
│   ├── pages                 # 页面
│   ├── components            # 组件
│   ├── images                # 图片
│   ├── app.css               # 公共css
│   ├── app.js                # 微信小程序公共区域
│   └── app.json              # 配置项
│
└── cloudfunctions            # 微信云函数
```

## 技术栈
使用了微信小程序的云开发功能

1.授权系统   <button open-type="getUserInfo" bindgetuserinfo="onGetUserInfo">获取微信授权信息</>

- getUserInfo   (button open-type="getUserInfo")
  - 获取用户信息，可以从bindgetuserinfo回调中获取到用户信息   (bindgetuserinfo="onGetUserInfo")

2.获取用户信息管理   wx.getUserInfo

当微信小程序授权之后，就可以 获取到用户的信息

```javascript
    // 判断用户是否授权  
    wx.getSetting({
      success: (res) => {
        if (res.authSetting['scope.userInfo']) { // 授权
          // 获取用户信息
          wx.getUserInfo({
            success: (res) => {
              userInfo = res.userInfo // 保存用户信息
            },
          });
        } else {
          // 没有授权；弹出底部弹出成；获取用户信息
        }
      },
      fail: () => { },
      complete: () => { }
    });
```

3.调用云函数    wx.cloud.callFunction

```javascript
    wx.cloud.callFunction({
      name: "blog", // 那个云函数(blog)
      data: {
        $url: 'count' // 云函数中的哪个接口(count)
      }
    }).then((res) => {
      blogCount = res.result.total // 返回结果
    })
```

4.云函数配置和调用

```javascript
// 云函数入口文件
const cloud = require('wx-server-sdk')
const tcbRouter = require("tcb-router") // 微信云函数路由配置，可以让一个云函数变成多个使用
cloud.init({
  // env 调用都保持和云函数当前所在环境一致
  env: 'xxx-xx-xx-xxx'
})
let db = cloud.database();
let videoCollection = db.collection('video')

// 云函数入口函数
exports.main = async (event, context) => {
    
  const app = new tcbRouter({ // new 一个tcbRouter对象
    event
  });

  app.router("count", async (ctx, next) => {
    // 分页查询，从第一个索引值开始 查询多少条数据
    let num = await videoCollection.count().then((res) => {
      return res
    })
    ctx.body = num // 返回给小程序的数
  })

  return app.serve()
}
```



## 功能列表

-  下拉刷新(在json文件中配置)
  - "enablePullDownRefresh": true
-  使用组件(在json文件中配置)
  -  "usingComponents": {   }

## 安装

​	在云函数中使用   tcb-router

​	需要安装 npm insatll tcb-router --save

## 开发环境

微信小程序开发平台

## 语法

微信小程序开发语法和vue相似




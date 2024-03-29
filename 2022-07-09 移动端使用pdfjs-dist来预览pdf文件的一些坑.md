# 移动端使用pdfjs-dist来预览pdf文件的一些坑

## 前言

> 最近公司挺多项目使用了pdfjs-dist这个库来实现移动端预览pdf，记录一下使用这个库里面的一些坑, pdfjs-dist 其实就是pdf.js,底层都是它,本来以为直接引入，调用一下就好了，可谁想到竟然还有坑，差点是无法填过去的那种！！！

## pdf.js 的兼容性，使用别人的库，还是老老实实看说明，少走弯路😄😄😄

>需要特别注意，这个库从2.4.456 版本开始就不支持旧版本的浏览器了，只支持谷歌76+ 和苹果浏览器13+，还是要好好看文档，所以想支持这些版本一下的，就老老实实的使用这个版本以下的，如果你公司和我们一样，又需要电子签证的功能，那只能和产品说会有这种问题，然后try catch代码，提示用户升级浏览器版本把

### [官网说明](https://github.com/mozilla/pdf.js/wiki/Frequently-Asked-Questions)

![pdf.js兼容性](https://api2.mubu.com/v3/document_image/4ec99b45-b743-426b-a631-318212f59511-2331693.jpg)

### [2021年5月15日更新浏览器支持](https://github.com/mozilla/pdf.js/pull/13379)

> 2021年5月15日 官方更新说明说支持谷歌68起 苹果浏览器11.1起，我尝试了， 我使用pdf.js 这个库在业务代码里面会第一页数据会错乱, 但是使用浏览器直接打开这个pdf文件预览是ok的，麻了，不知道啥问题，感觉是业务的使用方式有问题把
![更新浏览器支持](https://api2.mubu.com/v3/document_image/e602aee3-0ff2-4fa1-bf4e-412e28d0b222-2331693.jpg)

## 坑1：无法预览电子签章

### 问题描述

> 使用pdf.js来预览pdf，别的内容都可以展示，就是这个电子签章死活出不来，电子签章如下图：

![电子签章](https://api2.mubu.com/v3/document_image/27953f6f-73f5-4a02-a8e6-c3cc4553fa86-2331693.jpg)

### 问题原因

>官网没时间搞，我在官网的issue里面看到了很多关于这个issue的讨论，

* 还有说把源码的多少行给打开就好了的， 我觉得这个方式也是不靠谱，有兴趣可以看看下文：
[某個大佬的博客](https://blog.csdn.net/ZYX10077/article/details/122733335)
[掘金](https://www.jianshu.com/p/c6daf60d3855)

### 解決办法

> 官方自救！！！ 10年了终于加上了，官网在2.9.359版本里面修复了，十年了，你知道我都经历了什么😅😅😅

* [官网issue](https://github.com/owncloud/files_pdfviewer/issues/286)
![官网issue](https://api2.mubu.com/v3/document_image/613e736f-09ca-432e-b474-d744f6ac0040-2331693.jpg)
![官网issue](https://api2.mubu.com/v3/document_image/88200431-fcfe-4d7a-a7e3-fba399ad41d9-2331693.jpg)

## 坑2： globalthis is not defined 请看下文说明，这个问题只是表象！！！

> 本来以为高高兴兴上线就完了，可谁知道某一天在监控平台上看到了globalthis is not defined 这个错，而且还挺多的，😅😅😅 ，不能愉快的搬砖了，而且这玩意听都没听说过，🤣🤣🤣，老规矩，不懂就谷歌一下，代码都是cv的，全靠大佬们了
* 2022-07-13 经过三天的波折，发现这个错误其实只是表像，处理了这个错，会报一个正则的错误，处理正则的错误之后会发现，业务里面使用这个库，预览的pdf有问题，但是直接使用浏览器打开是正常的，感觉是业务使用的方式有问题，或者是库在低版本里面有问题，毕竟我们用的是不在他兼容范围里面的版本！！！懒得搞了，最后的解决办法是try catch错误，然后提示用户更新系统

### 问题描述

* 当前项目pdf的版本是 "pdfjs-dist": "2.13.216"
* 另外两个项目的pdf版本 "pdfjs-dist": "2.0.943"
* 只有"pdfjs-dist": "2.13.216"这个版本会挂了，凉凉， "pdfjs-dist": "2.0.943"这个版本是2018年10月份发布的，这个版本没有问题，搞事情哈！！！，为啥会有这个问题，请看上文，没好好看api的下场！！！

![错误1](https://api2.mubu.com/v3/document_image/1314cedf-7503-4967-9403-f6b58a361863-2331693.jpg)

* 报错的浏览器版本，谷歌版本68，相当老了
![谷歌版本68](https://api2.mubu.com/v3/document_image/c0c328d7-a1e1-479d-adcf-93abbf1470e8-2331693.jpg)

### 报错原因

> 查看globalThis 兼容性，这东西在谷歌71版本和苹果浏览器 12.1 后才支持，问题很明显了，报错的浏览器版本是68，不报错才怪了 就是globalThis的兼容性问题

* [globalThis mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis)
![globalThis mdn](https://api2.mubu.com/v3/document_image/edf95330-f8b8-403a-bd3d-6fa86217289a-2331693.jpg)

### 解决办法

#### 办法1：这个亲测也可以的，看大佬们喜欢那个就撸那个咯

![官网demo](https://api2.mubu.com/v3/document_image/fcd308b8-5698-402c-8ac2-fbf554b3d4cd-2331693.jpg)

```js
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};

var globalThis  = getGlobal();

```

#### 办法2：反正这个globalThis 在浏览器环境就是指向window，直接判断一下，让他等于window不就好了，这种亲测也可以

![官网demo](https://api2.mubu.com/v3/document_image/c344672b-a158-4725-91c5-4516bfc38c1f-2331693.jpg)

```js
// 因为使用了pdf.js 来做预览pdf，它的源码里面有使用了globalThis这个东西，这个东西的兼容性是谷歌71版本以上和苹果浏览器12.1  以上，有兼容性问题，做个判断处理一下
   try {
       if(!window.globalThis){
         var globalThis = window
       }
     } catch (error) {
       console.error('globalThis 问题重现，请火速反馈给客服！！！', error)
     }
```

## 正则报错信息

### 报错描述

```bash
vue-router.esm.js:2314 SyntaxError: Invalid regular expression: /^(\s)|(\p{Mn})|(\p{Cf})$/: Invalid escape
    at new RegExp ()
```

![正则报错信息展示](https://api2.mubu.com/v3/document_image/be49db8a-57f7-4d31-8ea8-d41ea83ba568-2331693.jpg)

### 报错代码

> 特别提示 这个正则的报错信息，直接在代码里面是搜不到的，但是打包之后的文件里面又有，所以，可能是在别的包里面搞过来的，具体等待有缘人告知把

```bash
const SpecialCharRegExp = new RegExp("^(\s)|(\p{Mn})|(\p{Cf})$", "u");
```

![正则报错信息展示](https://api2.mubu.com/v3/document_image/f06ee0fa-884f-4478-8f39-79397d4650b3-2331693.jpg)

### 如何处理这个正则报错, 看第三条

![正则报错信息展示](https://api2.mubu.com/v3/document_image/585bdc1b-1043-418e-b1c0-39d7086d0030-2331693.jpg)

## try catch 提示用户升级版本

> try catch pdf.js 库是否有异常，有异常就提示用户升级系统

![正则报错信息展示](https://api2.mubu.com/v3/document_image/a61113e1-3320-48ac-91c4-ebb614c320a5-2331693.jpg)

## 总结

* 要想支持旧的版本就请使用2.4.456版本以前的，这个不支持电子印章！！！
* 要想又支持电子印章，就只能使用2.9.359版本以后的，大声的告诉产品，存在兼容问题！！！
* 那个沙雕要使用pdf在移动端来预览的，图片他不香吗，真的是服，浪费时间！！！

## 附送如何使用低版本调试的方法，知道各位看官老爷们都时间繁忙，我就把他抄过来了

### 1. [选择谷歌版本](https://www.chromedownloads.net/chrome64win-stable/list_2_2.html)

#### 我选的是68的内核

![选择谷歌版本](https://api2.mubu.com/v3/document_image/2fe23dd0-55de-4bbe-a495-2091d4ff3d02-2331693.jpg)

#### 选百度网盘

![选百度网盘](https://api2.mubu.com/v3/document_image/6a15dcd0-2419-444e-ae04-df5ab686fb54-2331693.jpg)

* 下载和解压成功之后得到的文件
![下载成功](https://api2.mubu.com/v3/document_image/ee44bef9-eee5-4f9b-baa5-bd2bd2ad4af6-2331693.jpg)

#### 解压文件的一些疑惑

* exe文件直接解压到当前文件夹

![下载成功](https://api2.mubu.com/v3/document_image/adac1547-8d95-4429-80a8-1cc4859ed9c2-2331693.jpg)

* 解压之后得到的文件夹

![下载成功](https://api2.mubu.com/v3/document_image/446f8e6d-a066-4c61-a2b0-de9676f13a63-2331693.jpg)

* 继续解压

![下载成功](https://api2.mubu.com/v3/document_image/c4e7082f-111a-4de9-a614-afe4e639e38f-2331693.jpg)

* 然后打开chrom文件夹里面就是我们要的

![下载成功](https://api2.mubu.com/v3/document_image/6561484c-5f79-48bd-a7d1-2cce74c2b860-2331693.jpg)

* chrom-bin文件夹内容

![下载成功](https://api2.mubu.com/v3/document_image/302eadcf-6483-436f-9b26-7db818708d24-2331693.jpg)

### 2. 下载好了之后，解压后会得到大概这样的

![下载成功](https://api2.mubu.com/v3/document_image/ee44bef9-eee5-4f9b-baa5-bd2bd2ad4af6-2331693.jpg)

### 3. 在当前目录下新建user-data文件夹

![新建user-data文件夹](https://api2.mubu.com/v3/document_image/5868846c-8699-4fd3-89ee-089819969be7-2331693.jpg)

### 4. 创建桌面快捷方式

![创建桌面快捷方式](https://api2.mubu.com/v3/document_image/c5d1a7b6-ae31-4dce-a3d6-ff78e4e1c049-2331693.jpg)

### 5.更改快捷方式设置

![创建桌面快捷方式](https://api2.mubu.com/v3/document_image/cff55a5e-f802-4a3b-a566-04133004364a-2331693.jpg)

#### 自己修改一下，改完记得点确定,大概格式如下，换成你自己的

> C:\Users\ASUS\Desktop\Chrome-bin\chrome.exe --user-data-dir="C:\Users\ASUS\Desktop\Chrome-bin\user-data"
![创建桌面快捷方式](https://api2.mubu.com/v3/document_image/dc7bccf5-53ed-4a43-b1bc-4d105221d10d-2331693.jpg)

### 6.然后双击刚才那个桌面快捷方式， 搞定，下班

![下班](https://api2.mubu.com/v3/document_image/484db014-d277-4e28-a75c-277fea03c2d2-2331693.jpg)

## 参考资料

* [同一操作系统安装多个不同版本谷歌chrome浏览器](https://www.jianshu.com/p/ce848fd3674e)
* [pdfjs兼容性问题，这个人碰到的和我的一样哈哈](https://github.com/mozilla/pdf.js/issues/14961)

## 结束语

* 本文如有错误，欢迎指正，非常感谢
* 觉得有用的老铁，点个双击，小红心走一波
* 欢迎灌水，来呀，互相伤害哈 o(∩_∩)o 哈哈

# 全景万花筒效果H5页面

使用的js库与插件：<br>
1、[zepto](http://www.zeptojs.cn/) ---- 轻量级兼容jQuery的JavaScript库<br>
2、[touch](https://github.com/Clouda-team/touchjs) ---- 移动设备上的手势识别与事件库<br>
3、[pxloader-images](https://github.com/thinkpixellab/PxLoader) ---- 图片预加载<br>
4、[jstween](https://github.com/shrekshrek/jstween) ---- 动画库<br>
5、[orienter](https://github.com/shrekshrek/orienter) ---- 横竖屏重力感应的易用组件<br>
6、[css3d](https://github.com/shrekshrek/css3d-engine) ---- css 3d引擎<br>

##pxloader-images

```javascript
var fileLoader = function(){
	var loader = new window.PxLoader();
	    /*声明资源文件列表*/
	    var fileList = [
	             ...
	    ];

	    for (var i = 0; i < fileList.length; i++) {
		    loader.addImage(fileList[i]);
	    }
	   /*监听文件加载过程*/
	    loader.addProgressListener(function (e) {
		    ...
    	});
	   /*监听文件加载完成*/
	    loader.addCompletionListener(function () {
	    	...
	    });
	   /*开始预加载*/
	    loader.start();
}
```

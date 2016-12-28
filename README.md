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
##jstween
```javascript
         var gotoFuns = {
		gotoIndex: function(){
		        /*主页面显示动画*/
			JT.fromTo(main.spMain, 4, {
				z: -2200
			}, {
				z: -150,
				ease: JT.Quad.Out,
				onUpdate: function() {
					this.target.updateT().updateV()
				},onEnd:function(){
					touchEvent.canMove = false
					touchEvent.actiondh();
					JT.to($("#bg"), 1, {
							opacity: 1
						});
					skyLogo.visibility({
						visible: 1
					}).updateV();
					$(".govr").show();
				}
			});
		        /*主页面背景显示动画*/
			JT.fromTo(pano.panoBg, 4, {
				rotationY: -720
			}, {
				rotationY: 25,
				ease: JT.Quad.Out,
				onUpdate: function() {
					this.target.updateT().updateV()
				},onEnd:function(){
				
				}
			});
			for (var A = 0, B = pano.panoBg.children.length; B > A; A++){
				JT.from(pano.panoBg.children[A], 0.5, {
					x: 0,
					z: 0,
					scaleX: 0,
					scaleY: 0,
					delay: .05 * A,
					ease: JT.Quad.Out,
					onUpdate: function() {
						this.target.updateT()
					},
					onStart: function() {
						this.target.visibility({
							alpha: 1
						}).updateV()
					}
					
				});
			} 
			/*主页面第二层元素显示动画*/
			JT.fromTo(panoItem.items, 4, {
				rotationY: -720
			}, {
				rotationY: 25,
				delay: Math.random() + 1,
				ease: JT.Quad.Out,
				onUpdate: function() {
					this.target.updateT().updateV()
				}
			})
			for (var g = 0, C = panoItem.items.children.length; C > g; g++){
				JT.from(panoItem.items.children[g], 2, {
					x: 0,
					z: 0,
					scaleX: .01,
					scaleY: .01,
					scaleZ: .01,
					delay: Math.random() + 2,
					ease: JT.Quad.Out,
					onUpdate: function() {
						this.target.updateT()
					},
					onStart: function() {
						this.target.visibility({
							alpha: this.target.__name.split('-')[1]>1?0:1
						}).updateV()
					},onEnd:function(){
						var panoItemName = this.target.__name.split('-')[0],
							index = this.target.__name.split('-')[1]-0;
						if(index === 1){
							new animateList({
								list: $('[data-name^="'+panoItemName+'"]'),
								interval: 500,
								index: 0,
								name:'loadingAni',
								intervalT: 1,
								filter: '[data-name^="'+panoItemName+'"]'
							})
						}
					}
				});
			}
		}
	}
```

##css3d
```javascript
       
```
##orienter
```javascript
        //重力感应
	var o2 = new Orienter();
	o2.onOrient = function (t) {
		touchEvent.d.lon = -t.lon,
		touchEvent.d.lat = t.lat
		if(touchEvent.canMove){
			touchEvent.f.lat = -touchEvent.d.lat, 
			touchEvent.f.lon = -touchEvent.d.lon
		}
	};
	o2.init();
```

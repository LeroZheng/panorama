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
    var main = {
		stage: null,
		spMain: null,
		init: function(){
			//创建场景
			this.stage = new C3D.Stage({
				el: $("#three")[0]
			});
			this.stage.size(window.innerWidth, window.innerHeight).update();
			//容器
			this.spMain = new C3D.Sprite();
			this.spMain.position(0, 0, -600).update();
			this.stage.addChild(this.spMain);

			$('.win').width(window.innerWidth).height(window.innerHeight);
		}
	}   
	//创建全景场景
	var pano = {
		bgImgUrl: 'images/bg/',
		bgNum: 20,
		bgWidth: 2580,
		bgHeight: 1099,
		panoBg: null,
		picWidth: 0,
		picRadius: 0,
		init: function(){		
			this.createPanoBg();
		},
		createPanoBg: function(){
			var that = this;
	                var _step = this.bgWidth / this.bgNum;  //每一块切块的宽
	                var _radius = Math.floor(_step / 2 / Math.tan(Math.PI / this.bgNum))-2; //半径

	                this.picWidth = _step;
	                this.picRadius = _radius;

			this.panoBg = new C3D.Sprite();

			this.panoBg.name("panoBg").position(0, 0, 0).update();
			main.spMain.addChild(this.panoBg);

			//为每个切块生成一个平面 
			for (var i = 0; i < this.bgNum; i++) {
				var _p = new C3D.Plane();
				var _r = -360 / this.bgNum * i;  //平面的倾斜角度
				var _a = _r / 180 * Math.PI;  //平面的中心切线与水平线之前的角度
				_p.size(_step, this.bgHeight).position(Math.sin(_a) * _radius, 0, Math.cos(_a) * _radius).rotation(0, _r + 180, 0).visibility({
					alpha: 0
				}).material({
				    image: that.bgImgUrl+(i+1)+'.png',
				    repeat: 'no-repeat',
				    bothsides: false,
				}).update();
				this.panoBg.addChild(_p);
			}
		}
	}
	
	//旋转事件与场景刷新
	var touchEvent = {
		d: {
			lat: 0,
			lon: 0
		},
		f: {
			lon: 0,
			lat: 0
		},
		c: {
			lon: 25,
			lat: 0
		},
		canMove: true,
		originTouchPos: {
			x: 0,
			y: 0
		},
		oldTouchPos: {
			x: 0,
			y: 0
		},
		newTouchPos: {
			x: 0,
			y: 0
		},
		firstDir: "",
		originTime: 0,
		oldTime: 0,
		newTime: 0,
		dx: 0,
		dy: 0,
		ax: 0,
		ay: 0,
		time: 0,
		onTouchStart: function(t){
			touchEvent.firstDir = "", 
			t = t.changedTouches[0];
			event.preventDefault();
			touchEvent.originTouchPos.x = touchEvent.oldTouchPos.x = touchEvent.newTouchPos.x = t.clientX ;
			touchEvent.originTouchPos.y = touchEvent.oldTouchPos.y = touchEvent.newTouchPos.y = t.clientY ;
			touchEvent.originTime = touchEvent.oldTime = touchEvent.newTime = Date.now();
			touchEvent.dx = touchEvent.dy = touchEvent.ax = touchEvent.ay = 0, 
			main.stage.on("touchmove", touchEvent.onTouchMove), 
			main.stage.on("touchend", touchEvent.onTouchEnd)
		},
		onTouchMove: function(t){
			t = t.changedTouches[0], 
			touchEvent.newTouchPos.x = t.clientX, 
			touchEvent.newTouchPos.y = t.clientY, 
			touchEvent.newTime = Date.now(), 
			touchEvent.checkGesture(), 
			touchEvent.oldTouchPos.x = touchEvent.newTouchPos.x, 
			touchEvent.oldTouchPos.y = touchEvent.newTouchPos.y, 
			touchEvent.oldTime = touchEvent.newTime
			return false;
		},
		onTouchEnd: function(e){
			touchEvent.newTime = Date.now();
			var t = (touchEvent.newTime - touchEvent.oldTime) / 1e3;
			main.stage.off("touchmove", touchEvent.onTouchMove);
			main.stage.off("touchend", touchEvent.onTouchEnd);
		},
		checkGesture: function(){
			touchEvent.dx = touchEvent.fixed2(touchEvent.newTouchPos.x - touchEvent.originTouchPos.x), 
			touchEvent.dy = touchEvent.fixed2(touchEvent.newTouchPos.y - touchEvent.originTouchPos.y), 
			touchEvent.ax = touchEvent.fixed2(touchEvent.newTouchPos.x - touchEvent.oldTouchPos.x), 
			touchEvent.ay = touchEvent.fixed2(touchEvent.newTouchPos.y - touchEvent.oldTouchPos.y), 
			touchEvent.time = (touchEvent.newTime - touchEvent.oldTime) / 1e3, 
			"" == touchEvent.firstDir && (Math.abs(touchEvent.ax) > Math.abs(touchEvent.ay) ? touchEvent.firstDir = "x" : Math.abs(touchEvent.ax) < Math.abs(touchEvent.ay) && (touchEvent.firstDir = "y"));
			
			if(!touchEvent.canMove){
				touchEvent.c.lon = (touchEvent.c.lon - .2 * touchEvent.ax) % 360,
				touchEvent.c.lat = Math.max(-90, Math.min(90, touchEvent.c.lat + .2 * touchEvent.ay))
			}
		},
		fixed2: function(t) {
			return Math.floor(100 * t) / 100
		},
		actiondh: function(){
			var t = (touchEvent.d.lon + touchEvent.f.lon + touchEvent.c.lon) % 360,
				i = .35 * (touchEvent.d.lat + touchEvent.f.lat + touchEvent.c.lat);
			t - pano.panoBg.rotationY > 180 && (pano.panoBg.rotationY += 360),
			t - pano.panoBg.rotationY < -180 && (pano.panoBg.rotationY -= 360);
			var n = t - pano.panoBg.rotationY,
				a = i - pano.panoBg.rotationX;
			Math.abs(n) < .1 ? pano.panoBg.rotationY = t : pano.panoBg.rotationY += .3 * n, 
			Math.abs(a) < .1 ? pano.panoBg.rotationX = i : pano.panoBg.rotationX += .15 * a, 
			pano.panoBg.updateT();

			t - panoItem.items.rotationY > 180 && (panoItem.items.rotationY += 360),
			t - panoItem.items.rotationY < -180 && (panoItem.items.rotationY -= 360);
			var o = t - panoItem.items.rotationY,
				r = i - panoItem.items.rotationX;
			Math.abs(o) < .1 ? panoItem.items.rotationY = t : panoItem.items.rotationY += .25 * o, Math.abs(r) < .1 ? panoItem.items.rotationX = i : panoItem.items.rotationX += .15 * r, panoItem.items.updateT();
								
			skyLogo.rotationY = pano.panoBg.rotationY - 90;
	        skyLogo.rotationX = pano.panoBg.rotationX;
	        skyLogo.updateT();
								
			var s12 = -150 - 20 * Math.abs(n);
			main.spMain.z += .1 * (s12 - main.spMain.z), 
			main.spMain.updateT(),
			requestAnimationFrame(touchEvent.actiondh);
		},
		init: function(){
			main.stage.on("touchstart", touchEvent.onTouchStart);
			requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame || window.oRequestAnimationFrame ||
			function (callback) {
				setTimeout(callback, 1000 / 60);
			};
			window.ontouchmove = function(e) {e.preventDefault(); };
		}
	}
	
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

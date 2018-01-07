# vue-smart-scroller
##### a vue scroller component base on zynga/scroller
### Usage
```
npm install vue-smart-scroller --save
import VueSmartScroller from 'vue-smart-scroller'
```
```
npm run dev
npm run build
```


### Props:
```
options:Object
options.scrollingX:Boolean true|false //x轴滚动
options.scrollingY:Boolean true|false //y轴滚动
options.animating:Boolean true|false //动画
options.animationDuration:Number 250|Number //动画缓动
options.bouncing:Boolean true|false //x轴滚动
options.locking:Boolean true|false //锁定
options.paging:Boolean false|true //snap to full page width/height
options.snapping:Boolean false|true  //snap to an user definable pixel grid
options.zooming:Boolean false|true  //缩放
options.minZoom:Number 0.5|Number //最小缩放
options.maxZoom:Number 3|Number //最大缩放

```
### Methods
```
setDimensions(clientWidth, clientHeight, contentWidth, contentHeight);
setPosition(clientLeft, clientTop);
setSnapSize(width, height);
activatePullToRefresh(height, activate, deactivate, start);
finishPullToRefresh();
getValues()
zoomTo(level, animate ? false, originLeft ? center, originTop ? center)
zoomBy(factor, animate ? false, originLeft ? center, originTop ? center);
scrollTo(left, top, animate ? false);
scrollBy(leftOffset, topOffset, animate ? false);

```
### Properties
```
scroller 滚动条
values 滚动值
values.left
values.top
values.zoom
max //最大值
max.left
max.top

```

### Event
```
update
start
move
end

```
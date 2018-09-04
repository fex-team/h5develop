# 概述
为更好地满足个性化定制的需求，百度 H5 提供扩展脚本能力，用户可以通过编写 js 和 css 代码自由控制 H5 页面上的组件，
处于安全考虑，扩展脚本仅支持用于预览和独立部署，不支持用于默认发布。用户可在购买开通扩展脚本部署功能后将脚本用于独立部署的 H5。

# 调用入口

H5 同时支持 css 和 js，可利用 `<style>` 和 `<script>` 标签来向页面插入自定义的样式和脚本。
```js
<style>
   /*可在此处编写自定义组件样式*/
</style>

<script>
$h5.ready(function(){
	// 在此处添加自定义 js 代码
});
</script>
```
# API 
## 全局 API
###  $h5.ready([function])
####  - 概述
自定义 js 脚本的入口，在 H5 初始化后自动调取。
#### - 参数
function 扩展脚本的执行函数
#### - 示例
代码
```js
<script>
$h5.ready(function(){
	alert('test');
});
</script>
```
运行结果
![图片](http://agroup-bos.cdn.bcebos.com/5123a0937a45869ae7bbbf00a06f21be3697ce99)

### $h5.getComponentsByName([name])
#### - 概述
通过组件名或规则获取组件实例列表
#### - 参数
**name {string|Regex|Array}**
组件名或者组件名的规则，组件名通过下方时间线面板双击编辑设定。
#### - 返回
components {Array}：组件对象数组，即使只有一个符合条件的对象，也会返回一个长度为 1 的数组
#### - 示例 1
界面
![图片](http://agroup-bos.cdn.bcebos.com/eb7752b205b1ea3ee45c581958e43fe42b96f76c)
代码
```js
<script>
    $h5.ready(function(){
        let button = $h5.getComponentsByName('button');
        console.log(button);
    });
</script>
```
运行结果
![图片](http://agroup-bos.cdn.bcebos.com/713b4e4c3be374c912d33e107d006b3cbe51de2d)

#### - 示例 2
界面
![图片](http://agroup-bos.cdn.bcebos.com/cddcc612a20e47e146ddd55031b21fe47a834d22)

代码
```js
// 示例：时钟效果
<script>
    $h5.ready(function(){
        let component = $h5.getComponentsByName('labelTime')[0];
        function render() {
            let now = new Date();
            component.instance.attributeChange({
                html: '<p style="text-align: center;"><span style="color: #3981eb; font-size: 36px;">' + 
                [
                    now.getHours(), now.getMinutes(), now.getSeconds()
                ].join(':').replace(/\b\d\b/g, '0$&') + 
                '</span></p>'
            });
            setTimeout(render, 1000);
        }
        render();
    });
</script>
```
运行结果
![图片](http://agroup-bos.cdn.bcebos.com/cddcc612a20e47e146ddd55031b21fe47a834d22)

## 组件 API
H5 的每个组件外都包裹着 `.lg-trailer` 和 `.lg-surface` 两个 div 容器。
```html
<!-- trailerElement -->
<div class="lg-trailer">
     <!-- sufaceElement -->
     <div class="lg-surface">
	     「组件容器」
	 </div>
</div>
``` 
+ `.lg-surface` 是组件的容器，放置特效的地方（边框、阴影）
+ `.lg-trailer` 是动画效果容器（位置和动画）
+ 获取两个 DOM 元素的方法: 
```js
	$h5.getComponentsByName('xxx')[0].surfaceElement;
	$h5.getComponentsByName('xxx')[0].trailerElement;
```

### Component.hide()
#### - 概述
隐藏组件
#### - 示例
界面
![图片](http://agroup-bos.cdn.bcebos.com/609e00a00e8cb87f25884350445e3b203f65fa61)
代码
```js
<script>
    $h5.ready(function(){
        // 兼容移动端和 PC 预览
        const clickEvent = (function() {
            if ('ontouchstart' in document.documentElement === true)
                return 'touchstart';
            else
                return 'click';
            }
        )();

        let rect = $h5.getComponentsByName('rect')[0];
        let button = $h5.getComponentsByName('button')[0];
        button.surfaceElement.addEventListener(clickEvent, e => {
            rect.hide();
        });
    });
</script>
```
运行结果
当点击按钮时方块隐藏
![图片](http://agroup-bos.cdn.bcebos.com/354b5f8d415415a3a21e7040b78704df191e5adf)
### Component.show()
#### - 概述
显示已经隐藏/默认隐藏的组件
界面
![图片](http://agroup-bos.cdn.bcebos.com/56c851cf0472e6a2082c637052dcd4d6189660c0)
代码
```js
<script>
    $h5.ready(function(){
        // 兼容移动端和 PC 预览
        const clickEvent = (function() {
            if ('ontouchstart' in document.documentElement === true)
                return 'touchstart';
            else
                return 'click';
            }
        )();

        let rect = $h5.getComponentsByName('rect')[0];
        let button = $h5.getComponentsByName('button')[0];
        button.surfaceElement.addEventListener(clickEvent, e => {
            rect.show();
        });
    });
</script>
```
运行结果
点击按钮，方块显示
![图片](http://agroup-bos.cdn.bcebos.com/1fe4593246f9eb4b08b107c078cff0b76be47683)
### [临时接口] Component.instance.attributeChange([Attributes])
#### - 概述
用于更新组件属性的接口，调用方式后续可能更改
#### - 参数
[Attributes] 期望更改的属性列表
#### - 示例
界面
![图片](http://agroup-bos.cdn.bcebos.com/893fe7699add849367c1fa35ae26d166fdb4eef2)

代码
```js
<script>
    $h5.ready(function(){
        // 兼容移动端和 PC 预览
        const clickEvent = (function() {
            if ('ontouchstart' in document.documentElement === true)
                return 'touchstart';
            else
                return 'click';
            }
        )();

        let rect = $h5.getComponentsByName('rect')[0];
        let button = $h5.getComponentsByName('button')[0];
        button.surfaceElement.addEventListener(clickEvent, e => {
            rect.instance.attributeChange({
                fill: `rgb(
                    ${parseInt(Math.random() * 256)},
                    ${parseInt(Math.random() * 256)},
                    ${parseInt(Math.random() * 256)})`
            });
        });
    });
</script>
```
运行结果
点击按钮时方块的颜色会随机变化
![图片](http://agroup-bos.cdn.bcebos.com/497fca9154acbc2184df45b83911f45dc3f42e4f)













# vue-scrollbars

![GitHub](https://img.shields.io/github/license/mashape/apistatus.svg) ![Github file size](https://img.shields.io/badge/size-10kb-brightgreen.svg)
![npm](https://img.shields.io/badge/npm-6.4.1-red.svg)
![node.js](https://img.shields.io/badge/node.js-v10.9.0-blue.svg)

> 基于 Vue 的 PC 端滚动组件 简单 轻量 高效
> 仅对原生滚动组件进行滚动条样式统一化处理，并添加自动隐藏,拖动滚动等常用功能，利用动态滚动宽度监测来填平平台之间的差异

## github

[github](https://github.com/zhangzhengyi12/vue-scrollbars)

if you like , click star , thanks!

## DEMO

[live-demo](https://yinode.tech/vue-scrollbars/)

使用示例请看 app.vue

## How to use

```bash
sudo npm i @zhangzhengyi12/vue-scrollbars --svae
```

global regisiter

```js
import Vue from 'vue'
import Scrollerbars from '@zhangzhengyi12/vue-scrollbars'
Vue.use(Scrollerbars)
```

OR in component

```js
import Scrollerbars from '@zhangzhengyi12/vue-scrollbars'

// in vue component

export default {
  components: { Scrollerbars }
}
```

```xml
<scrollbars style="height:200px" autoHide>
  <!-- you content -->
</scrollerbars>
```

## Props

| name               | type     | desc                                      | default |
| ------------------ | -------- | ----------------------------------------- | ------- |
| autoHide           | Boolean  | auto hide scrollbar                       | false   |
| hideTime           | Boolean  | auto Hide Time                            | 1000    |
| data               | Array    | on watch Data Change refresh Bar          | []      |
| displayBar         | Boolen   | display scrollbar                         | true    |
| listenScrollBottom | Function | Called when scrolling to the bottom       | false   |
| scrollBottomHeight | Number   | listenScrollBottom handler trigger height | 50      |
| listenScrollRight  | Function | Called when scrolling to the right        | false   |
| scrollBottomHeight | Number   | listenScrollRight handler trigger height  | 50      |

## APIS

| name      | params | desc              |
| --------- | ------ | ----------------- |
| refresh() | null   | refersh scrollbar |

> this.\$refs.scrollbars.refresh()

## Event

| name    | params              | desc                |
| ------- | ------------------- | ------------------- |
| @scroll | scroll event object | native scroll Event |

## 特性

### 自动监测滚动条

这里了解这个组件的原理是利用 容器的 Overflow:hidden 属性 ，随后将 content 设置一个负的 marigin-bottom & margin-right 把滚动条顶出容器 已达到隐藏的效果 （一般来说这个值就是滚动条的宽度），但是在不同平台的不同设备上，这个宽度是不一致的，在我的 mbp15 inch 上 这个值是 15 像素，而在一台 windows 分辨率 1920\*1080 的设备上，这个宽度为 17

The principle of understanding this component here is to use the container's Overflow:hidden property, and then set the content to a negative marigin-bottom & margin-right. The scroll bar is pushed out of the container to achieve a hidden effect (generally this value is the scroll bar) Width), but this width is inconsistent on different devices on different platforms. This value is 15 pixels on my mbp15 inch, and on a 1920\*1080 windows resolution, this width is 17

**所以我加入了一个动态检测滚动条宽度的功能，能够让滚动条自动适应你的设备**

**So I added a feature that dynamically detects the width of the scrollbar, allowing the scrollbar to automatically adapt to your device.**


## 自定义

考虑再三还是还是没有加入 bar 的颜色等常用 Props, 如有需要，请使用样式优先级进行覆盖。

## issues相关

麻烦报告问题的老哥多给点信息 最起码浏览器版本给写上 不然我没法定位啊

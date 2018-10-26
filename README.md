# vue-scrollbars

> 基于 Vue 的 PC 端滚动组件 简单 轻量 高效
> 仅对原生滚动组件进行滚动条样式统一化处理，并添加自动隐藏等常用功能

## DEMO

[live-demo](http://yinode.tech/vue-scrollbars/)

## How to use

```bash
npm i zhangzhengyi@vue-scrollbars --svae
```

global regisiter

```js
import Vue from 'vue'
import Scrollerbars from 'zhangzhengyi12@vue-scrollbars'
Vue.use(Scrollerbars)
```

OR in component

```js
import Scrollerbars from 'zhangzhengyi12@vue-scrollbars'

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

| name | type | desc | default |
| autoHide | Boolean | auto hide scrollbar | false |
| hideTime | Boolean | auto Hide Time | 1000 |
| data | Array | on watch Data Change refresh Bar| |

## APIS

| name | params | desc |
| refresh() | null | refersh scrollbar|

> this.$refs.scrollbars.refresh()

## TODO

- scroll event listen
- shortcut scroll API

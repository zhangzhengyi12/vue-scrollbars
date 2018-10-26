# vue-scrollbars

> 基于 Vue 的 PC 端滚动组件 简单 轻量 高效
> 仅对原生滚动组件进行滚动条样式统一化处理，并添加自动隐藏等常用功能

## DEMO

TBD

## How to use

> npm i zhangzhengyi@vue-scrollbars --svae

global regisiter

> import Vue from 'vue'
> import Scrollerbars from 'zhangzhengyi12@vue-scrollbars'
> Vue.use(Scrollerbars)

OR in component

> import Scrollerbars from 'zhangzhengyi12@vue-scrollbars
> components:{Scrollerbars}


```html
<scrollbars style="height:200px" autoHide>
  <!-- you content -->
</scrollerbars>
```

## Props

| name | type | desc | default |
| autoHide | Boolean | auto hide scrollbar | false |
| hideTime | Boolean | auto Hide Time | 1000 |
| data | Array | on watch Data Change refresh Bar|  |

## APIS

| name | params | desc |
| refresh() | null | refersh scrollbar|

> this.$refs.scrollbars.refresh()

## TODO

- scroll event listen
- shortcut scroll API







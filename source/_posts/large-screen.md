---
title: large-screen
date: 2021-08-24 17:16:59
tags:
---
# 大屏适配解决方案
## 背景
近期公司投放在展厅大屏中演示的大数据页面，出现了文字、图表、表格等多类组件显示错乱的情况。

## 目的
解决页面错乱问题，实现多种分辨率的大屏适配

## 入坑分析
俗话说的好，走过的最多路，就是网友们套路
### 1. vh、vw、rem组合方式
body,html {
  font-size:1vw
}
**描述：** 定义父级大小，之后进行组件的height、margin、padding等多种css属性采用rem作为单位，继承父级设置等单位（1vw）,实现适配。

**坑点** 仅能满足一些特定的小场景，大屏中的实施非常不友好，例如我们覆盖element表格中的为font-size：0.5rem。此时投放到大屏，字体是自适应的，但其组件的多种默认属性用px为单位。如line-height的设置为22px，此时覆盖样式后的字体过大，并不能适应行高，就会出现文字重叠等错乱问题。

### 2. 采用font.js+rem的组合
```javascript
(function(doc, win) {
  var docEl = doc.documentElement
  var resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
  var recalc = function() {
    var clientWidth = docEl.clientWidth
    if (!clientWidth) return
    docEl.style.fontSize = 100 * (clientWidth / 1920) + 'px'
  }
  if (!doc.addEventListener) return
  win.addEventListener(resizeEvt, recalc, false)
  doc.addEventListener('DOMContentLoaded', recalc, false)
})(document, window)
```
**描述：** 引用第三方脚本，通过在main.js中写代码计算，使用rem进行继承,实现适配。

**坑点：** 原理跟上述方法1是一样的，同样无法实现大屏适配，坑点不在赘述。

### 解决思路
#### 山重水复疑无路，柳暗花明又一村
当自己没有好的方法时，我们不妨借助下'巨人肩膀'的力量。本人找了下百度、网易等大数据适配的解决方案。通过线上案例发现如下所示规律（下方是我摘取的[网易有数](https://youdata.163.com/)截图）：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77b1e879360c4fa88e1404ff62801973~tplv-k3u1fbpfcp-watermark.awebp)

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5fe547d4549424db27a39944541e860~tplv-k3u1fbpfcp-watermark.awebp)

## 逻辑分析：
上图案例中采用了css3的缩放transform: scale(X)属性，对比两个图，我们不难发现本人改变分辨率时，scale的值是变化的。前端小伙们们看到这，想必也已经明白了大概。
我们只要监听浏览器窗口大的小，同时控制变化的比例就可以了。

### 1. 封装一个全局组件
以vue 为主，直接粘贴本人编写的代码如下，**该公共组件为了下文中方便描述，命名为ScreenAdapter**
```javascript
<template>
  <div
    class="ScreenAdapter"
    :style="style"
  >
    <slot />
  </div>
</template>
<script>
export default {
  name: '',
  //参数注入
  props: {
    width: {
      type: String,
      default: '1920' 
    },
    height: {
      type: String,
      default: '1080' 
    }
  },
  data() {
    return {
      style: {
        width: this.width + 'px',
        height: this.height + 'px',
        transform: 'scale(1) translate(-50%, -50%)'
      }
    }
  },
  mounted() {
    this.setScale()
    window.onresize = this.Debounce(this.setScale, 1000)
  },
  methods: {
    Debounce: (fn, t) => {
      const delay = t || 500
      let timer
      return function() {
        const args = arguments
        if (timer) {
          clearTimeout(timer)
        }
        const context = this
        timer = setTimeout(() => {
          timer = null
          fn.apply(context, args)
        }, delay)
      }
    },
    // 获取放大缩小比例
    getScale() {
      const w = window.innerWidth / this.width
      const h = window.innerHeight / this.height
      return w < h ? w : h
    },
    // 设置比例
    setScale() {
      this.style.transform = 'scale(' + this.getScale() + ') translate(-50%, -50%)'
      console.log('任你千变万化,我都不会影响性能')
    }
  }
}
</script>
<style lang="scss" scoped>
.ScreenAdapter {
  transform-origin: 0 0;
  position: absolute;
  left: 50%;
  top: 50%;
  transition: 0.3s;
  background: red;
}
</style>
```
### 2. 将此组件作为外壳，包在我们搭建的页面上
```javascript
<ScreenAdapter> <div>大家好，我是大屏展示页面</div> </ScreenAdapter>
```

### 3. 定义网页规范
- 根据美工给出的设计（主要获取美工给出的分辨率，如1920*1080）。
- Div布局多采用flex+百分比布局（当然也可以根据美工给出的设计，默认写px。）。
- 各类空间设计，根据美工给出的px进行定义即可
### 问题统一回复
问题：很多朋友说加入后会有留白的情况，需要调试。
解决：是因为注入的宽高，跟电脑实际分辨率宽高不一致的原因

问题：注入的宽高，跟分辨率宽高一致，为什么还有留白？
解决：

1. 一致的话，只需要将浏览器全屏就可以看到正确的效果.
2. 将注入的宽高换成document.documentElement.clientWidth、document.documentElement.clientHeight。即使不全屏也可以得到正确的结果

这下大家应该明白了吧😊，基础很重要哦，哈哈～～～

## 结束感言
按照上述步骤操作，即可完全按照美工的设计在大屏中进行等比例输出放大，趋于完美的解决了大屏适配问题。

互联网的精神在于分享，望本文能够帮助到一些跟我遇到一样问题的朋友。

分享点滴技术，记录美好生活。

转载于 https://juejin.cn/post/6972416642600927246#heading-3
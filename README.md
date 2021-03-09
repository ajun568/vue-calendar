# vue-calendar

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2acb32cd05840a995affd30a263043b~tplv-k3u1fbpfcp-watermark.image)

> Hello, 各位勇敢的小伙伴, 大家好, 我是你们的嘴强王者小五, 身体健康, 脑子没病.
>
> 本人有丰富的脱发技巧, 能让你一跃成为资深大咖.
>
> 一看就会一写就废是本人的主旨, 菜到抠脚是本人的特点, 卑微中透着一丝丝刚强, 傻人有傻福是对我最大的安慰.
>
> 欢迎来到`小五`的`随笔系列`之`手摸手教你用VUE封装日历组件`.

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

## 写在前面

**双脚奉上最终效果图:**

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04ce32ccee804e5286b2dd209f1a6ebf~tplv-k3u1fbpfcp-watermark.image)

## 需求分析

需求分析无非是一个想要什么并逐步细化的过程, 毕竟谁都不能一口吃掉一张大饼, 所以我们先把饼切开, 一点一点吃. 以下基于特定场景来实现一个基本的日历组件. 小生不才, 还望各位看官轻喷, 欢迎各路大神留言指教.

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8ed495c3f9a4e69a67c9b3ae752e483~tplv-k3u1fbpfcp-watermark.image)

场景: 在`移动端`中通过`切换日期`来切换收益数据, 展现形式为上面日历, 下面对应数据, 只显示`日数据`.

基于此场景, 我们对该日历功能进行需求分析

* 普遍场景下, 我们更倾向当天的数据情况. 所以基于此, 首次进入应展示当前月份且选中日期为今日

* 点选日期, 应可以准确切换, 否则做它何用, 当🌹瓶吗

* 切换月份, 以查看更多数据. 场景基于移动端, 交互方式选择体验更好的滑动切换, 左滑切换至上一月, 右滑切换至下一月

* 滑动切换月份后, 选中该月1号

* 移动端的展示区域非常宝贵, 减少占用空间显得极为重要, 这时候周视图就有了用武之地. 交互上可上滑切换至周视图, 下拉切换回月视图.

* 明确月视图滑动切月, 周视图滑动切周

* 滑动切换星期后, 选中该星期的第一天, 若左滑切换后存在1号, 选中1号

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2e33e6ee57e42aabd832eb539ca9829~tplv-k3u1fbpfcp-watermark.image)

## 结构及样式

先拆分一下日历, 可将其上下拆分成两部分, 上面的 `星期` 部分, 和下面的 `数据` 部分, 一周7天限定了列数为**7列**, 行数会随**当月天数**及**1号所在位置**而有所不同.

移动端亦应根据屏幕宽度自适应布局, `flex`布局就是一个很好的选择, 我们对数据部分进行下模拟, 先造一个长度为40数据都为0的数组如下:

```js
const dataArr = Array(40).fill(0, 0, 40)
```

现在, 我们想要每排显示7个, 顺次下移, 不妨想一下, 如果是你, 你会怎么做?

* 父元素设置

  * `flex-direction` : 用于定义主轴方向

  * `flex-wrap` : 用于定义是否换行

  * `flex-flow` : 同时定义`flex-direction`和`flex-wrap`

* 子元素设置

  * `flex-basis` : 用于设置伸缩基准值，可设置具体宽度或百分比，默认值是auto

  * `flex-grow` : 用于设置放大比例，默认为0，如果存在剩余空间，该元素也不会被放大

  * `flex-shrink` : 用于设置缩小比例，默认为1，如果空间不足，将等比例缩小。如果设置为0，则它不会被缩小

  * `flex` : `flex-grow`、`flex-shrink`和`flex-basis`的缩写

综上, 我们可以设置样式为 👉🏼 &nbsp; **父** &nbsp; `flex: row wrap` &nbsp; **子** &nbsp; `flex: 0 0 14.285%` (1/7 ≈ 14.285%)

**效果图 👇**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ecf5c883dcd141d59b2d0bf5ff320320~tplv-k3u1fbpfcp-watermark.image)

**代码片段 👇**

<img src=https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6d629b0715a94fe39d24f3ae8a219695~tplv-k3u1fbpfcp-watermark.image width=600 />

此时, 可以加一层结构, 让子元素宽高固定为40✖️40, 方便对选中后的样式进行处理

**我们来随意勾勒两笔样式, 呈现如下 👇**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa8b5a6157234401b9fe0f209253fa57~tplv-k3u1fbpfcp-watermark.image)

## 展示当前月份及选中当天日期

凭空想象哪有直接上图片来的直观, 就像老板画的饼哪有money来的实在😏, 接下来我们结合下面图片进行进一步的分析, 图片为我截取的手机日历图

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9a587a0bbc34aa09e7e8118db2a730b~tplv-k3u1fbpfcp-watermark.image)

首先, 既然是默认选中今天, 我们就先来获取下当前日期

```js
// 获取当前日期
getCurrentDate() {
  this.selectData = {
    year: new Date().getFullYear(),
    month: new Date().getMonth() + 1,
    day: new Date().getDate(),
  }
}
```

我们来看下这张图片, 不考虑蓝框中的部分, 要显示出当月日期, 我们只需知道以下两个点, 然后做for循环就可以了.

1. 当前月份的天数

2. 当前月份第一天应该显示在什么位置

这么一看, 是不是 so easy! 不要太简单有木有.

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/697e602747a54cd9aceab9cf7220aec6~tplv-k3u1fbpfcp-watermark.image)

**当月天数**

**“一三五七八十腊, 三十一天永不差”**, 每年除了二月分平年闰年以外, 其余月份的天数都是固定的, 这么一看, 这不是区分下二月就完事了吗

```js
const { year } = this.selectData
let daysInMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]

if ((year % 4 === 0 && year % 100 !== 0) || year % 400 === 0) { // 闰年处理
  daysInMonth[1] = 29
}
```

**当月第一天的位置**

想知道当月第一天的位置, 换个思路想, 其实就是想知道当月第一天是星期几, 诶, 这不是巧了吗, 拿当月第一天的日期 `getDay()` 这不就完事了吗

```js
const { year, month } = this.selectData
const monthStartWeekDay = new Date(year, month - 1, 1).getDay()
```

接下来我们填充下数据, 前后做留白处理, 代码及效果如下:

**🧟‍♂️ Code**

<img src=https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/341d4a6836f3427abb3f708cf7839aae~tplv-k3u1fbpfcp-watermark.image width=600 />

**🧟‍♂️ Image**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8985a0afba5e4a19a6d808afd0baca9c~tplv-k3u1fbpfcp-watermark.image)

## 日期切换及月份切换

**日期切换 = 更改当前数组中子元素的`isSelected`**

```js
// 切换点选日期
checkoutDate(selectData) {
  if (selectData.type !== 'normal') return // 非有效日期不可点选

  this.selectData.day = selectData.day // 对选中日期赋值

   // 查找当前选中日期的索引
  const oldSelectIndex = this.dataArr.findIndex(item => item.isSelected && item.type === 'normal')
  // 查找新切换日期的索引 (tips: 这里也可以直接把索引值传过来 -> index)
  const newSelectIndex = this.dataArr.findIndex(item => item.day === selectData.day && item.type === 'normal')

  // 更改isSelected值
  if (this.dataArr[oldSelectIndex]) this.$set(this.dataArr[oldSelectIndex], 'isSelected', false)
  if (this.dataArr[newSelectIndex]) this.$set(this.dataArr[newSelectIndex], 'isSelected', true)
}
```

**月份切换 = 重新生成新月份所对应的`dataArr`, 并选中当月1号**

**tips:** 这里需要注意的点是, **1月的上一月**和**12月的下一月**, 以上一月举例:

```js
checkoutPreMonth() {
  let { year, month, day } = this.selectData
  if (month === 1) {
    year -= 1
    month = 12
  } else {
    month -= 1
  }

  this.selectData = { year, month, day: 1 }
  this.dataArr = this.getMonthData(this.selectData)
},
```

**今日**

```js
checkoutCurrentDate() {
  this.getCurrentDate()
  this.dataArr = this.getMonthData(this.selectData)
},
```

至此, 一个基本的月视图就实现完毕了

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/055466a35cbd44d99026a62d209e8c39~tplv-k3u1fbpfcp-watermark.image)

## 滑动切月

接下来我们来对月视图进行优化, 增加滑动切月的功能. 我们先来看一下实现的效果👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2ced2e61a989431cb23b4b7b8a4c8a5b~tplv-k3u1fbpfcp-watermark.image)

以左滑为例:

* 滑动过程中, 我们可以看到部分下个月的数据

* 滑动距离过小, 自动回弹到当前视图

* 滑动超过一定距离, 自动滑至下一个月

**touch**

作案是需要工具的, 想要触发滑动事件, 得先找到对应的工具

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3dd1592fd144c50a7ca6a8b24c4c06a~tplv-k3u1fbpfcp-watermark.image)

* `touchstart` : 手指触摸屏幕时触发

* `touchmove` : 手指在屏幕中拖动时触发

* `touchend` : 手指离开屏幕时触发

光靠这个事件, 在滑动过程中是无法看到下个月的部分数据的, 想要在滑动过程中看到数据, 这就是典型的轮播场景. 本质上就是一次`transform`的过程.

<img src=https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06ae53103e3749afb7e61782e3e86525~tplv-k3u1fbpfcp-watermark.image width=400 />

此时, 我们调整下页面结构, 由对`dataArr`的单层循环改为双层循环模式, 其本质就是上图所示的`[pre, current, next]`数组

此步骤涉及的代码改动较多, 接下来主要通过新引入的变量来捋清思路, 思路清晰了, 代码顺其自然就好, 👀 Let's go, come on baby!

```js
allDataArr: [], // 轮播数组
isSelectedCurrentDate: false, // 是否点选的当月日期
translateIndex: 0, // 轮播所在位置
transitionDuration: 0.3, // 动画持续时间
needAnimation: true, // 左右滑动是否需要动画
isTouching: false, // 是否为滑动状态
touchStartPositionX: null, // 初始滑动X的值
touchStartPositionY: null, // 初始滑动Y的值
touch: { // 本次touch事件，横向，纵向滑动的距离的百分比
  x: 0,
  y: 0,
},
```

**`allDataArr` - 轮播数组**

❓ 什么时候对这个数组进行赋值

🅰️ 当`[pre, current, next]`中任意值变化时, 而`pre`和`next`的变化都依附于`current`的变化, Wow, interesting! watch watch watch !!!

**`isSelectedCurrentDate` - 是否点选的当月日期**

❓ 在点选切换数据时, 因为`isSelected`的变化, `watch`监听并执行赋值操作, 但此时并没有必要重新生成`pre`和`next`

**`translateIndex` - 轮播所在位置**

用于控制`pre, current, next`位置, 当触发滑动切月时, 通过更改`translateIndex`来更改位置. 在重新赋值时还原到初始值.

**`touchStartPositionX`, `touchStartPositionY`, `touch`**

这三个是为了确定滑动方向及距离的, 向什么方向滑动? **(不要和我说你任性, 就想斜着滑动)** 滑动多远? 松手后, 滑动距离小做回弹处理, 滑动距离大做切换处理 **(结合`translateIndex`, 我知道你懂得)**

**`needAnimation` - 左右滑动是否需要动画**

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a97457ab1124958b4447a1fa27679ac~tplv-k3u1fbpfcp-watermark.image)

我们看图说话(👆), 是不是感觉这个动画怪怪的, 但又说不清楚哪里怪, 那是因为在动画进行中时候, 我们就对`allDataArr`进行了赋值操作, 我们在定时器中延迟下这个赋值操作, 效果如下(👇):

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c63c73f341004499ae482a7aeb7c5f61~tplv-k3u1fbpfcp-watermark.image)

是不是有一个明显的反复横跳的过程, 因为我们滑动过去时候在`next`, 但最后回到的是`current`. 这点小问题怎么能限制住我们的聪明大脑, 将回到`current`的动画去掉, 不就完美解决问题了吗.

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ff7a6c49b3a466e9f17575a5262ea0b~tplv-k3u1fbpfcp-watermark.image)

**赋部分代码片段:**

<img src=https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/451882287f654706a91dd3c0347580f7~tplv-k3u1fbpfcp-watermark.image width=600 />

<img src=https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e02d5f2ce5444ac3a76427d3bf8c63d5~tplv-k3u1fbpfcp-watermark.image width=600 />

<img src=https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3e66e163e9a489dab53d35f0705c0aa~tplv-k3u1fbpfcp-watermark.image width=600 />

## 切换周视图

还是看图说话, 文字哪有图片直观, 我们来分析下切换周的过程:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e900fe4ea960453fb420e5d4b189c590~tplv-k3u1fbpfcp-watermark.image)

Bingo, 就是一个`transformY`+`height`的过程

👉 对于`height`, 无非是总高度到单行高度反复横跳的过程, 每行高度是固定的, 总高度=单行高度*总行数

```js
isWeekView: false, // 周视图还是月视图
itemHeight: 50, // 日历行高
lineNum: 0, // 当前视图总行数

this.lineNum = Math.ceil(this.dataArr.length / 7)
```

👉 对于`transformY`, 其移动距离=(当前所在行数-1)*单行高度

```js
offsetY: 0, // 周视图 Y轴偏移量

// 处理周视图的数据变化
dealWeekViewData() {
  const selectedIndex = this.dataArr.findIndex(item => item.isSelected)
  const indexOfLine = Math.ceil((selectedIndex + 1) / 7)
  this.offsetY = -((indexOfLine - 1) * this.itemHeight)
},
```

## 补全视图信息

在做周视图的滑动切换之前, 我们来补全一下视图信息, 将`daraArr`的空白处填上对应日期.

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5035a568d7544368c8e4553ccfffd11~tplv-k3u1fbpfcp-watermark.image)

年和月的填充就不说了, 简单说下日的填充

`next`比较简单, **循环次数=7-最后一行天数=7-次月1日的星期索引** (tip: 需要注意的是, 若次月1日索引为0, 代表无空白处可填充, 自然也无需循环), `day`的赋值**从1号顺次增加**即可.

```js
const nextInfo = this.getNextMonth()

let nextObj = {
  type: 'next',
  day: i + 1,
  month: nextInfo.month,
  year: nextInfo.year,
}
```

再来说说`pre`, **循环次数=7-第一行天数=当月1号的星期索引**, `day`的赋值等于**上月日期的倒序 => 上月天数 - (当月1号星期索引 - (index + 1))**

```js
const preInfo = this.getPreMonth(date)

let preObj = {
  type: 'pre',
  day: daysInMonth[preInfo.month - 1] - (monthStartWeekDay - i - 1),
  month: preInfo.month,
  year: preInfo.year,
}
```


❓ 这里`getPreMonth()`函数传`date`的原因

🅰️ 说白了, date就是参照物呗, 对谁取上个月就传谁; 而`getNextMonth()`为什么不传呢, 单纯的无所谓, 传与不传它都是从1递增, 谁又会在一个无关紧要的事上浪费感情呢.

点选非本月日期时, 对应做切换月份的处理即可, 此时切换后的日期为点选日期, 而非1号

## 滑动切换星期

在视图切换的过程中, 与我们一同上下摩擦的, 还是陪着我们不离不弃的`preArr`和`nextArr`. 既然甩不掉, 何不将它们的价值榨干到极致, 这样才符合利益最大化嘛, 我们对同一横行的前后数据做狸猫换太子的操作, 将其分别换成当前数据的前一周和后一周, 毕竟破坏才是更好的创造.

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4a2e483b9b3044f09061af6158956ee3~tplv-k3u1fbpfcp-watermark.image)

要想狸猫换太子, 得先找到那只狸猫, 在找到太子, 才能进行两者的对调. 我们以切换至上一周为例, 来具体找一下狸猫和太子.

* 狸猫 - `lastWeek`

No.1 如果非首行数据, 上周=上一行. 通过当前行数, 拿到两端数据的索引, 分别减7获取上一周两端数据的索引, 进而拿到上一周的数据.

No.2 如果当前为首行, 又可进一步划分为: 首个数据项是否为1号, 若是, 则取上个月最后一行数据; 若否, 则取上个月倒数第二行数据`(tips: 此时上个月最后一行等同于当前首行)`; 以上两点, 也可考虑成查找特定日期在上个月的所在行.

* 太子 - 平行世界的当前行

```js
// 获取处理周视图所需的位置信息
getInfoOfWeekView(selectedIndex, length) {
  const indexOfLine = Math.ceil((selectedIndex + 1) / 7) // 当前行数
  const totalLine = Math.ceil(length / 7) // 总行数
  const sliceStart = (indexOfLine - 1) * 7 // 当前行左端索引
  const sliceEnd = sliceStart + 7 // 当前行右端索引

  return { indexOfLine, totalLine, sliceStart, sliceEnd }
},

// 处理lastWeek、nextWeek, 并返回替换行索引
dealWeekViewSliceStart() {
  const selectedIndex = this.dataArr.findIndex(item => item.isSelected)
  const {
    indexOfLine,
    totalLine,
    sliceStart,
    sliceEnd
  } = this.getInfoOfWeekView(selectedIndex, this.dataArr.length)

  this.offsetY = -((indexOfLine - 1) * this.itemHeight)

  // 前一周数据
  if (indexOfLine === 1) {
    const preDataArr = this.getMonthData(this.getPreMonth(), true)
    const preDay = this.dataArr[0].day - 1 || preDataArr[preDataArr.length - 1].day
    const preIndex = preDataArr.findIndex(item => item.day === preDay && item.type === 'normal')
    const { sliceStart: preSliceStart, sliceEnd: preSliceEnd } = this.getInfoOfWeekView(preIndex, preDataArr.length)
    this.lastWeek = preDataArr.slice(preSliceStart, preSliceEnd)
  } else {
    this.lastWeek = this.dataArr.slice(sliceStart - 7, sliceEnd - 7)
  }

  // 后一周数据
  if (indexOfLine >= totalLine) {
    const nextDataArr = this.getMonthData(this.getNextMonth(), true)
    const nextDay = this.dataArr[this.dataArr.length - 1].type === 'normal' ? 1 : this.dataArr[this.dataArr.length - 1].day + 1
    const nextIndex = nextDataArr.findIndex(item => item.day === nextDay)
    const { sliceStart: nextSliceStart, sliceEnd: nextSliceEnd } = this.getInfoOfWeekView(nextIndex, nextDataArr.length)
    this.nextWeek = nextDataArr.slice(nextSliceStart, nextSliceEnd)
  } else {
    this.nextWeek = this.dataArr.slice(sliceStart + 7, sliceEnd + 7)
  }

  return sliceStart
},

dealWeekViewData() {
  const sliceStart = this.dealWeekViewSliceStart()
  this.allDataArr[0].splice(sliceStart, 7, ...this.lastWeek)
  this.allDataArr[2].splice(sliceStart, 7, ...this.nextWeek)
},
```

## 优化代码

到这里基本就大功告成了, 我们总结下剩下的问题并加以处理, 阿拉霍洞开

* 一些蹩脚的动画: 此场景下, 一切奇怪的动画都是由`transitionDuration`导致的, 所以我们要想清楚什么时候需要动画, 什么时候不需要, 不需要时候赋值为0就好了

* 类似卡顿的效果: 此场景下, 几乎所有的卡顿、延迟, 都是那个万恶的`setTimeout`导致的, 所以要想好什么时候需要它, 什么时候果断舍弃它

* 最后加个底部的touch条, 使其更美观些

## 参考🔗链接

[Github - 基于 vue 2.0 开发的轻量，高性能日历组件](https://github.com/zwhGithub/vue-calendar)

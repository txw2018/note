# box-shadow

`**box-shadow**` 属性用于在元素的框架上添加阴影效果 ，该属性可设置的值包括，X偏移，Y偏移，阴影模糊半径，阴影扩散半径，和阴影颜色并以多个逗号分隔。

##### 取值

- `inset`

  如果没有指定`inset`，默认阴影在边框外，即阴影向外扩散。 使用 `inset` 关键字会使得阴影落在盒子内部，这样看起来就像是内容被压低了。 此时阴影会在边框之内 (即使是透明边框）、背景之上、内容之下。

- ` <offset-x>` ` ` `<offse-y>` 

  这是头两个 [`length`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) 值，用来设置阴影偏移量。x,y 是按照数学二维坐标系来计算的，只不过y垂直方向向下。 `` 设置水平偏移量，正值阴影则位于元素右边，负值阴影则位于元素左边。 `` 设置垂直偏移量，正值阴影则位于元素下方，负值阴影则位于元素上方。可用单位请查看 [``](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) 。

  如果两者都是0，那么阴影位于元素后面。这时如果设置了`` 或`` 则有模糊效果。需要考虑 `inset` 

- `<blur-radius>`

  这是第三个 [`<length>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) 值。值越大，模糊面积越大，阴影就越大越淡。 不能为负值。默认为0，此时阴影边缘锐利。本规范不包括如何计算模糊半径的精确算法，但是，它详细说明如下：

> 对于长而直的阴影边缘，它会创建一个过渡颜色用于模糊 以阴影边缘为中心、模糊半径为半径的局域，过渡颜色的范围在完整的阴影颜色到它最外面的终点的透明之间。 （译者注：对此有兴趣的可以了解下数字图像处理的模糊算法。）

- `<spread-radius>`

  这是第四个 [`length`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) 值。取正值时，阴影扩大；取负值时，阴影收缩。默认为0，此时阴影与元素同样大。需要考虑 `inset` 

- `<color>`

  相关事项查看 [`color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color_value) 。如果没有指定，则由浏览器决定——通常是[`color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color)的值，不过目前Safari取透明。

```
/* x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: 60px -16px teal;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影颜色 */
box-shadow: 10px 5px 5px black;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

/* 插页(阴影向内) | x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: inset 5em 1em gold;

/* 任意数量的阴影，以逗号分隔 */
box-shadow: 3px 3px red, -1em 0 0.4em olive;

/* 全局关键字 */
box-shadow: inherit;
box-shadow: initial;
box-shadow: unset;
```

[在codepen中打开](https://codepen.io/fellowt/pen/wvBqXMa)
# d3-scale

对于可视化来说，比例尺是一个很方便的工具：将抽象的维度数据映射为可视化表示。虽然经常使用位置编码定量数据，比如将测量单位米使用像素映射。但是比例尺可以对任何视觉编码进行映射，比如颜色，描边的宽度或者符号的大小。比例尺也可以用来对任意类型的数据进行映射，比如分类数据或离散的数据。

对于 [continuous(连续的)](#continuous-scales) 定量数据，通常会使用 [linear scale(线性比例尺)](#linear-scales). (对于时间序列则使用 [time scale(时间比例尺)](#time-scales).) 如果需要也可以使用 [power(幂)](#power-scales) 或 [log(对数)](#log-scales) 比例尺。[quantize scale(量化比例尺)](#quantize-scales) 可以将连续数据四舍五入到一组固定的离散值中，可以用来生成离散数据。类似的 [quantile scale(分位数比例尺)](#quantile-scales) 从样本总体计算分位数，而 [threshold scale(阈值比例尺)](#threshold-scales) 可以为一组连续数据指定分割阈值。

对于离散的顺序（有序）或者分类（无序）数据，[ordinal scale(序数比例尺)](#ordinal-scales) 指定从一组数据值到一组相应视觉属性的显式映射（比如颜色）。相关的 [band](#band-scales) 和 [point](#point-scales) 在对分类数据进行位置编码时很有用，如条形图以及分类散点图。

这个模块不会提供颜色方案，可以参考 [d3-scale-chromatic](https://github.com/xswei/d3-scale-chromatic) 来获取需要的颜色方案。

比例尺没有具体的视觉表现。但是大多数的比例尺可以在显示比例尺的参考轴时候 [generate(生成)](#continuous_ticks) 并 [format(格式化)](#continuous_tickFormat) 刻度。

更多比例尺的介绍可以参考以下推荐的教程:

* [Introducing d3-scale](https://medium.com/@mbostock/introducing-d3-scale-61980c51545f) by Mike Bostock

* Chapter 7. Scales of [*Interactive Data Visualization for the Web*](http://alignedleft.com/work/d3-book) by Scott Murray

* [d3: scales, and color.](http://www.jeromecukier.net/2011/08/11/d3-scales-and-color/) by Jérôme Cukier

## Installing

`NPM` 安装：`npm install d3-scale`. 此外还可以下载 [latest release](https://github.com/xswei/d3-scale/releases/latest). 可以直接从 [d3js.org](https://d3js.org) 以 [standalone library](https://d3js.org/d3-scale.v2.min.js) 或作为 [D3](https://github.com/xswei/d3) 的一部分直接引入。支持 `AMD`, `CommonJS` 以及基本的标签引入形式。如果使用标签引入则会暴露全局 `d3` 变量：

```html
<script src="https://d3js.org/d3-array.v1.min.js"></script>
<script src="https://d3js.org/d3-color.v1.min.js"></script>
<script src="https://d3js.org/d3-format.v1.min.js"></script>
<script src="https://d3js.org/d3-interpolate.v1.min.js"></script>
<script src="https://d3js.org/d3-time.v1.min.js"></script>
<script src="https://d3js.org/d3-time-format.v2.min.js"></script>
<script src="https://d3js.org/d3-scale.v2.min.js"></script>
<script>

var x = d3.scaleLinear();

</script>
```

(如果不使用 [d3.scaleTime](#scaleTime) or [d3.scaleUtc](#scaleUtc) 则可以忽略`d3-time` 和 `d3-time-format`.)

## API Reference

* [Continuous](#continuous-scales) ([Linear](#linear-scales), [Power](#power-scales), [Log](#log-scales), [Identity](#identity-scales), [Time](#time-scales))
* [Sequential](#sequential-scales)
* [Diverging](#diverging-scales)
* [Quantize](#quantize-scales)
* [Quantile](#quantile-scales)
* [Threshold](#threshold-scales)
* [Ordinal](#ordinal-scales) ([Band](#band-scales), [Point](#point-scales))

### Continuous Scales

连续比例尺可以将连续的、定量的输入 [domain](#continuous_domain) 映射到连续的输入 [range](#continuous_range)。如果输出范围也是数值则这种映射关系可以被 [inverted(反转)](#continuous_invert)。连续比例尺是一类比例尺，不能直接使用，可以使用 [linear](#linear-scales), [power](#power-scales), [log](#log-scales), [identity](#identity-scales), [time](#time-scales) 或 [sequential color](#sequential-scales).

<a name="_continuous" href="#_continuous">#</a> <i>continuous</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

根据给定的位于 [domain](#continuous_domain) 中的 *value* 返回对应的位于 [range](#continuous_range) 中的值。如果给定的 *value* 不在 `domain` 中，并且 [clamping](#continuous_clamp) 没有启用，则返回的对应的值也会位于 `range` 之外，这种映射值推算出来的。例如：

```js
var x = d3.scaleLinear()
    .domain([10, 130])
    .range([0, 960]);

x(20); // 80
x(50); // 320
```

或者将数值映射为颜色：

```js
var color = d3.scaleLinear()
    .domain([10, 100])
    .range(["brown", "steelblue"]);

color(20); // "#9a3439"
color(50); // "#7b5167"
```

<a name="continuous_invert" href="#continuous_invert">#</a> <i>continuous</i>.<b>invert</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

根据给定的位于 [range](#continuous_range) 中的 *value* 返回对应的位于 [domain](#continuous_domain) 的值。反向映射在交互中通常很有用，根据鼠标的位置计算对应的数据范围。例如:

```js
var x = d3.scaleLinear()
    .domain([10, 130])
    .range([0, 960]);

x.invert(80); // 20
x.invert(320); // 50
```

如果给定的 *value* 位于 `range` 外面，并且没有启用 [clamping](#continuous_clamp) 则会推算出对应的位于 `domain` 之外的值。这个方法仅仅在 `range` 为数值时有用。如果 `range` 不是数值类型则返回 `NaN`.

对于有效的处于 `range` 中的 *y* 值，<i>continuous</i>(<i>continuous</i>.invert(<i>y</i>)) 近似等于 *y*；同理对于有效的 *x* 值，<i>continuous</i>.invert(<i>continuous</i>(<i>x</i>)) 近似等于 *x*。因为浮点数精度问题，比例尺和它的反推可能不精确。

<a name="continuous_domain" href="#continuous_domain">#</a> <i>continuous</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

如果指定了 *domain* 则将比例尺的 `domain` 设置为指定的数值数组。数组比例包含两个或者两个以上元素。如果给定的数组中的元素不是数值类型，则会被强制转为数值类型。如果没有指定 *domain* 则会返回当前比例尺的 `domain` 的拷贝。

尽管对于连续比例尺来说，`doamin` 通常包含两个值就可以，但是指定多个值的话会生成一个分段的比例尺。比如创建一个 [diverging color scale(分段的颜色比例尺)](#diverging-scales)，当值为负时在白色和红色之间插值，当值为正时在白色和绿色之间插值：

```js
var color = d3.scaleLinear()
    .domain([-1, 0, 1])
    .range(["red", "white", "green"]);

color(-0.5); // "rgb(255, 128, 128)"
color(+0.5); // "rgb(128, 192, 128)"
```

在内部，分段比例尺根据给定的 `domain` 的值 `range` 插值器进行 [binary search](https://github.com/xswei/d3-array#bisect)。因此 `domain` 必须是有序的。如果 `domain` 和 `range` 的长度不一样分别为 *N* 和 *M* 则只有 *min(N,M)* 个元素会被使用。

<a name="continuous_range" href="#continuous_range">#</a> <i>continuous</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

如果指定了 *range* 则将比例尺的 `range` 设置为指定的数组。数组必须包含两个或两个以上元素。与 [domain](#continuous_domain) 不同的是，`range` 中的元素不一定非要为数值类型。任何支持 [interpolator](#continuous_interpolate) 的类型都可以被设置。但是要注意的是如果要使用 [invert](#continuous_invert) 则 `range` 必须指定为数值类型. 如果 *range* 没有指定则返回比例尺当前 `range` 的拷贝。参考 [*continuous*.interpolate](#continuous_interpolate) 获取更多例子。

<a name="continuous_rangeRound" href="#continuous_rangeRound">#</a> <i>continuous</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

设置比例尺的 [*range*](#continuous_range) 为指定的数组同时设置比例尺的 [interpolator](#continuous_interpolate) 为 [interpolateRound](https://github.com/xswei/d3-interpolate#interpolateRound). 这是一个便捷方法等价于：

```js
continuous
    .range(range)
    .interpolate(d3.interpolateRound);
```

启用插值器的四舍五入有时对避免反锯齿有用，当然也可以使用 [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering) 的“crispEdges” 样式. 注意这种只针对数值类型的 `range` 有效。

<a name="continuous_clamp" href="#continuous_clamp">#</a> <i>continuous</i>.<b>clamp</b>(<i>clamp</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

如果指定了 *clamp* 则启用或者关闭比例尺的钳位功能。如果没有启用钳位功能，则当传入位于 [domain](#continuous_domain) 之外的值时会推算出对应的、处于 [range](#continuous_range) 之外的值。如果启用了钳位功能，则能保证返回的值总是处于 [range](#continuous_range)。钳位功能对于 [*continuous*.invert](#continuous_invert) 也是同样的效果。例如：

```js
var x = d3.scaleLinear()
    .domain([10, 130])
    .range([0, 960]);

x(-10); // -160, outside range
x.invert(-160); // -10, outside domain

x.clamp(true);
x(-10); // 0, clamped to range
x.invert(-160); // 10, clamped to domain
```

如果没有指定 *clamp* 则返回当前比例尺是否启用了钳位功能。

<a name="continuous_interpolate" href="#continuous_interpolate">#</a> <i>continuous</i>.<b>interpolate</b>(<i>interpolate</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

如果指定了 *interpolate* 则设置比例尺的 [range](#continuous_range) 插值器。插值器函数被用来在两个相邻的来自 `range` 值之间进行插值；这些插值器将输入值 *t* 归一化到 [0, 1] 之间。如果 *factory* 没有指定则返回比例尺的当前插值函数。默认为 [interpolate](https://github.com/xswei/d3-interpolate#interpolate). 参考 [d3-interpolate](https://github.com/xswei/d3-interpolate) 获取更多关于插值器的介绍。

If *interpolate* is specified, sets the scale’s [range](#continuous_range) interpolator factory. This interpolator factory is used to create interpolators for each adjacent pair of values from the range; these interpolators then map a normalized domain parameter *t* in [0, 1] to the corresponding value in the range. If *factory* is not specified, returns the scale’s current interpolator factory, which defaults to [interpolate](https://github.com/xswei/d3-interpolate#interpolate). See [d3-interpolate](https://github.com/xswei/d3-interpolate) for more interpolators.

例如，考虑输出范围为三个颜色值：

```js
var color = d3.scaleLinear()
    .domain([-100, 0, +100])
    .range(["red", "white", "green"]);
```

内部会创建两个插值器，等价于：

```js
var i0 = d3.interpolate("red", "white"),
    i1 = d3.interpolate("white", "green");
```

自定义插值器的一个直接的原因是可以修改插值器的颜色空间，比如使用 [HCL](https://github.com/xswei/d3-interpolate#interpolateHcl) 颜色空间:

```js
var color = d3.scaleLinear()
    .domain([10, 100])
    .range(["brown", "steelblue"])
    .interpolate(d3.interpolateHcl);
```

或者自定义 [Cubehelix](https://github.com/xswei/d3-interpolate#interpolateCubehelix) 的 `gamma` 值:

```js
var color = d3.scaleLinear()
    .domain([10, 100])
    .range(["brown", "steelblue"])
    .interpolate(d3.interpolateCubehelix.gamma(3));
```

注意：[default interpolator](https://github.com/xswei/d3-interpolate#interpolate) **可能复用返回值**。例如，如果 `range` 为对象，则 `range` 插值器总是返回同一个修改后的对象。如果比例尺用来设置样式或者属性，则可以使用这种方式，但是如果你想存储比例尺的返回值，则必须指定自己的插值器或者适当的复制。

<a name="continuous_ticks" href="#continuous_ticks">#</a> <i>continuous</i>.<b>ticks</b>([<i>count</i>])

返回近似的用来表示比例尺 [domain](#continuous_domain) 的 *count*。如果没有指定 *count* 则默认为 `10`. 返回的 `tick` 值的个数是均匀的并且对人类友好的(比如都为 `10` 的整数倍)，切在 `domain` 的范围内。`ticks` 经常被用来显示刻度线或者刻度标记。指定的 *count* 仅仅是一个参考，比例尺会根据 `domain` 计算具体的 `ticks`。可以参考 `d3-array` 的 [ticks](https://github.com/xswei/d3-array#ticks).

<a name="continuous_tickFormat" href="#continuous_tickFormat">#</a> <i>continuous</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]]) [<>](https://github.com/xswei/d3-scale/blob/master/src/tickFormat.js "Source")

返回一个调整小时刻度值的 [number format](https://github.com/xswei/d3-format) 函数。*count* 应该与通过 [tick values](#continuous_ticks) 指定的 *count* 相同。

Returns a [number format](https://github.com/xswei/d3-format) function suitable for displaying a tick value, automatically computing the appropriate precision based on the fixed interval between tick values. The specified *count* should have the same value as the count that is used to generate the [tick values](#continuous_ticks).

可选的 *specifier* 允许 [custom format(自定义格式)](https://github.com/xswei/d3-format#locale_format)，格式的精度会自动设置。比如将数字格式化为百分比：

```js
var x = d3.scaleLinear()
    .domain([-1, 1])
    .range([0, 960]);

var ticks = x.ticks(5),
    tickFormat = x.tickFormat(5, "+%");

ticks.map(tickFormat); // ["-100%", "-50%", "+0%", "+50%", "+100%"]
```

如果 *specifier* 使用格式类型 `s` 则比例尺会根据 `domain` 的最大值返回 [SI-prefix format](https://github.com/xswei/d3-format#locale_formatPrefix)。如果 *specifier* 已经指定了精度则这个方法等价于 [*locale*.format](https://github.com/xswei/d3-format#locale_format).

<a name="continuous_nice" href="#continuous_nice">#</a> <i>continuous</i>.<b>nice</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/nice.js "Source")

扩展 [domain](#continuous_domain) 使其为整。这个方法通常会修改 `domain` 将其扩展为最接近的整数值。可选的 *count* 参数可以用来控制扩展的步长。对 `domain` 的取整在根据数据计算 `domain` 时候很有用。比如计算后的 `domain` 为 [0.201479…, 0.996679…] 时，在经过取整之后会被扩展为 [0.2, 1.0]。如果 `domain` 包含两个以上的元素，则取整操作仅仅影响第一个和最后一个值。参考 `d3-array` 的 [tickStep](https://github.com/xswei/d3-array#tickStep) 操作。

取整操作仅仅影响当前 `domain`, 不会对之后通过 [*continuous*.domain](#continuous_domain) 设置的 `domain` 产生影响，如果需要在重置 `domain` 之后重新取整，则必须在重置之后再次取整。

<a name="continuous_copy" href="#continuous_copy">#</a> <i>continuous</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

返回一个当前比例尺的拷贝。返回的拷贝和当前比例尺之间不会相互影响。

#### Linear Scales

<a name="scaleLinear" href="#scaleLinear">#</a> d3.<b>scaleLinear</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/linear.js "Source")

使用单位 [domain](#continuous_domain) [0, 1], 单位 [range](#continuous_range) [0, 1], 以及 [default](https://github.com/xswei/d3-interpolate#interpolate) [interpolator](#continuous_interpolate) 和关闭的 [clamping](#continuous_clamp) 构造一个新的 [continuous scale](#continuous-scales)。线性插值器是一个很好的适用于连续定量数据的比例尺，因为它很好的保留了比例差异。每一个 `range` 中的值 *y* 都可以被表示为一个函数：*y* = *mx* + *b*，其中 *x* 为对应的 `domain` 中的值。

#### Power Scales

幂比例尺与 [linear scales](#linear-scales) 类似，只不过在计算输出 `range` 值之前对 `domain` 值应用了指数变换。每一个输出值 *y* 可以表示为 *x* 的一个函数：*y* = *mx^k* + *b*，其中 *k* 为 [exponent](#pow_exponent) 值。幂比例尺也支持值为负的输入值，这种情况下输入值和输出值都会被乘以 `-1`.

<a name="scalePow" href="#scalePow">#</a> d3.<b>scalePow</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

使用单位 [domain](#continuous_domain) [0, 1], 单位 [range](#continuous_range) [0, 1], 指数 [exponent](#pow_exponent) 为 1, 以及 [default(默认的)](https://github.com/xswei/d3-interpolate#interpolate) [interpolator](#continuous_interpolate) 并关闭 [clamping](#continuous_clamp) 构造一个新的 [continuous scale](#continuous-scales)。（在设置指数之前，与线性比例尺功效一样）。

<a name="pow" href="#_pow">#</a> <i>pow</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*](#_continuous).

<a name="pow_invert" href="#pow_invert">#</a> <i>pow</i>.<b>invert</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.invert](#continuous_invert).

<a name="pow_exponent" href="#pow_exponent">#</a> <i>pow</i>.<b>exponent</b>([<i>exponent</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

如果指定了 *exponent* 则将当前幂比例尺的指数设置为指定的值。如果没有指定 *exponent* 则返回当前的指数，默认为 `1`。（指数为 `1` 时候，与线性比例尺功效一样）。

<a name="pow_domain" href="#pow_domain">#</a> <i>pow</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.domain](#continuous_domain).

<a name="pow_range" href="#pow_range">#</a> <i>pow</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.range](#continuous_range).

<a name="pow_rangeRound" href="#pow_rangeRound">#</a> <i>pow</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.rangeRound](#continuous_rangeRound).

<a name="pow_clamp" href="#pow_clamp">#</a> <i>pow</i>.<b>clamp</b>(<i>clamp</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.clamp](#continuous_clamp).

<a name="pow_interpolate" href="#pow_interpolate">#</a> <i>pow</i>.<b>interpolate</b>(<i>interpolate</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.interpolate](#continuous_interpolate).

<a name="pow_ticks" href="#pow_ticks">#</a> <i>pow</i>.<b>ticks</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.ticks](#continuous_ticks).

<a name="pow_tickFormat" href="#pow_tickFormat">#</a> <i>pow</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.tickFormat](#continuous_tickFormat).

<a name="pow_nice" href="#pow_nice">#</a> <i>pow</i>.<b>nice</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.nice](#continuous_nice).

<a name="pow_copy" href="#pow_copy">#</a> <i>pow</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

参考 [*continuous*.copy](#continuous_copy).

<a name="scaleSqrt" href="#scaleSqrt">#</a> d3.<b>scaleSqrt</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/pow.js "Source")

使用单位 [domain](#continuous_domain) [0, 1], 单位 [range](#continuous_range) [0, 1], [exponent](#pow_exponent) 为 `0.5`, [default](https://github.com/xswei/d3-interpolate#interpolate) [interpolator](#continuous_interpolate) 并关闭 [clamping](#continuous_clamp) 构造一个新的连续的 [power scale](#power-scales)。这是 `d3.scalePow().exponent(0.5)` 的一个便捷方式。

#### Log Scales

对数比例尺与 [linear scales](#linear-scales) 类似。只不过在计算输出值之前对输入值进行了对数转换。对应的 *y* 值可以表示为 *x* 的函数：*y* = *m* log(<i>x</i>) + *b*.

因为  log(0) = -∞，所以对数比例尺的输入值**必须严格为负或者严格为正**；输入范围必须不能包含或跨过 `0`. 如果将负值传递给输入范围为正的对数比例尺，则返回未定义，反之亦然。

<a name="scaleLog" href="#scaleLog">#</a> d3.<b>scaleLog</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

使用单位 [domain](#log_domain) [1, 10], 单位 [range](#log_range) [0, 1],  [base(基)](#log_base) 为 10, [default](https://github.com/xswei/d3-interpolate#interpolate) [interpolator](#log_interpolate) 并关闭 [clamping] 构造一个新的 [continuous scale](#continuous-scales)。

<a name="log" href="#_log">#</a> <i>log</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*](#_continuous).

<a name="log_invert" href="#log_invert">#</a> <i>log</i>.<b>invert</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.invert](#continuous_invert).

<a name="log_base" href="#log_base">#</a> <i>log</i>.<b>base</b>([<i>base</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

If *base* is specified, sets the base for this logarithmic scale to the specified value. If *base* is not specified, returns the current base, which defaults to 10.

<a name="log_domain" href="#log_domain">#</a> <i>log</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.domain](#continuous_domain).

<a name="log_range" href="#log_range">#</a> <i>log</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/continuous.js "Source")

参考 [*continuous*.range](#continuous_range).

<a name="log_rangeRound" href="#log_rangeRound">#</a> <i>log</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.rangeRound](#continuous_rangeRound).

<a name="log_clamp" href="#log_clamp">#</a> <i>log</i>.<b>clamp</b>(<i>clamp</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.clamp](#continuous_clamp).

<a name="log_interpolate" href="#log_interpolate">#</a> <i>log</i>.<b>interpolate</b>(<i>interpolate</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.interpolate](#continuous_interpolate).

<a name="log_ticks" href="#log_ticks">#</a> <i>log</i>.<b>ticks</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

与 [*continuous*.ticks](#continuous_ticks) 类似，但是是对数比例尺特定的。如果 [base](#log_base) 为整数，则返回的 `ticks` 在基的每个整数幂内间隔均匀。否则，基的每个整数次幂一个 `tick`。如果没有指定 *count* 则默认为 `10`.

<a name="log_tickFormat" href="#log_tickFormat">#</a> <i>log</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]]) [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

与 [*continuous*.tickFormat](#continuous_tickFormat) 类似，但是为对数比例尺定特定的。指定的 *count* 通常与生成 [tick values](#continuous_ticks) 的个数一致。如果个数太多的话，则 `formatter` 可能会对某些刻度返回空字符串，但是注意刻度仍然会显示。若要禁用筛选，请指定 *count* 为无穷大。可能还需要一个格式化 *specifier* 或者格式化函数。例如显示格式为货币并且刻度个数为 `20` 则可以定义为：`log.tickFormat(20, "$,f")`。如果格式化说明符没有指定精度则会自动计算。

<a name="log_nice" href="#log_nice">#</a> <i>log</i>.<b>nice</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

与 [*continuous*.nice](#continuous_nice) 类似，但是其实现是将 `domain` 扩展为 [base](#log_base) 的整数次幂。例如对于输入域为 [0.201479…, 0.996679…], 基为 `10` 的比例尺，则 `domain` 会被扩展为 [0.1, 1]。如果 `domain` 有两个以上元素，则只对第一个和最后一个元素有效。

<a name="log_copy" href="#log_copy">#</a> <i>log</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/log.js "Source")

参考 [*continuous*.copy](#continuous_copy).

#### Identity Scales

恒等比例尺是 [linear scales](#linear-scales) 的一种特殊情况。其输入域和值域是完全一致的；这种比例尺的映射以及反映射是完全恒等的。在处理像素坐标时，这些比例尺有时是有用的，例如与轴或画笔一起使用。恒等比例尺不支持 [rangeRound](#continuous_rangeRound), [clamp](#continuous_clamp) 或 [interpolate](#continuous_interpolate).

<a name="scaleIdentity" href="#scaleIdentity">#</a> d3.<b>scaleIdentity</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/identity.js "Source")

使用单元 [domain](#continuous_domain) [0, 1]和单元 [range](#continuous_range) [0, 1] 构造一个恒等比例尺。

#### Time Scales

时间比例尺是 [linear scales](#linear-scales) 的一种变体。它的输入被强制转为 [dates](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date) 而不是数值类型，并且 [invert](#continuous_invert) 返回的是 `date` 类型。时间比例尺基于 [calendar intervals](https://github.com/xswei/d3-time) 实现 [ticks](#time_ticks)。

例如创建一个时间-位置映射比例尺：

```js
var x = d3.scaleTime()
    .domain([new Date(2000, 0, 1), new Date(2000, 0, 2)])
    .range([0, 960]);

x(new Date(2000, 0, 1,  5)); // 200
x(new Date(2000, 0, 1, 16)); // 640
x.invert(200); // Sat Jan 01 2000 05:00:00 GMT-0800 (PST)
x.invert(640); // Sat Jan 01 2000 16:00:00 GMT-0800 (PST)
```

对于合法的输出值 *y*，<i>time</i>(<i>time</i>.invert(<i>y</i>)) 等于 *y*; 对于合法的输入值 *x*，<i>time</i>.invert(<i>time</i>(<i>x</i>)) 等于 *x*. 反转方法在交互时很有用，可以确定鼠标位置与时间之间的对应关系。

<a name="scaleTime" href="#scaleTime">#</a> d3.<b>scaleTime</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

使用默认的 [domain](#time_domain) : [2000-01-01, 2000-01-02], 单位 [range](#time_range) [0, 1] 以及 [default](https://github.com/xswei/d3-interpolate#interpolate) [interpolator](#time_interpolate) 并关闭 [clamping](#time_clamp) 构造一个新的时间比例尺。

<a name="time" href="#_time">#</a> <i>time</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*](#_continuous).

<a name="time_invert" href="#time_invert">#</a> <i>time</i>.<b>invert</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.invert](#continuous_invert).

<a name="time_domain" href="#time_domain">#</a> <i>time</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.domain](#continuous_domain).

<a name="time_range" href="#time_range">#</a> <i>time</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.range](#continuous_range).

<a name="time_rangeRound" href="#time_rangeRound">#</a> <i>time</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.rangeRound](#continuous_rangeRound).

<a name="time_clamp" href="#time_clamp">#</a> <i>time</i>.<b>clamp</b>(<i>clamp</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.clamp](#continuous_clamp).

<a name="time_interpolate" href="#time_interpolate">#</a> <i>time</i>.<b>interpolate</b>(<i>interpolate</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")

参考 [*continuous*.interpolate](#continuous_interpolate).

<a name="time_ticks" href="#time_ticks">#</a> <i>time</i>.<b>ticks</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")
<br><a name="time_ticks" href="#time_ticks">#</a> <i>time</i>.<b>ticks</b>([<i>interval</i>])

从比例尺的 `domain` 中返回具有代表性的日期刻度。返回的刻度值是等间距的(大多数情况下)，并且是合理的(比如每天的午夜)，并且保证在输入域的范围内。刻度通常被用来显示参考刻度线或者刻度标记。

可选的 *count* 可以用来指定生成多少刻度。如果 *count* 没有指定则默认为 `10`。指定的 *count* 仅仅是一个参考值。最后返回的刻度个数可能或多或少。例如，创建一个默认的刻度值则可以：

```js
var x = d3.scaleTime();

x.ticks(10);
// [Sat Jan 01 2000 00:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 03:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 06:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 09:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 12:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 15:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 18:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 21:00:00 GMT-0800 (PST),
//  Sun Jan 02 2000 00:00:00 GMT-0800 (PST)]
```

如下的时间间隔在自动计算刻度时会被考虑：

* 1-, 5-, 15- and 30-second.
* 1-, 5-, 15- and 30-minute.
* 1-, 3-, 6- and 12-hour.
* 1- and 2-day.
* 1-week.
* 1- and 3-month.
* 1-year.

作为 *count* 的替代物，可以指定一个明确的 [time *interval*](https://github.com/xswei/d3-time#intervals)。表示根据指定的时间 *interval(间隔)* 使用 [*interval*.every](https://github.com/xswei/d3-time#interval_every) 生成刻度。例如，每 `15`-[minute](https://github.com/xswei/d3-time#minute) 生成一个刻度：

```js
var x = d3.scaleTime()
    .domain([new Date(2000, 0, 1, 0), new Date(2000, 0, 1, 2)]);

x.ticks(d3.timeMinute.every(15));
// [Sat Jan 01 2000 00:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 00:15:00 GMT-0800 (PST),
//  Sat Jan 01 2000 00:30:00 GMT-0800 (PST),
//  Sat Jan 01 2000 00:45:00 GMT-0800 (PST),
//  Sat Jan 01 2000 01:00:00 GMT-0800 (PST),
//  Sat Jan 01 2000 01:15:00 GMT-0800 (PST),
//  Sat Jan 01 2000 01:30:00 GMT-0800 (PST),
//  Sat Jan 01 2000 01:45:00 GMT-0800 (PST),
//  Sat Jan 01 2000 02:00:00 GMT-0800 (PST)]
```

或者给 [*interval*.filter](https://github.com/xswei/d3-time#interval_filter) 传递一个测试函数：

```js
x.ticks(d3.timeMinute.filter(function(d) {
  return d.getMinutes() % 15 === 0;
}));
```

注意：在某些情况下，比如使用日期为间隔时，可能会导致刻度间隔不均匀，因为时间间隔对应的输出区间长度可能不均匀。

<a name="time_tickFormat" href="#time_tickFormat">#</a> <i>time</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")
<br><a href="#time_tickFormat">#</a> <i>time</i>.<b>tickFormat</b>([<i>interval</i>[, <i>specifier</i>]])

返回一个适用于显示日期类 [tick](#time_ticks) 的格式化函数。指定的 *count* 或 *interval* 目前是被忽略，但是为了保持与 [*continuous*.tickFormat](#continuous_tickFormat) 的一致性，可以这么使用。如果指定了格式化 *specifier* 则等价于 [format](https://github.com/xswei/d3-time-format#format)。如果 *specifier* 没有指定则返回默认的日期格式。默认的日期格式化会从以下人类友好的格式化说明符中选择合适的：

* `%Y` - 年份，比如 `2011`.
* `%B` - 月份，比如 `February`.
* `%b %d` - 周, 比如 `Feb 06`.
* `%a %d` - 天, 比如 `Mon 07`.
* `%I %p` - 时, 比如 `01 AM`.
* `%I:%M` - 分钟, 比如 `01:23`.
* `:%S` - 秒, 比如 `:45`.
* `.%L` - 毫秒, 比如 `.012`.

虽然有些不同寻常，但这种默认的格式具有同时提供本地和全局上下文的优点: 将时间序列格式化为 `[11 PM, Mon 07, 01 AM]`，与 `[11 PM, 12 AM, 01 AM]` 相比可以同时显示有关小时、日期和日期的信息，如果想自定义自己的时间格式，请参考 [d3-time-format](https://github.com/xswei/d3-time-format)。

<a name="time_nice" href="#time_nice">#</a> <i>time</i>.<b>nice</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/time.js "Source")
<br><a name="time_nice" href="#time_nice">#</a> <i>time</i>.<b>nice</b>([<i>interval</i>[, <i>step</i>]])

扩展比例尺的 `domain` 使其开始和结束值更规整。这个方法会修改比例尺的 `domain`，并且只将其扩展到最近的整值。参考 [*continuous*.nice](#continuous_nice)。

可选的刻度 *count* 允许对扩展步骤大小进行更精确的控制，确保返回的刻度数正好覆盖整个 `domain`。或者可以显示的指定一个 [time *interval*](https://github.com/xswei/d3-time#intervals) 来设置刻度。如果指定了 *interval* 则还可以指定一个可选步长来跳过一些刻度。例如 `time.nice(d3.timeSecond, 10)` 在扩展 `domain` 时会根据步长 `10秒` 来扩展（`0s`, `10s`, `20a` 等）。参考 [*time*.ticks](#time_ticks) 和 [*interval*.every](https://github.com/xswei/d3-time#interval_every) 获取详细介绍。

当输入范围根据数据计算时，取整操作很有用。例如对于时间范围 `[2009-07-13T00:02, 2009-07-13T23:48]` 取整后为 `[2009-07-13, 2009-07-14]`。如果输入域有两个以上元素，则取整只对第一个和最后一个元素生效。

<a name="scaleUtc" href="#scaleUtc">#</a> d3.<b>scaleUtc</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/utcTime.js "Source")

等价于 [time](#time)，但是返回的时间格式是 [Coordinated Universal Time(协调世界时)](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) 而不是当地时间。

### Sequential Scales

序列比例尺，与 [diverging scales](#diverging-scales) 和 [continuous scales](#continuous-scales) 类似，将连续的数字输入域映射到连续的输出域。但是与连续比例尺不一样的是，它的输出域是根据指定的插值器内置且不可配置，其次它的插值方式也不可配置。不暴露 [invert](#continuous_invert), [range](#continuous_range), [rangeRound](#continuous_rangeRound) 和 [interpolate](#continuous_interpolate) 方法。

<a name="scaleSequential" href="#scaleSequential">#</a> d3.<b>scaleSequential</b>(<i>interpolator</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

使用跟定的 [*interpolator*](#sequential_interpolator) 函数构造一个新的序列比例尺。在应用比例尺时，可以传入的值 `[0, 1]`。其中 `0` 表示最小值，`1` 表示最大值。例如实现一个 [HSL](https://github.com/xswei/d3-color#hsl) 具有周期性的颜色插值器：

```js
var rainbow = d3.scaleSequential(function(t) {
  return d3.hsl(t * 360, 1, 0.5) + "";
});
```

使用 [d3.interpolateRainbow](https://github.com/xswei/d3-scale-chromatic/blob/master/README.md#interpolateRainbow) 实现一种更优雅并且更高效的周期性颜色插值器：

```js
var rainbow = d3.scaleSequential(d3.interpolateRainbow);
```

<a name="_sequential" href="#_sequential">#</a> <i>sequential</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

参考 [*continuous*](#_continuous).

<a name="sequential_domain" href="#sequential_domain">#</a> <i>sequential</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

参考 [*continuous*.domain](#continuous_domain). Note that a sequential scale’s domain must be numeric and must contain exactly two values.

<a name="sequential_clamp" href="#sequential_clamp">#</a> <i>sequential</i>.<b>clamp</b>([<i>clamp</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

参考 [*continuous*.clamp](#continuous_clamp).

<a name="sequential_interpolator" href="#sequential_interpolator">#</a> <i>sequential</i>.<b>interpolator</b>([<i>interpolator</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

如果指定了 *interpolate* 则将比例尺的插值器设置为指定的函数。如果没有指定 *interpolate* 则返回当前比例尺的插值器。

<a name="sequential_copy" href="#sequential_copy">#</a> <i>sequential</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/sequential.js "Source")

参考 [*continuous*.copy](#continuous_copy).

### Diverging Scales

Diverging scales, like [sequential scales](#sequential-scales), are similar to [continuous scales](#continuous-scales) in that they map a continuous, numeric input domain to a continuous output range. However, unlike continuous scales, the output range of a diverging scale is fixed by its interpolator and not configurable. These scales do not expose [invert](#continuous_invert), [range](#continuous_range), [rangeRound](#continuous_rangeRound) and [interpolate](#continuous_interpolate) methods.

<a name="scaleDiverging" href="#scaleDiverging">#</a> d3.<b>scaleDiverging</b>(<i>interpolator</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

Constructs a new diverging scale with the given [*interpolator*](#diverging_interpolator) function. When the scale is [applied](#_diverging), the interpolator will be invoked with a value typically in the range [0, 1], where 0 represents the extreme negative value, 0.5 represents the neutral value, and 1 represents the extreme positive value. For example, using [d3.interpolateSpectral](https://github.com/xswei/d3-scale-chromatic/blob/master/README.md#interpolateSpectral):

```js
var spectral = d3.scaleDiverging(d3.interpolateSpectral);
```

<a name="_diverging" href="#_diverging">#</a> <i>diverging</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

See [*continuous*](#_continuous).

<a name="diverging_domain" href="#diverging_domain">#</a> <i>diverging</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

See [*continuous*.domain](#continuous_domain). Note that a diverging scale’s domain must be numeric and must contain exactly three values. The default domain is [0, 0.5, 1].

<a name="diverging_clamp" href="#diverging_clamp">#</a> <i>diverging</i>.<b>clamp</b>([<i>clamp</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

See [*continuous*.clamp](#continuous_clamp).

<a name="diverging_interpolator" href="#diverging_interpolator">#</a> <i>diverging</i>.<b>interpolator</b>([<i>interpolator</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

If *interpolator* is specified, sets the scale’s interpolator to the specified function. If *interpolator* is not specified, returns the scale’s current interpolator.

<a name="diverging_copy" href="#diverging_copy">#</a> <i>diverging</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/diverging.js "Source")

See [*continuous*.copy](#continuous_copy).

### Quantize Scales

Quantize scales are similar to [linear scales](#linear-scales), except they use a discrete rather than continuous range. The continuous input domain is divided into uniform segments based on the number of values in (*i.e.*, the cardinality of) the output range. Each range value *y* can be expressed as a quantized linear function of the domain value *x*: *y* = *m round(x)* + *b*. See [bl.ocks.org/4060606](http://bl.ocks.org/mbostock/4060606) for an example.

<a name="scaleQuantize" href="#scaleQuantize">#</a> d3.<b>scaleQuantize</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Constructs a new quantize scale with the unit [domain](#quantize_domain) [0, 1] and the unit [range](#quantize_range) [0, 1]. Thus, the default quantize scale is equivalent to the [Math.round](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Math/round) function.

<a name="_quantize" href="#_quantize">#</a> <i>quantize</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Given a *value* in the input [domain](#quantize_domain), returns the corresponding value in the output [range](#quantize_range). For example, to apply a color encoding:

```js
var color = d3.scaleQuantize()
    .domain([0, 1])
    .range(["brown", "steelblue"]);

color(0.49); // "brown"
color(0.51); // "steelblue"
```

Or dividing the domain into three equally-sized parts with different range values to compute an appropriate stroke width:

```js
var width = d3.scaleQuantize()
    .domain([10, 100])
    .range([1, 2, 4]);

width(20); // 1
width(50); // 2
width(80); // 4
```

<a name="quantize_invertExtent" href="#quantize_invertExtent">#</a> <i>quantize</i>.<b>invertExtent</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Returns the extent of values in the [domain](#quantize_domain) [<i>x0</i>, <i>x1</i>] for the corresponding *value* in the [range](#quantize_range): the inverse of [*quantize*](#_quantize). This method is useful for interaction, say to determine the value in the domain that corresponds to the pixel location under the mouse.

```js
var width = d3.scaleQuantize()
    .domain([10, 100])
    .range([1, 2, 4]);

width.invertExtent(2); // [40, 70]
```

<a name="quantize_domain" href="#quantize_domain">#</a> <i>quantize</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

If *domain* is specified, sets the scale’s domain to the specified two-element array of numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If *domain* is not specified, returns the scale’s current domain.

<a name="quantize_range" href="#quantize_range">#</a> <i>quantize</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

If *range* is specified, sets the scale’s range to the specified array of values. The array may contain any number of discrete values. The elements in the given array need not be numbers; any value or type will work. If *range* is not specified, returns the scale’s current range.

<a name="quantize_ticks" href="#quantize_ticks">#</a> <i>quantize</i>.<b>ticks</b>([<i>count</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Equivalent to [*continuous*.ticks](#continuous_ticks).

<a name="quantize_tickFormat" href="#quantize_tickFormat">#</a> <i>quantize</i>.<b>tickFormat</b>([<i>count</i>[, <i>specifier</i>]]) [<>](https://github.com/xswei/d3-scale/blob/master/src/linear.js "Source")

Equivalent to [*continuous*.tickFormat](#continuous_tickFormat).

<a name="quantize_nice" href="#quantize_nice">#</a> <i>quantize</i>.<b>nice</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Equivalent to [*continuous*.nice](#continuous_nice).

<a name="quantize_copy" href="#quantize_copy">#</a> <i>quantize</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantize.js "Source")

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Quantile Scales

Quantile scales map a sampled input domain to a discrete range. The domain is considered continuous and thus the scale will accept any reasonable input value; however, the domain is specified as a discrete set of sample values. The number of values in (the cardinality of) the output range determines the number of quantiles that will be computed from the domain. To compute the quantiles, the domain is sorted, and treated as a [population of discrete values](https://en.wikipedia.org/wiki/Quantile#Quantiles_of_a_population); see d3-array’s [quantile](https://github.com/xswei/d3-array#quantile). See [bl.ocks.org/8ca036b3505121279daf](http://bl.ocks.org/mbostock/8ca036b3505121279daf) for an example.

<a name="scaleQuantile" href="#scaleQuantile">#</a> d3.<b>scaleQuantile</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

Constructs a new quantile scale with an empty [domain](#quantile_domain) and an empty [range](#quantile_range). The quantile scale is invalid until both a domain and range are specified.

<a name="_quantile" href="#_quantile">#</a> <i>quantile</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

Given a *value* in the input [domain](#quantile_domain), returns the corresponding value in the output [range](#quantile_range).

<a name="quantile_invertExtent" href="#quantile_invertExtent">#</a> <i>quantile</i>.<b>invertExtent</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

Returns the extent of values in the [domain](#quantile_domain) [<i>x0</i>, <i>x1</i>] for the corresponding *value* in the [range](#quantile_range): the inverse of [*quantile*](#_quantile). This method is useful for interaction, say to determine the value in the domain that corresponds to the pixel location under the mouse.

<a name="quantile_domain" href="#quantile_domain">#</a> <i>quantile</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

If *domain* is specified, sets the domain of the quantile scale to the specified set of discrete numeric values. The array must not be empty, and must contain at least one numeric value; NaN, null and undefined values are ignored and not considered part of the sample population. If the elements in the given array are not numbers, they will be coerced to numbers. A copy of the input array is sorted and stored internally. If *domain* is not specified, returns the scale’s current domain.

<a name="quantile_range" href="#quantile_range">#</a> <i>quantile</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

If *range* is specified, sets the discrete values in the range. The array must not be empty, and may contain any type of value. The number of values in (the cardinality, or length, of) the *range* array determines the number of quantiles that are computed. For example, to compute quartiles, *range* must be an array of four elements such as [0, 1, 2, 3]. If *range* is not specified, returns the current range.

<a name="quantile_quantiles" href="#quantile_quantiles">#</a> <i>quantile</i>.<b>quantiles</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

Returns the quantile thresholds. If the [range](#quantile_range) contains *n* discrete values, the returned array will contain *n* - 1 thresholds. Values less than the first threshold are considered in the first quantile; values greater than or equal to the first threshold but less than the second threshold are in the second quantile, and so on. Internally, the thresholds array is used with [bisect](https://github.com/xswei/d3-array#bisect) to find the output quantile associated with the given input value.

<a name="quantile_copy" href="#quantile_copy">#</a> <i>quantile</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/quantile.js "Source")

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Threshold Scales

Threshold scales are similar to [quantize scales](#quantize-scales), except they allow you to map arbitrary subsets of the domain to discrete values in the range. The input domain is still continuous, and divided into slices based on a set of threshold values. See [bl.ocks.org/3306362](http://bl.ocks.org/mbostock/3306362) for an example.

<a name="scaleThreshold" href="#scaleThreshold">#</a> d3.<b>scaleThreshold</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

Constructs a new threshold scale with the default [domain](#threshold_domain) [0.5] and the default [range](#threshold_range) [0, 1]. Thus, the default threshold scale is equivalent to the [Math.round](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Math/round) function for numbers; for example threshold(0.49) returns 0, and threshold(0.51) returns 1.

<a name="_threshold" href="#_threshold">#</a> <i>threshold</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

Given a *value* in the input [domain](#threshold_domain), returns the corresponding value in the output [range](#threshold_range). For example:

```js
var color = d3.scaleThreshold()
    .domain([0, 1])
    .range(["red", "white", "green"]);

color(-1);   // "red"
color(0);    // "white"
color(0.5);  // "white"
color(1);    // "green"
color(1000); // "green"
```

<a name="threshold_invertExtent" href="#threshold_invertExtent">#</a> <i>threshold</i>.<b>invertExtent</b>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

Returns the extent of values in the [domain](#threshold_domain) [<i>x0</i>, <i>x1</i>] for the corresponding *value* in the [range](#threshold_range), representing the inverse mapping from range to domain. This method is useful for interaction, say to determine the value in the domain that corresponds to the pixel location under the mouse. For example:

```js
var color = d3.scaleThreshold()
    .domain([0, 1])
    .range(["red", "white", "green"]);

color.invertExtent("red"); // [undefined, 0]
color.invertExtent("white"); // [0, 1]
color.invertExtent("green"); // [1, undefined]
```

<a name="threshold_domain" href="#threshold_domain">#</a> <i>threshold</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

If *domain* is specified, sets the scale’s domain to the specified array of values. The values must be in sorted ascending order, or the behavior of the scale is undefined. The values are typically numbers, but any naturally ordered values (such as strings) will work; a threshold scale can be used to encode any type that is ordered. If the number of values in the scale’s range is N+1, the number of values in the scale’s domain must be N. If there are fewer than N elements in the domain, the additional values in the range are ignored. If there are more than N elements in the domain, the scale may return undefined for some inputs. If *domain* is not specified, returns the scale’s current domain.

<a name="threshold_range" href="#threshold_range">#</a> <i>threshold</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

If *range* is specified, sets the scale’s range to the specified array of values. If the number of values in the scale’s domain is N, the number of values in the scale’s range must be N+1. If there are fewer than N+1 elements in the range, the scale may return undefined for some inputs. If there are more than N+1 elements in the range, the additional values are ignored. The elements in the given array need not be numbers; any value or type will work. If *range* is not specified, returns the scale’s current range.

<a name="threshold_copy" href="#threshold_copy">#</a> <i>threshold</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/threshold.js "Source")

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

### Ordinal Scales

Unlike [continuous scales](#continuous-scales), ordinal scales have a discrete domain and range. For example, an ordinal scale might map a set of named categories to a set of colors, or determine the horizontal positions of columns in a column chart.

<a name="scaleOrdinal" href="#scaleOrdinal">#</a> d3.<b>scaleOrdinal</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

Constructs a new ordinal scale with an empty [domain](#ordinal_domain) and the specified [*range*](#ordinal_range). If a *range* is not specified, it defaults to the empty array; an ordinal scale always returns undefined until a non-empty range is defined.

<a name="_ordinal" href="#_ordinal">#</a> <i>ordinal</i>(<i>value</i>) [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

Given a *value* in the input [domain](#ordinal_domain), returns the corresponding value in the output [range](#ordinal_range). If the given *value* is not in the scale’s [domain](#ordinal_domain), returns the [unknown](#ordinal_value); or, if the unknown value is [implicit](#scaleImplicit) (the default), then the *value* is implicitly added to the domain and the next-available value in the range is assigned to *value*, such that this and subsequent invocations of the scale given the same input *value* return the same output value.

<a name="ordinal_domain" href="#ordinal_domain">#</a> <i>ordinal</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

If *domain* is specified, sets the domain to the specified array of values. The first element in *domain* will be mapped to the first element in the range, the second domain value to the second range value, and so on. Domain values are stored internally in a map from stringified value to index; the resulting index is then used to retrieve a value from the range. Thus, an ordinal scale’s values must be coercible to a string, and the stringified version of the domain value uniquely identifies the corresponding range value. If *domain* is not specified, this method returns the current domain.

Setting the domain on an ordinal scale is optional if the [unknown value](#ordinal_unknown) is [implicit](#scaleImplicit) (the default). In this case, the domain will be inferred implicitly from usage by assigning each unique value passed to the scale a new value from the range. Note that an explicit domain is recommended to ensure deterministic behavior, as inferring the domain from usage will be dependent on ordering.

<a name="ordinal_range" href="#ordinal_range">#</a> <i>ordinal</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

If *range* is specified, sets the range of the ordinal scale to the specified array of values. The first element in the domain will be mapped to the first element in *range*, the second domain value to the second range value, and so on. If there are fewer elements in the range than in the domain, the scale will reuse values from the start of the range. If *range* is not specified, this method returns the current range.

<a name="ordinal_unknown" href="#ordinal_unknown">#</a> <i>ordinal</i>.<b>unknown</b>([<i>value</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

If *value* is specified, sets the output value of the scale for unknown input values and returns this scale. If *value* is not specified, returns the current unknown value, which defaults to [implicit](#implicit). The implicit value enables implicit domain construction; see [*ordinal*.domain](#ordinal_domain).

<a name="ordinal_copy" href="#ordinal_copy">#</a> <i>ordinal</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

Returns an exact copy of this ordinal scale. Changes to this scale will not affect the returned scale, and vice versa.

<a name="scaleImplicit" href="#scaleImplicit">#</a> d3.<b>scaleImplicit</b> [<>](https://github.com/xswei/d3-scale/blob/master/src/ordinal.js "Source")

A special value for [*ordinal*.unknown](#ordinal_unknown) that enables implicit domain construction: unknown values are implicitly added to the domain.

#### Band Scales

Band scales are like [ordinal scales](#ordinal-scales) except the output range is continuous and numeric. Discrete output values are automatically computed by the scale by dividing the continuous range into uniform bands. Band scales are typically used for bar charts with an ordinal or categorical dimension. The [unknown value](#ordinal_unknown) of a band scale is effectively undefined: they do not allow implicit domain construction.

<img src="https://raw.githubusercontent.com/d3/d3-scale/master/img/band.png" width="751" height="238" alt="band">

<a name="scaleBand" href="#scaleBand">#</a> d3.<b>scaleBand</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Constructs a new band scale with the empty [domain](#band_domain), the unit [range](#band_range) [0, 1], no [padding](#band_padding), no [rounding](#band_round) and center [alignment](#band_align).

<a name="_band" href="#_band">#</a> <i>band</i>(*value*) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Given a *value* in the input [domain](#band_domain), returns the start of the corresponding band derived from the output [range](#band_range). If the given *value* is not in the scale’s domain, returns undefined.

<a name="band_domain" href="#band_domain">#</a> <i>band</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *domain* is specified, sets the domain to the specified array of values. The first element in *domain* will be mapped to the first band, the second domain value to the second band, and so on. Domain values are stored internally in a map from stringified value to index; the resulting index is then used to determine the band. Thus, a band scale’s values must be coercible to a string, and the stringified version of the domain value uniquely identifies the corresponding band. If *domain* is not specified, this method returns the current domain.

<a name="band_range" href="#band_range">#</a> <i>band</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *range* is specified, sets the scale’s range to the specified two-element array of numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If *range* is not specified, returns the scale’s current range, which defaults to [0, 1].

<a name="band_rangeRound" href="#band_rangeRound">#</a> <i>band</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Sets the scale’s [*range*](#band_range) to the specified two-element array of numbers while also enabling [rounding](#band_round). This is a convenience method equivalent to:

```js
band
    .range(range)
    .round(true);
```

Rounding is sometimes useful for avoiding antialiasing artifacts, though also consider the [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering) “crispEdges” styles.

<a name="band_round" href="#band_round">#</a> <i>band</i>.<b>round</b>([<i>round</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *round* is specified, enables or disables rounding accordingly. If rounding is enabled, the start and stop of each band will be integers. Rounding is sometimes useful for avoiding antialiasing artifacts, though also consider the [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering) “crispEdges” styles. Note that if the width of the domain is not a multiple of the cardinality of the range, there may be leftover unused space, even without padding! Use [*band*.align](#band_align) to specify how the leftover space is distributed.

<a name="band_paddingInner" href="#band_paddingInner">#</a> <i>band</i>.<b>paddingInner</b>([<i>padding</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *padding* is specified, sets the inner padding to the specified value which must be in the range [0, 1]. If *padding* is not specified, returns the current inner padding which defaults to 0. The inner padding determines the ratio of the range that is reserved for blank space between bands.

<a name="band_paddingOuter" href="#band_paddingOuter">#</a> <i>band</i>.<b>paddingOuter</b>([<i>padding</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *padding* is specified, sets the outer padding to the specified value which must be in the range [0, 1]. If *padding* is not specified, returns the current outer padding which defaults to 0. The outer padding determines the ratio of the range that is reserved for blank space before the first band and after the last band.

<a name="band_padding" href="#band_padding">#</a> <i>band</i>.<b>padding</b>([<i>padding</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

A convenience method for setting the [inner](#band_paddingInner) and [outer](#band_paddingOuter) padding to the same *padding* value. If *padding* is not specified, returns the inner padding.

<a name="band_align" href="#band_align">#</a> <i>band</i>.<b>align</b>([<i>align</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *align* is specified, sets the alignment to the specified value which must be in the range [0, 1]. If *align* is not specified, returns the current alignment which defaults to 0.5. The alignment determines how any leftover unused space in the range is distributed. A value of 0.5 indicates that the leftover space should be equally distributed before the first band and after the last band; *i.e.*, the bands should be centered within the range. A value of 0 or 1 may be used to shift the bands to one side, say to position them adjacent to an axis.

<a name="band_bandwidth" href="#band_bandwidth">#</a> <i>band</i>.<b>bandwidth</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns the width of each band.

<a name="band_step" href="#band_step">#</a> <i>band</i>.<b>step</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns the distance between the starts of adjacent bands.

<a name="band_copy" href="#band_copy">#</a> <i>band</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.

#### Point Scales

Point scales are a variant of [band scales](#band-scales) with the bandwidth fixed to zero. Point scales are typically used for scatterplots with an ordinal or categorical dimension. The [unknown value](#ordinal_unknown) of a point scale is always undefined: they do not allow implicit domain construction.

<img src="https://raw.githubusercontent.com/d3/d3-scale/master/img/point.png" width="648" height="155" alt="point">

<a name="scalePoint" href="#scalePoint">#</a> d3.<b>scalePoint</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Constructs a new point scale with the empty [domain](#point_domain), the unit [range](#point_range) [0, 1], no [padding](#point_padding), no [rounding](#point_round) and center [alignment](#point_align).

<a name="_point" href="#_point">#</a> <i>point</i>(*value*) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Given a *value* in the input [domain](#point_domain), returns the corresponding point derived from the output [range](#point_range). If the given *value* is not in the scale’s domain, returns undefined.

<a name="point_domain" href="#point_domain">#</a> <i>point</i>.<b>domain</b>([<i>domain</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *domain* is specified, sets the domain to the specified array of values. The first element in *domain* will be mapped to the first point, the second domain value to the second point, and so on. Domain values are stored internally in a map from stringified value to index; the resulting index is then used to determine the point. Thus, a point scale’s values must be coercible to a string, and the stringified version of the domain value uniquely identifies the corresponding point. If *domain* is not specified, this method returns the current domain.

<a name="point_range" href="#point_range">#</a> <i>point</i>.<b>range</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *range* is specified, sets the scale’s range to the specified two-element array of numbers. If the elements in the given array are not numbers, they will be coerced to numbers. If *range* is not specified, returns the scale’s current range, which defaults to [0, 1].

<a name="point_rangeRound" href="#point_rangeRound">#</a> <i>point</i>.<b>rangeRound</b>([<i>range</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Sets the scale’s [*range*](#point_range) to the specified two-element array of numbers while also enabling [rounding](#point_round). This is a convenience method equivalent to:

```js
point
    .range(range)
    .round(true);
```

Rounding is sometimes useful for avoiding antialiasing artifacts, though also consider the [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering) “crispEdges” styles.

<a name="point_round" href="#point_round">#</a> <i>point</i>.<b>round</b>([<i>round</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *round* is specified, enables or disables rounding accordingly. If rounding is enabled, the position of each point will be integers. Rounding is sometimes useful for avoiding antialiasing artifacts, though also consider the [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering) “crispEdges” styles. Note that if the width of the domain is not a multiple of the cardinality of the range, there may be leftover unused space, even without padding! Use [*point*.align](#point_align) to specify how the leftover space is distributed.

<a name="point_padding" href="#point_padding">#</a> <i>point</i>.<b>padding</b>([<i>padding</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *padding* is specified, sets the outer padding to the specified value which must be in the range [0, 1]. If *padding* is not specified, returns the current outer padding which defaults to 0. The outer padding determines the ratio of the range that is reserved for blank space before the first point and after the last point. Equivalent to [*band*.paddingOuter](#band_paddingOuter).

<a name="point_align" href="#point_align">#</a> <i>point</i>.<b>align</b>([<i>align</i>]) [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

If *align* is specified, sets the alignment to the specified value which must be in the range [0, 1]. If *align* is not specified, returns the current alignment which defaults to 0.5. The alignment determines how any leftover unused space in the range is distributed. A value of 0.5 indicates that the leftover space should be equally distributed before the first point and after the last point; *i.e.*, the points should be centered within the range. A value of 0 or 1 may be used to shift the points to one side, say to position them adjacent to an axis.

<a name="point_bandwidth" href="#point_bandwidth">#</a> <i>point</i>.<b>bandwidth</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns zero.

<a name="point_step" href="#point_step">#</a> <i>point</i>.<b>step</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns the distance between the starts of adjacent points.

<a name="point_copy" href="#point_copy">#</a> <i>point</i>.<b>copy</b>() [<>](https://github.com/xswei/d3-scale/blob/master/src/band.js "Source")

Returns an exact copy of this scale. Changes to this scale will not affect the returned scale, and vice versa.
---
title: 'FrontEnd Notes [2020.01]'
catalog: true
date: 2020-01-02 09:57:13
subtitle:
header-img:
tags: FE
---
#### About

📅 2020 年 1 月的零散学习记录。

新年快乐！希望新的一年能坚持记笔记！

#### 2020/01/15
一、[一步一步解码 PNG 图片](https://vivaxyblog.github.io/2019/12/07/decode-a-png-image-with-javascript-cn.html)
可以当成一份扩展阅读，讲了怎么从一张二进制 PNG 图片转成包含像素数据的 ImageData 。
之前在工作中，主要是利用 `canvas` 得到 `imageData` ：
```
export function getImageData(url: string): Promise<ImageData> {
  const img = document.createElement("img");
  return new Promise((resolve, reject) => {
    img.onload = () => {
      try {
        const canvas = document.createElement("canvas");
        canvas.width = img.naturalWidth;
        canvas.height = img.naturalHeight;
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0);
        resolve(ctx.getImageData(0, 0, canvas.width, canvas.height));
      } catch (err) {
        reject(err);
      }
    };
    img.onerror = err => {
      reject(err);
    };
    img.crossOrigin = "Anonymous";
    img.src = url;
  });
}
```
`imageData` 是不能直接存进后台数据库的，我们需要进行 PNG 编码转换为二进制数据，用到的是 [UPNG.js](https://github.com/photopea/UPNG.js/) 这个库：
```
import UPNG from "upng-js";
const buffer: ArrayBuffer = UPNG.encode(
  [imageData.buffer as ArrayBuffer],
  image.width,
  image.height,
  0
);
```

#### 2020/01/13
一、[3 things you didn’t know about the forEach loop in JS](https://medium.com/front-end-weekly/3-things-you-didnt-know-about-the-foreach-loop-in-js-ff02cec465b1)
总结一下，就是在 `forEach` 中，使用 `return` 、 `break` 、 `continute` 都是无效的。 `return` 不会退出函数，`break` 和 `continute` 不允许在 `forEach` 中使用。如果需要能退出循环，使用简单的 `for` loop 即可。MDN 上已经写明：
> There is no way to stop or break a forEach() loop other than by throwing an exception. If you need such behavior, the forEach() method is the wrong tool.

二、[under_scores, camelCase and PascalCase - The three naming conventions every programmer should be aware of](https://dev.to/prahladyeri/underscores-camelcasing-and-pascalcasing-the-three-naming-conventions-every-programmer-should-be-aware-of-3aed)
简单介绍了编程中三种命名规则，可以学一下正确的名词表达：
![image.png](https://i.loli.net/2020/01/13/kAnN3GvZBiWDPRu.png)
`camelCase` 大家都不陌生，就是最常见的首字母小写驼峰样式；`under_scores` 就是以下划线隔开多个小写字母，JS 中这些命名方式比较少用；`PascalCase` 就是首字母大写驼峰样式。此外，函数、变量、类、命名空间这些可以统称为 `tokens` 。

#### 2020/01/12
一、[Why ['1', '7', '11'].map(parseInt) returns [1, NaN, 3] in Javascript](https://medium.com/dailyjs/parseint-mystery-7c4368ef7b21)
一篇非常有趣的文章，开篇一张图：
![image.png](https://i.loli.net/2020/01/12/grShLvH28RDXtYM.png)
出现这种结果的原因，在于 `map` 函数默认会传入三个参数：currentValue、currentIndex 以及 full array 。而对于 `parseInt` 而言，第二个参数表示基数，用此来解析数组中的每个字符串，取值是 2 ~ 36 的整数。'1' 以 0 为基数进行解析，0 的布尔值为 `false`，等于没有传入第二个参数，因此会以默认基数 10 进行解析，输出 1 ；'7' 以 1 为基数 进行解析，会输出 `NaN` ；'11' 以 2 为基数解析，会输出 3。

二、[The Power of the Observer Pattern in JavaScript](https://medium.com/better-programming/the-observer-pattern-in-javascript-4f4e0b908d5e)
介绍了 Observer Patten，并用 JS 写了一份简单实现。本质就是维护一个观察者列表，每次有变化时遍历通知。

#### 2020/01/09
一、[How to handle errors in Promise.all](https://stackoverflow.com/questions/30362733/handling-errors-in-promise-all/30378082)
简单来说就是：
```
await Promise.all(vals.map(val => 
  promise(val)
  .catch(err => {
    // handle here
    return err
  })
))
```

二、[New ES2018 Features](https://css-tricks.com/new-es2018-features-every-javascript-developer-should-know/)

#### 2020/01/05
今天需要对某个数值进行类型校验，避免出现 `NaN`，本来是用 `!typeof value === 'number'`，后来发现：
```
typeof NaN === 'number'
```
关于为什么是这种输出，可以看 medium 上这篇文章：[NaN and typeof](https://javascriptrefined.io/nan-and-typeof-36cd6e2a4e43)。
如果需要判断某个数是否为 `NaN`，可用 `isNaN(value)` 判断。

#### 2020/01/04
今天在处理正则表达式时遇到这样的疑惑：
```
const str = '\abc';
const reg = /\\abc/;
reg.test(str); // false
```
我们都知道反斜杠 `\` 是转义的作用，如果要输出斜杠，那么必须使用 `\\` ；在上面的代码中，`reg` 匹配是是 `\abc` ，为什么输出结果会是 `false` 呢？
经过查阅，发现在字符串中，'\' 也有转义的作用。如果反斜杠出现在字符的前面，那么他们就是一个整体，比如说 '\n' 表示换行，字符串 '\abc' 也涉及转义操作，由于 a 不是有效的转义符，所以就直接转成 'abc' 。下面的代码可以验证这个说法：
```
const str = "\abc";
console.info(str.length); // 3
"\abc" === "abc" //true
```
因此，上面的代码应该改成：
```
const str = '\\abc';
const reg = /\\abc/;
reg.test(str); // true
```

#### 2020/01/02
放大预览图片的某个部分：思路是计算 bbox 的 scale 数值，使之能填充满整个 container ；再计算原始图片的偏移量，使之只显示 bbox 部分。    
```
function Preview(props: {
  imgSrc: string;
  imgSize: { width: number; height: number };
  box: {
    x: number;
    y: number;
    width: number;
    height: number;
  };
}) {
  const [containerSize] = useState({ width: 350, height: 400 });

  const scale = Math.min(
    containerSize.width / props.box.width,
    containerSize.height / props.box.height
  );
  const boxCenter = {
    x: props.box.x + props.box.width / 2,
    y: props.box.y + props.box.height / 2
  };
  const containerCenter = {
    x: containerSize.width / 2,
    y: containerSize.height / 2
  };
  const tr = {
    x: containerCenter.x - boxCenter.x * scale,
    y: containerCenter.y - boxCenter.y * scale
  };

  return (
    <div style={{ position: "relative", ...containerSize, overflow: "hidden" }}>
      <img
        src={props.imgSrc}
        style={{
          ...props.imgSize,
          objectFit: "contain",
          position: "absolute",
          left: 0,
          top: 0,
          transform: `translate(${tr.x}px, ${tr.y}px) scale(${scale})`,
          transformOrigin: "top left",
          transition: "all 0.3s"
        }}
      />
    </div>
  );
}
```
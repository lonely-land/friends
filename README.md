# 是这些伙伴们构成了我

一个友链页面的视觉实验。

白底，名字像水流一样铺满整屏，中间一行大字压在最上面 —— 字所在的位置被白色方框盖住，方框边缘渐渐透明，名字从字的两侧淌过去。

![预览](docs/preview.png)

## 用

单文件、零依赖，丢到任何静态托管（Cloudflare Pages / Vercel / GitHub Pages，或者一个 `python3 -m http.server`）就能跑。

改名字只需要动 `index.html` 顶部这一个数组：

```js
const LINKS = [
  { name: '阿岚',     url: 'https://example.com' },
  { name: 'Nova',     url: 'https://example.com' },
  // ...
];
```

## URL 参数

| 参数 | 默认 | 作用 |
| --- | --- | --- |
| `?deg=-8` | `-8` | 名字墙的倾斜角度，范围 -20 ~ 20 |
| `?alt=0` | 开 | 关掉隔行反向，变成单向流动 |
| `?static=1` | 关 | 跳过所有入场动画，直接看终态（截图 / 调试用） |

## 怎么实现的

1. **每行一条 HTML marquee**：一行内容复制两份塞进 `inline-flex`，`translate3d(-50%)` 无限循环。纯 CSS transform，走 GPU 合成层，五十行同时跑也不掉帧（早期用 SVG `textPath` 逐帧改 `startOffset` 的版本，密到这个程度会卡）。
2. **名字之间的空隙用 `margin-right` 不用 `gap`**：`gap` 会让两份副本的接缝处多出半个间距，`translate3d(-50%)` 就对不上，循环时会跳一下。
3. **遮罩按行生成**：`h1 span` 设成 `width: max-content`，盒子收缩到这一行文字的真实宽度；每行建一个白色 `div`，尺寸取它的 `getBoundingClientRect()`，四边用 `box-shadow` 向外羽化。所以「我」那一个字的白块只有中间一小条，左右照常露名字。
4. **遮罩和标题同帧淡入**：否则页面一加载中间就先空一块。
5. **倾斜要补偿**：整层 `rotate()` 之后四角会露白，按外接矩形算了一个 `scale()` 顶上去。
6. **行数取 `ceil + 3`**：多铺几行溢出边缘，任何视口高度都不会在底部露白。

## 致谢

- 灵感来源：[杨柳 WillowTree](https://willowtree1184.github.io)
- 设计指导：Carleight Wu、斬馬

## License

MIT © Lonely

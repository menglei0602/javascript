# CSS-in-JavaScript

## 命名
* 使用驼峰命名法作为对象键（即“选择器”）。

为什么？我们将这些键作为styles组件中对象的属性来访问，因此使用驼峰命名法是最方便的。
```js
// bad
{
  'bermuda-triangle': {
    display: 'none',
  },
}

// good
{
  bermudaTriangle: {
    display: 'none',
  },
}
```
- 使用与设备无关的名称（例如“小”、“中”和“大”）来命名媒体查询断点。

> 为什么？“手机”、“平板电脑”和“桌面”等常用名称与现实世界中的设备特征不符。使用这些名称会带来错误的期望。
```js
// bad
const breakpoints = {
  mobile: '@media (max-width: 639px)',
  tablet: '@media (max-width: 1047px)',
  desktop: '@media (min-width: 1048px)',
};

// good
const breakpoints = {
  small: '@media (max-width: 639px)',
  medium: '@media (max-width: 1047px)',
  large: '@media (min-width: 1048px)',
};
```

## 订购
- 在组件之后定义样式。

> 为什么？我们使用高阶组件来主题化我们的样式，这自然是在组件定义之后使用的。将样式对象直接传递给此函数可以减少间接性。

```js
// bad
const styles = {
  container: {
    display: 'inline-block',
  },
};

function MyComponent({ styles }) {
  return (
    <div {...css(styles.container)}>
      Never doubt that a small group of thoughtful, committed citizens can
      change the world. Indeed, it’s the only thing that ever has.
    </div>
  );
}

export default withStyles(() => styles)(MyComponent);

// good
function MyComponent({ styles }) {
  return (
    <div {...css(styles.container)}>
      Never doubt that a small group of thoughtful, committed citizens can
      change the world. Indeed, it’s the only thing that ever has.
    </div>
  );
}

export default withStyles(() => ({
  container: {
    display: 'inline-block',
  },
}))(MyComponent);
```

## 行内的
  - 对于使用次数较多的样式，通过prop传参使用内联样式，而不是使用多个重复的内联样式。

  > 为什么？生成主题样式表的成本可能很高，因此它们最适合离散的样式集。

    ```jsx
    // bad
    export default function MyComponent({ spacing }) {
      return (
        <div style={{ display: 'table', margin: spacing }} />
      );
    }

    // good
    function MyComponent({ styles, spacing }) {
      return (
        <div {...css(styles.periodic, { margin: spacing })} />
      );
    }
    export default withStyles(() => ({
      periodic: {
        display: 'table',
      },
    }))(MyComponent);
    ```
<!-- --todo -->
## 主题
- 使用抽象层，例如启用主题化的react-with-styles 。react-with-styles 为我们提供了、、 和 等内容，这些内容在本文档的一些示例中使用。withStyles()ThemedStyleSheetcss()
> 为什么？拥有一组共享变量来设置组件的样式非常有用。使用抽象层使这更方便。此外，这可以帮助防止您的组件与任何特定的底层实现紧密耦合，从而为您提供更多自由。

- 仅在主题中定义颜色。
 ```js
// bad
export default withStyles(() => ({
  chuckNorris: {
    color: '#bada55',
  },
}))(MyComponent);

// good
export default withStyles(({ color }) => ({
  chuckNorris: {
    color: color.badass,
  },
}))(MyComponent);
仅在主题中定义字体。

// bad
export default withStyles(() => ({
  towerOfPisa: {
    fontStyle: 'italic',
  },
}))(MyComponent);

// good
export default withStyles(({ font }) => ({
  towerOfPisa: {
    fontStyle: font.italic,
  },
}))(MyComponent);
```
- 将字体定义为相关样式集。
 ```js
// bad
export default withStyles(() => ({
  towerOfPisa: {
    fontFamily: 'Italiana, "Times New Roman", serif',
    fontSize: '2em',
    fontStyle: 'italic',
    lineHeight: 1.5,
  },
}))(MyComponent);

// good
export default withStyles(({ font }) => ({
  towerOfPisa: {
    ...font.italian,
  },
}))(MyComponent);
 ```
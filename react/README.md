# Airbnb React/JSX 风格指南
*React 和 JSX 的最合理方法*

## 基本规则
  - 每个文件仅包含一个 React 组件。
    - 但是，每个文件允许多个无状态或纯组件。eslint：react/no-multi-comp.
    - 始终使用 JSX 语法。
    - React.createElement除非您从非 JSX 文件初始化应用程序，否则请勿使用。
## 类 vs React.createClass无状态
  - 如果您有内部状态和/或参考，则class extends React.Component优先于React.createClass. eslint：react/prefer-es6-class react/prefer-stateless-function

```jsx
// bad
const Listing = React.createClass({
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
});

// good
class Listing extends React.Component {
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
}
```
  - 如果您没有状态或引用，则更喜欢普通函数（而不是箭头函数）而不是类：

```jsx
// bad
class Listing extends React.Component {
  render() {
    return <div>{this.props.hello}</div>;
  }
}

// bad (relying on function name inference is discouraged)
const Listing = ({ hello }) => (
  <div>{hello}</div>
);

// good
function Listing({ hello }) {
  return <div>{hello}</div>;
}
```
## 混入
  - 不要使用 mixins。
  > 为什么？Mixin 引入了隐式依赖关系，导致名称冲突，并导致复杂性滚雪球式增长。mixins 的大多数用例都可以通过组件、高阶组件或实用模块以更好的方式完成。

## 命名

  - 扩展：使用.jsx React 组件的扩展。eslint：react/jsx-filename-extension
  - 文件名：使用 PascalCase 作为文件名。例如，ReservationCard.jsx。
  - 参考命名：React 组件使用 PascalCase，实例使用驼峰命名法。eslint：react/jsx-pascal-case

```jsx
// bad
import reservationCard from './ReservationCard';

// good
import ReservationCard from './ReservationCard';

// bad
const ReservationItem = <ReservationCard />;

// good
const reservationItem = <ReservationCard />;
```
  - 组件命名：使用文件名作为组件名称。例如，ReservationCard.jsx应该有一个参考名称ReservationCard。但是，对于目录的根组件，请使用index.jsx用作文件名并使用目录名称作为组件名称：

```jsx
// bad
import Footer from './Footer/Footer';

// bad
import Footer from './Footer/index';

// good
import Footer from './Footer';
```
  - 高阶组件命名：使用高阶组件名称和传入组件名称的组合作为displayName生成组件的名称。例如，高阶组件withFoo()，当传递一个组件时Bar应该产生一个带有displayNameof 的组件withFoo(Bar)。

  > 为什么？组件displayName可以由开发人员工具使用或在错误消息中使用，更容易地识别函数，并且具有清晰表达这种关系的值可以帮助人们了解正在发生的情况。
  
```jsx
// bad
export default function withFoo(WrappedComponent) {
  return function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }
}

// good
export default function withFoo(WrappedComponent) {
  function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }

  const wrappedComponentName = WrappedComponent.displayName
    || WrappedComponent.name
    || 'Component';

  WithFoo.displayName = `withFoo(${wrappedComponentName})`;
  return WithFoo;
}
```

## 宣言
  - 请勿用于displayName命名组件。相反，通过引用命名组件。

```jsx
// bad
export default React.createClass({
  displayName: 'ReservationCard',
  // stuff goes here
});

// good
export default class ReservationCard extends React.Component {
}
```
## 对齐
  - 请遵循 JSX 语法的这些对齐样式。eslint：react/jsx-closing-bracket-location react/jsx-closing-tag-location

```jsx
// bad
<Foo superLongParam="bar"
     anotherSuperLongParam="baz" />

// good
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
/>

// if props fit in one line then keep it on the same line
<Foo bar="bar" />

// children get indented normally
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
>
  <Quux />
</Foo>

// bad
{showButton &&
  <Button />
}

// bad
{
  showButton &&
    <Button />
}

// good
{showButton && (
  <Button />
)}

// good
{showButton && <Button />}

// good
{someReallyLongConditional
  && anotherLongConditional
  && (
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />
  )
}

// good
{someConditional ? (
  <Foo />
) : (
  <Foo
    superLongParam="bar"
    anotherSuperLongParam="baz"
  />
)}
```
## 引号
  - 始终"对 JSX 属性使用双引号 ()，但'对所有其他 JS 使用单引号 ()。eslint：jsx-quotes

  > 为什么？常规 HTML 属性通常也使用双引号而不是单引号，因此 JSX 属性反映了此约定。

```jsx
// bad
<Foo bar='bar' />

// good
<Foo bar="bar" />

// bad
<Foo style={{ left: "20px" }} />

// good
<Foo style={{ left: '20px' }} />
```
## 间距
  - 始终在自闭合标签中包含一个空格。eslint：no-multi-spaces，react/jsx-tag-spacing

```jsx
// bad
<Foo/>

// very bad
<Foo                 />

// bad
<Foo
 />

// good
<Foo />
```
  - 不要用空格填充 JSX 花括号。eslint：react/jsx-curly-spacing

```jsx
// bad
<Foo bar={ baz } />

// good
<Foo bar={baz} />
```
## Props
  - 始终使用驼峰命名法作为 prop 名称，如果 prop 值是 React 组件，则使用 PascalCase。

```jsx
// bad
<Foo
  UserName="hello"
  phone_number={12345678}
/>

// good
<Foo
  userName="hello"
  phoneNumber={12345678}
  Component={SomeComponent}
/>
```
  - 当 prop 的值是明确的时，省略它true。eslint：react/jsx-boolean-value

```jsx
// bad
<Foo
  hidden={true}
/>

// good
<Foo
  hidden
/>

// good
<Foo hidden />
```
  - 始终在<img>标签上包含一个alt prop。如果图像是展示性的，alt则可以是空字符串或<img>必须有role="presentation". eslint：jsx-a11y/alt-text

```jsx
// bad
<img src="hello.jpg" />

// good
<img src="hello.jpg" alt="Me waving hello" />

// good
<img src="hello.jpg" alt="" />

// good
<img src="hello.jpg" role="presentation" />
```
- 请勿在<img> alt道具中使用“图像”、“照片”或“图片”等词语。eslint：jsx-a11y/img-redundant-alt

  > 为什么？屏幕阅读器已经将img元素宣布为图像，因此无需在替代文本中包含此信息。

```jsx
// bad
<img src="hello.jpg" alt="Picture of me waving hello" />

// good
<img src="hello.jpg" alt="Me waving hello" />
```

  - 避免使用数组索引作为keyprop，更喜欢稳定的 ID。eslint：react/no-array-index-key
  > 为什么？不使用稳定的 ID是一种反模式，因为它会对性能产生负面影响并导致组件状态出现问题。

如果项目的顺序可能发生变化，我们不建议对键使用索引。

```jsx
// bad
{todos.map((todo, index) =>
  <Todo
    {...todo}
    key={index}
  />
)}

// good
{todos.map(todo => (
  <Todo
    {...todo}
    key={todo.id}
  />
))}
```
  - 始终为所有非必需的 props 定义显式的 defaultProps。
  > 为什么？propTypes 是文档的一种形式，提供 defaultProps 意味着代码的读者不必假设太多。此外，这可能意味着您的代码可以省略某些类型检查。

```jsx
// bad
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// good
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};
SFC.defaultProps = {
  bar: '',
  children: null,
};
```
  - 谨慎使用传播道具。
  > 为什么？否则你更有可能将不必要的 props 传递给组件。对于 React v15.6.1 及更早版本，您可以将无效的 HTML 属性传递给 DOM。

  异常处理：

  - 代理向下 props 和提升 propTypes 的 HOCs
```jsx
function HOC(WrappedComponent) {
  return class Proxy extends React.Component {
    Proxy.propTypes = {
      text: PropTypes.string,
      isLoading: PropTypes.bool
    };

    render() {
      return <WrappedComponent {...this.props} />
    }
  }
}
```
- 使用已知的、明确的 props 传播对象。当使用 Mocha 的 beforeEach 构造测试 React 组件时，这尤其有用。

```jsx
export default function Foo {
  const props = {
    text: '',
    isPublished: false
  }

  return (<div {...props} />);
}
```
- 使用注意事项： 尽可能过滤掉不必要的道具。另外，使用prop-types-exact来帮助防止错误。

```jsx
// bad
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...this.props} />
}

// good
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...relevantProps} />
}
```
## Refs
  - 始终使用 ref 回调。埃斯林特：react/no-string-refs

```jsx
// bad
<Foo
  ref="myRef"
/>

// good
<Foo
  ref={(ref) => { this.myRef = ref; }}
/>
```
## 括号
  - 当 JSX 标签跨越一行以上时，将它们括在括号中。埃斯林特：react/jsx-wrap-multilines

```jsx
// bad
render() {
  return <MyComponent variant="long body" foo="bar">
           <MyChild />
         </MyComponent>;
}

// good
render() {
  return (
    <MyComponent variant="long body" foo="bar">
      <MyChild />
    </MyComponent>
  );
}

// good, when single line
render() {
  const body = <div>hello</div>;
  return <MyComponent>{body}</MyComponent>;
}
```
## 标签
  - 始终自动关闭没有子项的标签。埃斯林特：react/self-closing-comp

```jsx
// bad
<Foo variant="stuff"></Foo>

// good
<Foo variant="stuff" />
如果您的组件具有多行属性，请在新行上关闭其标签。埃斯林特：react/jsx-closing-bracket-location

// bad
<Foo
  bar="bar"
  baz="baz" />

// good
<Foo
  bar="bar"
  baz="baz"
/>
```
## 方法
  - 为构造函数中的 render 方法绑定事件处理程序。埃斯林特：react/jsx-no-bind

  > 为什么？渲染路径中的绑定调用会在每个渲染上创建一个全新的函数。不要在类字段中使用箭头函数，因为这使它们难以测试和调试，并且会对性能产生负面影响，而且从概念上讲，类字段用于数据，而不是逻辑。

```jsx
// bad
class extends React.Component {
  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv.bind(this)} />;
  }
}

// very bad
class extends React.Component {
  onClickDiv = () => {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv} />
  }
}

// good
class extends React.Component {
  constructor(props) {
    super(props);

    this.onClickDiv = this.onClickDiv.bind(this);
  }

  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv} />;
  }
}
```
  - 不要在 React 组件的内部方法中使用下划线前缀。

  > 为什么？下划线前缀有时在其他语言中用作表示隐私的约定。但是，与这些语言不同的是，JavaScript 没有对隐私的本机支持，一切都是公开的。无论您的意图如何，向属性添加下划线前缀实际上并不会使它们成为私有属性，并且任何属性（无论是否带有下划线前缀）都应被视为公共属性。

```jsx
// bad
React.createClass({
  _onClickSubmit() {
    // do stuff
  },

  // other stuff
});

// good
class extends React.Component {
  onClickSubmit() {
    // do stuff
  }

  // other stuff
}
```
  - 确保在您的render方法中返回一个值。埃斯林特：react/require-render-return

```jsx
// bad
render() {
  (<div />);
}

// good
render() {
  return (<div />);
}
```
## 组件名、文件名、目录名大小写
  - 组件名用PascalCase；文件名和目录名用camelCase，
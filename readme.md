# react-modalify

Transform your plain react component into a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

## Install

```shell
npm install react-modalify --save
```

## Usage

Write your plain react component with a ```close``` prop that ```react-modalify``` pass in. Call it when you want close this modal.

```xml
class Notify extends Component {
    render() {
        return (
            <div className="panel">
                <h1>Hello, Modalify!</h1>
                <div>
                    <button onClick={this.props.close}>OK</button>
                </div>
            </div>
        )
    }
}
```

Modalify it

```js
import {modalify} from 'react-modalify'

let notify = modalify(Notify);
```

Now you can use it

```js
notify()
```

## Features

### Call modal() return a promise

```xml
class Confirm extends Component {
    render() {
        return (
            <div className="panel">
                <h1>Hello, Modalify!</h1>
                <div>
                    <button onClick={() => {
                        this.props.close('Yes');
                    }}>Yes</button>
                    <button onClick={() => {
                        this.props.close('No');
                    }}>No</button>
                </div>
            </div>
        )
    }
}
```

```js
let confirm = modalify(Confirm);
confirm().then((returnValue) => {
	console.log(returnValue);
});
```

### Pass props via modal(props)

```xml
class ReceiveProps extends Component {
    render() {
        return (
            <div className="panel">
                <h1>{`Hello, ${this.props.user}!`}</h1>
                <div>
                    <button onClick={this.props.close}>OK</button>
                </div>
            </div>
        )
    }
}
```

```js
let receiveProps = modalify(ReceiveProps);
receiveProps({
	user: 'Tom'
})
```

### Nested modals support

```react-modalify``` manage a stack of modals. So You can call modal() in a modal.

## Thoughts for Modals in react

Sorry, Chinese version only.

很多其它的react modal组件库，比如react-modal，react-portal。他们都是按照react的编程习惯。把modal组件也作为一个component放到使用的地方。这样会造成几个问题：

1. 在没有用到modal的时候，这个组件也会被渲染。为了避免这个问题，开发者就使用了奇技淫巧，重写component的生命周期，使modal只有被open的时候才渲染，而且渲染在当前节点之外。
1. 调用者要关心modal的位置。在列表中用到modal的话，则它会被重复渲染很多遍。
1. modal的打开状态到底是由调用者控制，还是由modal自己控制？这个关系到这个状态是用props还是用state。很显然modal不能自己打开自己，所以一定是调用者用props传入。但是大部分modal的关闭都不是调用者控制的，所以又得在dialog里面写state。导致这个状态相当的混乱。
1. 值的传递。大部分modal是需要返回结果给调用者的，使用component生成的组件，传递返回值相当的麻烦。这个相当于子组件要修改父组件的值。

以上几个原因导致现在modal组件都写的非常奇技淫巧。但是回归本质，modal就是一个异步调用，它能返回结果, 这个跟promise的时候场景很像，比如ajax。

所以react-modalify反其道而行，把一个compoent转化成了一个promise调用。使用者并不关心modal应该渲染在哪里，modal的打开关闭状态应该怎么控制。只需要关心输入和输出即可。

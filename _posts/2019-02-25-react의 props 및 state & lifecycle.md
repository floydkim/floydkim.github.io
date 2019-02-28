# props

리액트 컴포넌트가 갖는 속성 (attributes)
컴포넌트(component) - 재사용 가능한 작은 UI 조각, 구성 요소

```react
const value = "red";
<div className={value}>hello world</div>;
// <div> ~ </div> 모두를 div 컴포넌트라고 부릅니다.
```
div 컴포넌트의 attribute들이 **props** 오브젝트의 속성으로 등록됩니다.  
(attribute 이름을 key : 각각의 값을 value로 하는 key:value pair들이 props 오브젝트의 속성으로 등록되는 것 입니다.)

(참고: props에는 기본적으로 .children 속성이 포함돼있습니다. 컴포넌트의 child들을 담고있습니다.)

<hr />
아래는 [React.js 공식문서](https://reactjs.org/docs/components-and-props.html)의 번역입니다.
<hr />

##### 컴포넌트는 UI를 독립적이고, 재사용 가능한 조각으로 나눌 수 있게 하며, 각 조각에 대해 단독적으로 생각하게 해 줍니다. 이 페이지는 컴포넌트의 아이디어를 소개합니다.

개념적으로, 컴포넌트들은 자바스크립트 함수들과 같습니다. 임의의 입력(props라고 부릅니다)을 받고 스크린에 무엇이 나타나야 하는지 묘사하는 리액트 엘리먼트를 리턴합니다.

(생략)

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

1. `ReactDom.render()`를 `<Welcome name="Sara" />` 엘리먼트와 함께 호출합니다.
2. 리액트가 `{name: 'Sara'}`를 props로 삼아서 `Welcome` 컴포넌트를 호출합니다.
3. `Welcome` 컴포넌트가 `<h1>Hello, Sara</h1>` 엘리먼트를 결과로 리턴합니다.
4. 리액트 DOM이 효율적으로 DOM을 업데이트합니다.

(생략)




# state & lifecycle 
[React.js 공식문서](https://reactjs.org/docs/state-and-lifecycle.html)의 번역입니다.  

<hr />



##### 이 페이지는 리액트 컴포넌트 안의 state와 lifecycle의 개념을 소개합니다.  

앞 섹션의 ticking clock을 생각해보십시오. 엘리먼트를 렌더링할 때 우리는 UI를 업데이트하는 오직 하나의 방법만을 배웠습니다. 렌더된 결과를 변경하기 위해 `ReactDom.render()`를 호출하는 것입니다.

```react
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render( //
    element, //
    document.getElementById('root') //
  ); //
}

setInterval(tick, 1000);
```

이번 섹션에서, 우리는 어떻게 `Clock` 컴포넌트를 재사용가능하고 캡슐화된(은닉화된; encapsulated) 형태로 만들 수 있는지 배울 것입니다. 이 컴포넌트는 자신의 타이머를 세팅하며, 매 초 업데이트 될 것입니다.

우리는 시계가 어떻게 보이는지 캡슐화 함으로써 시작할 수 있습니다. (?)

```react
function Clock(props) {
  return (
    <div> //
      <h1>Hello, world!</h1> //
      <h2>It is {props.date.toLocaleTimeString()}.</h2> //
    </div> //
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />, //
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

그러나, 이것은 중대한 요구사항을 놓치고 있습니다: `Clock`이 타이머를 설정하고, UI를 매 초 업데이트 하는 동작은 `Clock`의 구현 세부 사항이어야합니다.


이상적으로는 우리는 코드를 한번만 적고, `Clock`이 스스로 업데이트 해야 합니다.
```react
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

이것을 구현하기 위해, `Clock` 컴포넌트에 상태(state)를 추가해야 합니다.

State는 props와 비슷하지만, private하고 컴포넌트에 의해 완전히 컨트롤 가능합니다.

클래스로 정의된 컴포넌트가 몇가지 추가적인 기능을 갖는다는 것을 [이전에 언급](https://reactjs.org/docs/components-and-props.html#functional-and-class-components)했습니다.  
로컬 state는 정확히 다음과 같습니다: 오직 클래스에만 사용가능한 기능.

(생략)

### 클래스에 라이프사이클 메서드 추가하기

많은 컴포넌트로 이루어진 어플리케이션에서, 컴포넌트들에 의해 점유된 리소스를 컴포넌트가 제거(destroy)된 후에 비워주는 것은 매우 중요합니다.

우리는 `Clock`이 처음으로 렌더될 때 마다 타이머를 세팅하고 싶습니다. 이것은 리액트에서 "mounting"이라고 부릅니다.

우리는 또한 `Clock`이 만들어 낸 DOM이 제거될 때 마다 타이머를 clear하고 싶습니다. 이것은 리액트에서 "unmounting"이라고 부릅니다.

우리는 컴포넌트가 마운트되고 언마운트될 때 코드를 실행하도록 컴포넌트 클래스에 특별한 메서드를 선언할 수 있습니다. :

```react
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
이 메서드들은 "lifecycle methods"라고 부릅니다.

`componentDidMount()` 메서드는 DOM에 컴포넌트 출력이 렌더된 후에 실행됩니다. 타이머 셋업을 위치시키기에 좋은 장소입니다.

```react
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

`this`의 오른편에 우리가 어떻게 타이머 ID를 저장하는지에 주목하세요.

`this.props`는 리액트가 스스로 만들어내고, `this.state`는 특별한 의미를 지닌 반면에, 데이터 플로우에 참여하지 않는 어떤것(타이머 ID 같은)을 저장하고자 한다면 클래스에는 자유롭게 추가적인 필드를 수동으로 추가해줄 수 있습니다.

타이머를 `componentWillUnmount()` 라이프사이클 메서드로 타이머를 분해해 보겠습니다. :

```react
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

드디어 우리는 `Clock` 컴포넌트가 매 초마다 실행할 `tick()` 이라는 메서드를 구현할 것입니다.

컴포넌트 로컬 state에 업데이트를 스케쥴 할 `this.setState()`를 사용합니다.

```react
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
이제 타이머가 매 초 똑딱거립니다.

빠르게 무슨 일이 벌어지는지와 메서드들이 호출되는 순서를 되짚어봅시다.

1. `<Clock />`이 `ReactDOM.render()`에 인자로 전달될 때, 리액트는 `Clock` 컴포넌트의 constructor를 호출합니다. `Clock`이 현재 시간을 표시해야 하기 때문에, `this.state`를 현재 시간을 포함하는 오브젝트로 초기화 시킵니다. 우리는 나중에 이 state를 업데이트 할 것입니다.

2. 리액트는 다음으로 `Clock` 컴포넌트의 `render()` 메서드를 호출합니다. 이것이 스크린에 어떤 것이 표시되어야 하는지 리액트가 배우는 방식입니다. 리액트는 그다음 `Clock`의 렌더 출력에 맞도록 DOM을 업데이트합니다.

3. `Clock` 출력이 DOM에 삽입될 때, 리액트는 `componentDidMount()` 라이프사이클 메서드를 호출합니다. 그 안에서, `Clock` 컴포넌트가 브라우저에게 컴포넌트의 `tick()` 메서드를 1초에 한 번씩 반복해 호출하는 타이머를 셋업하도록 요청합니다.

4. 매 초 마다 브라우저가 `tick()` 메서드를 호출합니다. 그 안에서, `Clock` 컴포넌트는 현재 시간을 담은 오브젝트를 가진 `setState()`를 호출함으로써 UI 업데이트를 스케쥴합니다. `setState()` 호출 덕분에, 리액트가 state가 변경된 것을 알게 됩니다. 그리고 스크린에 어떤 것이 떠야 하는지 알기 위해 `render()` 메서드를 다시 호출합니다. 이번에는, `render()` 메서드 안의 `this.state.date`가 다를 것이고, 따라서 렌더 출력은 업데이트된 시간을 포함할 것입니다. 리액트는 그에 따라 DOM을 업데이트합니다.

5. `Clock` 컴포넌트가 DOM에서 제거된다면, 리액트는 `componentWillUnmount()` 라이프사이클 메서드를 호출하고, 타이머가 멈추게 됩니다.

<hr />

아래 내용은 [이 글](https://blog.bitsrc.io/understanding-react-v16-4-new-component-lifecycle-methods-fa7b224efd7d) 중 업데이트와 관련된 부분만 발췌해 번역했습니다.

...

### 2) Updating

이 페이즈는 리액트 컴포넌트가 브라우저에 태어난 이후 새 업데이트를 받아 성장할 때 시작됩니다. 컴포넌트는 두 가지 방법으로 업데이트될 수 있습니다. **하나는 부모로부터 새 props를 전달받아서, 또 하나는 현재 state를 업데이트해서 이루어집니다.**

위 두 방법 어느것이든 업데이트가 일어날 때, 메서드의 리스트는 다음과 같은 순서로 호출됩니다. :

- **static getDerivedStateFromProps**  
  글 위쪽에 Mounting 페이즈에서와 정확히 동일하게 동작합니다.

  (윗부분 발췌: *props로부터 파생된 state를 가져온다*는 이름에서 알 수 있듯이, state가 props에 의존적일때 사용됩니다. 그러므로 props가 변경될 때 마다 state가 계속해서 동기화 되어야 합니다.
  
  이 메서드는 `constructor`가 호출된 후에 실행되며 컴포넌트의 state를 업데이트 하기 위한 오브젝트를 리턴해야 합니다. 만약 null이 리턴되면 state에는 어떤 변화도 일어나지 않습니다.
  
  `getDerivedStateFromProps` 메서드는 static합니다. 따라서 `this`에 대한 접근이 없습니다. 이 메서드는 현재 state와 props에 대한 접근만 가집니다. 따라서, 만약 state가 props에 의존적이라면 state가 여기에서 업데이트될 수 있습니다. 이 메서드는 리액트 v16.3+에서 새로 추가되었습니다. 사용 예: state가 새로 들어오는 props와 계속 동기화 되도록 유지합니다. 이 메서드는 `componentWillReceiveProps`메서드의 더 안전한 대체 수단입니다. 이 메서드는 순수함수(pure function)이고, 따라서 어떤 부수효과(side effect)도 발생시켜서는 안됩니다.)

- **shouldComponentUpdate**  

- **render**

- **getSnapshotBeforeUpdate**

- **componentDidUpdate**


<hr />

[리액트 라이프사이클 다이어그램](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)


#### **mounting**
1. `constructor` 호출
2. `getDerivedStateFromProps` 호출
3. `render` 호출
4. DOM 업데이트
5. `componentDidMount` 호출

#### **updating**
1. 새 `props`를 받거나, `setState()` 호출되었거나, `forceUpdate()` 호출되면
2. `getDerivedStateFromProps` 호출
3. forceUpdate()로 시작된게 아니라면, `shouldComponentUpdate` 호출됨. true 리턴된 경우 다음으로.
4. `render` 호출
5. `getSnapshotBeforeUpdate` 호출 (현상태 저장)
6. DOM 업데이트
7. `componentDidUpdate` 호출

#### **unmounting**
1. `componentWillUnmount` 호출

<hr />


최근에는 hook 이라는 것이 나왔다고 합니다. [벨로퍼트님 블로그 관련 글](https://velog.io/@velopert/react-hooks)
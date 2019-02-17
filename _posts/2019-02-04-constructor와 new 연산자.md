원래는 prototype을 이해하려고 읽었던 내용인데 `prototype 객제`와 `__proto__`까지 모두 한 포스트에 담자니 너무 글이 길어지고, 내용이 prototype 자체와는 약간 벗어나 있다고 생각되어 분리해 올립니다.  
그리고 constructor와 함께 자주 사용되는 new 연산자의 동작을 먼저 알아보겠습니다.

참고해둘 선행지식
* *오브젝트* **instanceof** *컨스트럭터*  
    (MDN) *컨스트럭터*의 prototype속성이 *오브젝트*의 프로토타입 체인 상에 존재하면 true를 리턴합니다.

* **Object.create(** *오브젝트* **)**  
    (MDN) 인자로 들어온 *오브젝트*(prototype 객체)를 원형으로 삼아 새로운 오브젝트를 만듭니다.  
    (새 오브젝트를 만드는데, 그 오브젝트의 \_\_proto\_\_속성이 인자로 전달한 *오브젝트*를 가리키도록 지정해 만듭니다.)

* 상속(inheritance)과 인스턴스화(instantiation) ([wikipedia](https://en.wikipedia.org/wiki/Instance_(computer_science)))  
인스턴스는 클래스로부터 만들어진 오브젝트를 말하며, 인스턴스를 만드는 행위나 동작을 인스턴스화(instantiation)라고 부릅니다. 다만 자바스크립트는 class-based 언어가 아닌 prototype-based 언어이며 함수(constructor)를 이용하여 class를 구현하고 있습니다. 또한 용어로 instantiation과 함께 construction을 사용하고 있습니다. (ES6에서 class 문법이 도입되었지만 내부 동작까지 class-based인 것은 아닙니다. 그리고 저는 class-based 언어 경험 없이 웹 상의 설명을 믿고있음을 알립니다..) 한편, 상속은 인스턴스화와 구분해 말할 수 있습니다. 인스턴스를 만드는 것을 두고 클래스의 속성을 상속받는다고 말할 수 있으나, class끼리 속성이나 메서드를 상속하는 것은 분명 인스턴스화와 다릅니다. 다만 자바스크립트는 두 경우 모두 prototype을 이용해 가능하므로 구분이 모호한 부분이 있습니다.

* `prototype 객체`와 `__proto__`  
선행 지식이긴 한데 이것부터 읽다가 지칠 수 있으니 아래 글을 먼저 읽으시다가 궁금해지시면 관련한 [이전 포스팅](/2019-01-30-prototype-%EA%B0%9D%EC%B2%B4%EC%99%80-__proto__/)을 읽어주세요!


본편 시작하겠습니다. 우선 간단히 new 연산자의 동작에 대해 알아보겠습니다.
# `new` operator
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)

new Foo(...) 라는 코드가 실행되면 다음과 같은 일들이 일어납니다:

1. Foo.prototype을 상속받으며 새 오브젝트가 만들어집니다. (역자 주: 인스턴스의 \_\_proto\_\_속성에 Foo.prototype 객체가 연결됩니다. prototype chain이 만들어지는 것입니다. `Object.create(Foo.prototype)` 구문으로 오브젝트를 만드는 것과 같습니다.)
2. 생성자 함수 Foo가 명시된 인자들을 전달받으며 호출되며, `this` 키워드가 새로 만들어진 오브젝트에 바인딩됩니다. new Foo 는 new Foo()와 같습니다. 다시 말해, 만약 아무 인자도 명시되지 않으면 Foo는 인자 없이 호출됩니다.
3. (null, false, 3.1415 또는 다른 primitive 타입이 아니고) 오브젝트가 생성자 함수로부터 리턴된 경우 이 오브젝트가 전체 `new` 표현식의 결과가 됩니다. 만약 생성자 함수가 오브젝트를 명시적으로 리턴하지 않는다면, 1번 단계에서 만들어진 오브젝트가 대신 사용됩니다. (보통 생성자들은 값을 리턴하지 않지만, 생성 과정의 보통 오브젝트를 덮어쓰고 싶다면 그렇게 할 수 있습니다.)

(다음은 constructor 항목입니다.)

# `constructor` property

(아래 내용은 [MDN constructor 항목](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)의 번역과 더불어 이해를 위한 주석을 붙인 것입니다.)

`constructor` 속성은 인스턴스 객체를 만든 `Object`인, 생성자 함수로의 참조를 리턴합니다.  
이 속성의 값은 함수 자체로의 참조이며 함수의 이름을 갖는 스트링이 아닙니다.  
이 값은 primitive 값에 대해서는 read-only입니다.

## Description
모든 오브젝트(`Object.create(null)`로 만든 오브젝트 제외)는 `constructor` 속성을 갖게 됩니다. 생성자 함수를 사용하지 않고 만들어진 오브젝트(`{}`나 `[]`같은 것)들은 `constructor` 속성이 해당 객체의 fundamental한(기본이 되는) 오브젝트 컨스트럭터 타입을 가리키게 됩니다.
```javascript
var a = {};
a.constructor; // Object
var a = [];
a.constructor; // Array
var a = new Array;
a.constructor; // Array
var n = new Number(3);
n.constructor; // Number
```

**(역자 주)** 나중에 알게 된 것인데, constructor 속성은 모든 오브젝트에서 \_\_proto\_\_속성을 통해 마치 자신이 constructor 속성을 직접 가진 것 처럼 **접근**할 수 있는 속성입니다. (prototype 객체를 제외하면 모든 오브젝트들은 constructor속성을 직접 가지지 않습니다) 모든 오브젝트는 \_\_proto\_\_속성을 직접 가지고 있고, 이 속성은 자신(오브젝트)를 만든 원형 오브젝트의 prototype 객체를 가리키기 때문에, 결국 \_\_proto\_\_를 따라가면 prototype 객체의 기본 구성요소인 constructor 속성에 접근할 수 있게 됩니다.
```javascript
var obj = {};
obj.constructor === Object; // true
// 이렇게 직접 가진 속성인듯이 이용할 수 있지만 사실은 __proto__를 통해 건너서 접근하는 것입니다.

obj.__proto__ === Object.prototype; // true
obj.__proto__.constructor === Object.prototype.constructor; // true
```

## Examples
### 오브젝트의 constructor 출력하기
이 예제는 원형인 Tree와 그 타입의 오브젝트인 theTree를 만듭니다. 그리고 오브젝트 theTree의 `constructor`속성을 화면에 나타내는 예제입니다.
```javascript
function Tree(name) {
    this.name = name;
}

var theTree = new Tree('Redwood');   // Tree의 인스턴스인 theTree
console.log('theTree.constructor is ' + theTree.constructor);
```
이 예제의 출력결과는 다음과 같습니다.
```javascript
theTree.constructor is function Tree(name) {
    this.name = name;
}
```

### 오브젝트의 constructor 변경하기
다음 예제는 일반적인 오브젝트의 constructor값을 변경하는 방법을 보여줍니다. true, 1, “test”같은 primitive값들은 영향을 받지 않습니다.(read-only 네이티브 컨스트럭터를 갖고있기 때문입니다.)  이 예제는 오브젝트의 `constructor` 속성에 의존하는것이 항상 안전하지는 않다는 것을 보여줍니다.

```javascript
function Type() {}

var types = [
    new Array(),
    [],
    new Boolean(),
    true,    // 변경되지 않을 것입니다.
    new Date(),
    new Error(),
    new Function(),
    function() {},
    Math,
    new Number(),
    1,    // 변경되지 않을 것입니다.
    new Object(),
    {},
    new RegExp(),
    /(?:)/,
    new String(),
    'test'    // 변경되지 않을 것입니다.
];

for (var i = 0; i < types.length; i++) {
    types[i].constructor = Type; // 각각의 constructor에 연결된 함수를 Type 함수로 재할당합니다.
    types[i] = [types[i].constructor, types[i] instanceof Type, types[i].toString()];
}

console.log(types.join('\n')); 
```
(역자 주: 출력은 생략합니다. primitive 값을 제외한 모든 오브젝트의 constructor가 Type함수로 변경된 것을 확인할 수 있는 결과입니다.)

### 함수의 constructor 변경하기
함수를 function-constructor(나중에 new로 호출)로 정의할 때와, prototype상속 체인을 정의할 때 주로 사용되는 예제입니다.

```javascript
function Parent() {}
Parent.prototype.parentMethod = function parentMethod() {};

function Child() {}
Child.prototype = Object.create(Parent.prototype); // Child 프로토타입을 Parent 프로토타입으로 재정의
// (역자 주) Child.prototype의 값은 이제 오브젝트 {__proto__: Parent.prototype}로 대체됩니다.

Child.prototype.constructor = Child; // original constructor를 Child 로 돌려줌
// (역자 주) {__proto__: Parent.prototype} 에 constructor 속성을 추가해 Child함수를 할당합니다.
```
그런데 마지막 라인을 실행할 필요가 있는 때는 언제일까요? 불행히도 답은 상황에 따라 달라집니다. (역자 주: 목적에 따라 마지막 constructor 할당은 필요할수도, 필요하지 않을 수도 있습니다.)

생성자를 다시 할당하는 것이 중요한 역할을 하는 경우와, 사용되지 않는 잉여 코드가 되는 경우를 정의해봅시다.

첫번째로 다음 예제를 봅시다: 오브젝트가 자기 자신을 만들기 위한 create메서드를 가지도록 합니다.
```javascript
function Parent() {};
function CreatedConstructor() {};

CreatedConstructor.prototype = Object.create(Parent.prototype);
// (역자 주) Parent.prototype을 원형으로 새 오브젝트를 만들어, CreatedConstructor.prototype에 할당


CreatedConstructor.prototype.create = function create() {
    return new this.constructor(); // (역자 주) this의 생성자를 가져와 새 인스턴스를 만들어 리턴
}
// (역자 주) CreatedConstructor의 인스턴스는 이 create메서드를 상속받습니다.

new CreatedConstructor().create().create(); // TypeError
// (역자 주) new CreatedConstructor()는 오브젝트이며,
// 첫번째 create()는 this.constructor를 가져오는데
// __proto__를 거쳐서 constructor 속성을 가져옵니다.
// (프로토타입 체인 상에 가장 먼저 발견되는 constructor인 Parent함수를 가져옵니다.)
// {__proto__: {
//     create(), 
//     __proto__: Parent.prototype{
//         constructor: Parent,  <- 이걸 가져옵니다.
//         __proto__: Object
//         }
//     }
// }
// 즉, new CreatedConstructor().create()는 Parent생성자의 인스턴스를 만듭니다.
// 그런데 Parent의 인스턴스는 위에 정의했던 create메서드를 갖고있지 않기 때문에
// undefined.create();가 되며 Type Error가 납니다.
```
위 예제에서는 constructor 가 Parent에 연결돼있기 때문에 exception이 보여지게 됩니다.

이걸 피하기 위해서는 우리가 사용하고자 하는 필요한 생성자를 할당해주기만 하면 됩니다.
```javascript
function Parent() {}; 
function CreatedConstructor() {} 

CreatedConstructor.prototype = Object.create(Parent.prototype); 
CreatedConstructor.prototype.constructor = CreatedConstructor; // set right constructor for further using
// (역자 주) 앞 예제와 달리, CreatedConstructor.prototype에 constructor 속성을 갖습니다.
// 이제는 constructor를 찾기 위해 __proto__ 한번만 보면 됩니다.
// (한단계 더 타고가면 Parent를 값으로 갖는 constructor속성이 여전히 있지만, 그 전에 탐색이 끝납니다.)

CreatedConstructor.prototype.create = function create() { 
    return new this.constructor();
} 

new CreatedConstructor().create().create(); // it's pretty fine
// (역자 주) 앞 예제와 달리 new CreatedConstructor()로 만든 인스턴스가 아래와 같습니다.
// {__proto__: {
//     constructor: CreatedConstructor,  <- create의 this.constructor가 이걸 참조합니다.
//     create(), 
//     __proto__: Parent.prototype{
//         constructor: Parent,
//         __proto__: Object
//         }
//     }
// }
// 이제는 .create()를 몇 번을 붙이든 항상 CreatedConstructor의 인스턴스를 만듭니다.
// 따라서 에러 없이 create메서드를 계속해서 불러올 수 있습니다.
```

좋습니다. 이제 constructor를 변경하는게 왜 유용한지 아주 명확해졌습니다.  
(역자: ..?)

한 케이스를 더 생각해 보겠습니다.
```javascript
function ParentWithStatic() {}

ParentWithStatic.startPosition = { x: 0,  y: 0 };
ParentWithStatic.getStartPosition = function getStartPosition() {
    return this.startPosition;
}

function Child(x, y) {
    this.position = {
        x: x,
        y: y
    };
}

Child.prototype = Object.create(ParentWithStatic.prototype);
Child.prototype.constructor = Child;

Child.prototype.getOffsetByInitialPosition = function getOffsetByInitialPosition() {
    var position = this.position;
    var startPosition = this.constructor.getStartPosition(); // error undefined is not a function, constructor가 Child이기 때문입니다.
    // (역자 주) constructor가 Child라서 ParentWithStatic의 getStartPosition 메서드를 불러올 수 없습니다.

    return {
        offsetX: startPosition.x - position.x,
        offsetY: startPosition.y - position.y
    }
};
```
이 예제에서는 부모 constructor를 변경하지 않아야 올바르게 동작합니다.

**종합하면**: constructor를 수동으로 업데이트 하거나 설정하는 것은 다른 결과, 혹은 때때로 혼란스러운 결과를 초래합니다. 이를 방지하기 위해서는 각 특정 케이스마다 constructor의 역할을 정의하기만 하면 됩니다. 대부분 케이스에서 constructor는 사용되지 않고, 재할당이 불필요합니다.
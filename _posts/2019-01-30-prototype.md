prototype을 이해하기 위해 MDN의 `constructor`, `Object.prototype`, `Object.prototype.__proto__` 항목에 대해 전부 혹은 일부를 보며 번역본을 남겼다.  
(함수 오브젝트와 prototype 객체와 \_\_proto\_\_ 속성의 관계에 대해서는 다루지 않음.)


참고해둘 선행지식 (MDN)
* *오브젝트* **instanceof** *컨스트럭터*  
    컨스트럭터의 프로토타입속성이 오브젝트의 프로토타입 체인 상에 존재하면 true

* **Object.create(** *오브젝트* **)**  
    인자로 들어온 오브젝트(프로토타입객체)를  원형으로 삼아 새로운 오브젝트를 만듦.






# `constructor` property

(아래 내용은 [MDN constructor 항목](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)의 번역과 더불어 이해를 위한 주석을 붙여둔 것이다.)

`constructor` 속성은 인스턴스 객체를 만든 `Object` 컨스트럭터 함수로의 참조를 리턴한다.
이 속성의 값은 함수 자체로의 참조이며 함수의 이름을 갖는 스트링이 아니다.
이 값은 primitive 값에 대해서는 read-only다.

## description
모든 오브젝트는 `constructor` 속성을 갖는다. 생성자 함수를 사용하지 않고 만들어진 오브젝트({}나 []같은것)들은 `constructor` 속성이 해당 객체의 fundamental한 오브젝트 컨스트럭터 타입을 가리키게 된다.
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
## Examples
### 오브젝트의 constructor 출력하기
이 예제는 prototype과 Tree와 그 타입의 오브젝트인 theTree를 만든다. 그리고 오브젝트 theTree의 `constructor`속성을 화면에 나타낸다.
```javascript
function Tree(name) {
    this.name = name;
}

var theTree = new Tree('Redwood');   // Tree의 인스턴스인 theTree
console.log('theTree.constructor is ' + theTree.constructor);
```
이 예제의 출력결과는 다음과 같다.
```javascript
theTree.constructor is function Tree(name) {
    this.name = name;
}
```

### 오브젝트의 constructor 변경하기
다음 예제는 일반적인 오브젝트의 constructor값을 변경하는 방법을 보여준다. true, 1, “test”같은 primitive값들은 영향을 받지 않는다.(read-only 네이티브 컨스트럭터를 갖고있다.)  이 예제는 오브젝트의 `constructor` 속성에 의존하는것이 항상 안전하지는 않다는 것을 보여준다.

```javascript
function Type() {}

var types = [
    new Array(),
    [],
    new Boolean(),
    true,    // 변경되지 않을것이다
    new Date(),
    new Error(),
    new Function(),
    function() {},
    Math,
    new Number(),
    1,    // 변경되지 않을것이다
    new Object(),
    {},
    new RegExp(),
    /(?:)/,
    new String(),
    'test'    // 변경되지 않을것이다
];

for (var i = 0; i < types.length; i++) {
    types[i].constructor = Type;    // 각각의 constructor에 연결된 함수를 Type 함수로 재할당하고있다
    types[i] = [types[i].constructor, types[i] instanceof Type, types[i].toString()];
}
 
console.log(types.join('\n')); 
```
출력은 생략. primitive 값을 제외한 모든 오브젝트의 constructor가 Type함수로 변경되었다.

### 함수의 constructor 변경하기
주로 함수를 function-constructor(나중에 new로 호출)로 정의할 때, prototype상속 체인을 정의할 때 사용된다.

```javascript
function Parent() {}
Parent.prototype.parentMethod = function parentMethod() {};

function Child() {}
Child.prototype = Object.create(Parent.prototype); // Child 프로토타입을 Parent 프로토타입으로 재정의
// Child.prototype에는 이제 Parent.prototype을 담은 오브젝트가 들어있다.
// Child.prototype 'Parent' > Object > Parent.prototype
// Child.constructor 와 Parent.constructor 는 둘 다 function기본객체다
// (Parent.prototype.constructor 는 Parent함수다)

Child.prototype.constructor = Child; // original constructor를 Child 로 돌려준다
// Child.prototype 'Parent' > .constructor — Child함수   가 추가됐다.
// Child.prototype 'Parent' > .__proto__ — Object > Parent.prototype  는 그대로 있어서 Child.prototype에 항목이 2개가됐다.
```
그런데 마지막 라인은 우리가 어떤 경우에 실행할 필요가 있을까? 불행히도 답은 그때그때 다르다.

원래의 생성자를 다시 할당하는 것이 중요한 역할을하게되며, 사용되지 않는 코드 라인이 추가되는 경우 를 정의해봅시다.

다음 예제를 보자: 오브젝트가 자기 자신을 만들기 위한 create메서드를 가지도록 함
```javascript
function Parent() {};
function CreatedConstructor() {};

CreatedConstructor.prototype = Object.create(Parent.prototype);
// Parent.prototype을 원형으로 새 오브젝트를 만들어, CreatedConstructor.prototype에 할당함
// 이 때 CreatedConstructor.prototype.constructor 가 Parent함수를 가리키게 됨

CreatedConstructor.prototype.create = function create() {
    return new this.constructor();     // this의 생성자를 가져와 새 인스턴스를 만들어 리턴
}
// CreatedConstructor 의 인스턴스는 이 create메서드를 상속받음

new CreatedConstructor().create().create(); // TypeError
// CreatedConstructor() 는 undefined 리턴함.
// 그리고 CreatedConstructor.create() 라고 해도 이 생성자 자체에는 create라는 메서드가 없음.
// (Object.create() 는 상속되는 메서드도 아님)
// new CreatedConstructor(); 로 만든 인스턴스에는 위에 정의한 .create()을 사용할 수 있음
```
위 예제에서 constructor 가 Parent에 연결돼있기 때문에 exception이 보여지게 된다.

이걸 피하기 위해 우리가 사용하고자 하는 필요한 생성자를 할당해주기만 하면 된다.
```javascript
function Parent() {}; 
function CreatedConstructor() {} 

CreatedConstructor.prototype = Object.create(Parent.prototype); 
CreatedConstructor.prototype.constructor = CreatedConstructor; // set right constructor for further using
// 이 줄만 추가하면 된다. CreatedConstructor 의 인스턴스는 이제 constructor 속성을 갖게됐고, 값은CreatedConstructor 생성자 함수다.
// 즉, 자신을 생성한 생성자 정보가 없었는데 만들어줌.

CreatedConstructor.prototype.create = function create() { 
    return new this.constructor();
} 

new CreatedConstructor().create().create(); // it's pretty fine
// (new CreatedConstructor()).create().create() 로 먼저 실행된다.
// > (CreatedConstructor의 인스턴스 오브젝트).create().create()
// >> 위 결과는 또 (CreatedConstructor의 인스턴스 오브젝트)를 만들고, .create()하면 같은게 또 만들어진다.
// this.constructor() 로 새 인스턴스를 만드는 메서드니까, 점 앞에 있는 오브젝트의 생성자를 계속 이용한다.
// 이제는 위처럼 .create()를 몇 번을 붙이든 에러 없이 CreatedConstructor의 인스턴스를 만든다.

// 이전 예제에서는 
// CreatedConstructor.prototype = Object.create(Parent.prototype); 했을때
// CreatedConstructor.prototype.__proto__ 만 존재하고 내용은 오브젝트{ constructor: Parent , __proto__: 네이티브Object }
// CreatedConstructor.prototype.constructor = CreatedConstructor; 함으로써
// CreatedConstructor.prototype.constructor도 추가되며, 내용은 CreatedConstructor(생성자함수)다.
```
(실행해보면 새로만든 인스턴스는 원형인 __proto__는 Parent 를 갖고있지만, constructor는 CreatedConstructor 를 가리킨다. 그리고 create 메서드도 상속받았다.)

좋아, 이제 constructor를 변경하는게 왜 유용한지 아주 명확해졌다.  (..?)

한 케이스를 더 보자.
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
    var startPosition = this.constructor.getStartPosition(); // error undefined is not a function
    // constructor가 Child라서 ParentWithStatic의 getStartPosition 메서드를 불러올 수 없다.
    // 만약 getStartPosition 메서드가 정적 메서드가 아니고 상속되는 메서드였다면 에러가 없었을 것

    return {
        offsetX: startPosition.x - position.x,
        offsetY: startPosition.y - position.y
    }
};
```
이 예제에서는 parent constructor를 변경하지 않아야 올바르게 동작한다.

**종합하면**: constructor를 수동으로 업데이트 하거나 설정하는 것은 다른 결과 혹은 때때로 혼란스러운 결과를 초래한다. 이를 방지하기 위해서는 각 특정 케이스마다 constructor의 역할을 정의하기만 하면 된다. 대부분 케이스에서 constructor는 사용되지 않고, 재할당이 불필요하다.







# Object.prototype

[MDN : Object.prototype 객체](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)

`Object.prototype` 속성은 Object인 원형 오브젝트를 나타낸다.
- Writable : no
- Enumerable : no
- Configurable : no

## Description
자바스크립트의 거의 모든 오브젝트들은 `Object`(생성자함수)의 인스턴스다. (Object는 `Object.prototype`으로부터 속성(, 메서드)을 상속하는 대표 오브젝트다. 그 속성들이 shadowed(a.k.a overridden)되었더라도 상속한다.)  그러나, `Object` 는 고의로 true가 아니게 만들어지거나(Object.create(null) 이용), 또는 더이상 true가 아니도록 변경될 수 있다.(Object.setPrototypeOf 이용)

원형 오브젝트인 `Object`에 가해진 변경사항은 그 변경되 속성과 메서드가 prototype chain을 따라 나중에 재정의(override)되지 않는 한, prototype chaining을 통해 모든 오브젝트에 반영된다. 오브젝트의 동작을 재정의(override)하거나 확장하는, 매우 강력하지만 잠재적으로 위험한 메커니즘을 제공한다.

## Properties

`Object.prototype.constructor`  
  오브젝트의 원형을 만드는 함수를 지정한다

`Object.prototype.__proto__`  
  오브젝트가 instantiate될 때 사용됐던 원형 오브젝트를 가리킨다  
  (즉, 인스턴스를 만들 때 사용된 원형 오브젝트. Object.create(원형) 이면 인자로 넣은 오브젝트를 가리킴)

이어서 
# Object.prototype.__proto__
[MDN : Object.prototype.__proto__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)


`Object.prototype`의 `__proto__` 속성은 accessor 속성(getter와 setter 함수)이다.
오브젝트의 internal `[[Prototype]]`을 드러낸다. (오브젝트거나 null이다)

`__proto__`의 사용은 논란의 여지가 있고 사용을 추천하지 않는다. (discouraged). EcmaScript 언어 스펙에 절대 포함된 적이 없는데 모던 브라우저들이 어쨌든 시행하기로 결정한 것이다. 최근 들어서야, `__proto__`속성은 브라우저의 호환성 확립을 위한 ECMAScript 2015 랭귀지 스펙에 표준화되었고 미래에 지원될 것이다.

...

생성시 [[Prototype]] 오브젝트를 설정하기 위해 `__proto__`속성을 오브젝트 리터럴 정의 안에서도 사용할 수 있다.(Object.create() 대신에)  object initializer / literal syntax 항목을 보시오.

### object initializer / literal syntax 중 관련 내용
Prototype mutation
`__proto__: value` 또는 `"__proto__": value` 같은 형식의 속성 정의는 `__proto__`라는 이름의 속성을 만들지 않는다. 대신, value로 오브젝트나 `null`이 주어지면, 만들어진 오브젝트의 `[[Prototype]]`을 그 값으로 변경한다. (value가 오브젝트나 null이 아니면 변경되지 않는다)
```javascript
var obj1 = {};
assert(Object.getPrototypeOf(obj1) === Object.prototype);

var obj2 = {__proto__: null};
assert(Object.getPrototypeOf(obj2) === null);

var protoObj = {};
var obj3 = {'__proto__': protoObj};
assert(Object.getPrototypeOf(obj3) === protoObj);

var obj4 = {__proto__: 'not an object or null'};
assert(Object.getPrototypeOf(obj4) === Object.prototype);
assert(!obj4.hasOwnProperty('__proto__'));
```
(위 모든 항목은 true임)

오브젝트 리터럴 안에서는 단 하나의 prototype mutation만 허용된다: 여러개의 mutation은 syntax error다.

콜론(:)을 사용하지 않은 속성 정의는 prototype mutation이 아니다: 다른 이름을 사용한 유사한 정의와 동일하게 동작하는 속성 정의다. (??..)
```javascript
var __proto__ = 'variable';

var obj1 = {__proto__};
assert(Object.getPrototypeOf(obj1) === Object.prototype);
assert(obj1.hasOwnProperty('__proto__'));
assert(obj1.__proto__ === 'variable');

var obj2 = {__proto__() { return 'hello'; }};
assert(obj2.__proto__() === 'hello');

var obj3 = {['__prot' + 'o__']: 17};
assert(obj3.__proto__ === 17);
```
(위 항목은 모두 true임)

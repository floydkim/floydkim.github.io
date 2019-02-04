prototype을 이해하기 위해 MDN의 `Object.prototype`, `Object.prototype.__proto__` 항목에 대해 전부 혹은 일부를 보며 번역본을 남겼습니다.  
(이 글은 함수 오브젝트와 prototype 객체와 \_\_proto\_\_ 속성의 관계에 대해서 은근하게 다루고 있습니다. 시각화 된 관계도를 원하신다면 죄송합니다!)


시작하겠습니다.


# Object.prototype

[MDN : Object.prototype 객체](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)

`Object.prototype` 속성은 Object인 원형 오브젝트를 나타냅니다.
- Writable : no
- Enumerable : no
- Configurable : no

(역자 주: prototype 객체는 함수 오브젝트가 만들어질 때 같이 만들어지는 별개의 오브젝트입니다. 그리고 함수 오브젝트의 .prototype 속성은 이 객체를 가리켜서, 함수와 prototype 객체가 서로 연결 혹은 종속된듯한 느낌을 갖게 합니다.)

## Description
자바스크립트의 거의 모든 오브젝트들은 `Object`(생성자함수)의 인스턴스입니다. (Object는 `Object.prototype`으로부터 속성(, 메서드)을 상속하는 대표 오브젝트입니다. 그 속성들이 shadowed(a.k.a. overridden)되었더라도 상속합니다.) 그러나, `Object` 는 고의로 true가 아니게 만들어지거나(Object.create(null) 이용), 또는 더이상 true가 아니도록 변경될 수 있습니다.(Object.setPrototypeOf 이용)

원형 오브젝트인 `Object`에 가해진 변경사항은 그 변경된 속성과 메서드가 prototype chain을 따라 나중에 재정의(override)되지 않는 한, prototype chaining을 통해 모든 오브젝트에 반영됩니다. 오브젝트의 동작을 재정의하거나 확장하는, 매우 강력하지만 잠재적으로 위험한 메커니즘을 제공합니다.

## Properties

(역자 주: prototype 객체는 적어도 아래 두 속성은 꼭 가지고 있습니다.)

`Object.prototype.constructor`  
오브젝트의 원형을 만드는 함수를 지정합니다.
(역자 주: prototype 객체는 자신을 만들어지게 한 함수를 constructor의 값으로 가리킵니다.)

`Object.prototype.__proto__`  
오브젝트가 인스턴스화(instantiate)될 때 사용됐던 원형 오브젝트를 가리킵니다.  
(역자 주: 즉, 인스턴스를 만들 때 사용된 prototype 객체를 가리킵니다. 예를 들어 Object.create(*원형*)으로 인스턴스를 만들었다면, \_\_proto\_\_는 인자로 넣은 오브젝트를 가리키게 됩니다.)

(역자 주 2: 모든 오브젝트는 이 \_\_proto\_\_ 속성을 갖습니다. prototype 객체 또한 오브젝트이므로 이 속성을 갖고 있으며, prototype 객체의 경우 Object.prototype 객체를 값으로 가리킵니다.)




(이어서 \_\_proto\_\_에 대해 알아보겠습니다.)
# Object.prototype.__proto__
[MDN : Object.prototype.__proto__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)


`Object.prototype`의 `__proto__` 속성은 accessor 속성(getter와 setter 함수)입니다.
오브젝트의 internal `[[Prototype]]`을 드러냅니다. (그 값은 오브젝트거나 null입니다.)

`__proto__`의 사용은 논란의 여지가 있고 사용을 추천하지 않습니다(discouraged). EcmaScript 언어 스펙에 절대 포함된 적이 없는데 모던 브라우저들이 어쨌든 시행하기로 결정한 것입니다. 최근 들어서야, `__proto__`속성은 브라우저의 호환성 확립을 위한 ECMAScript 2015 language 스펙에 표준화되었고 미래에 지원될 것입니다.

...(생략)

생성시 `[[Prototype]]` 오브젝트를 설정하기 위해 Object.create() 대신 `__proto__`속성을 오브젝트 리터럴 정의 안에서도 사용할 수 있습니다. 관련해서 object initializer / literal syntax 항목을 보십시오.

(역자: 해당 항목으로 잠깐 건너가보겠습니다.)
### object initializer / literal syntax 중 관련 내용
Prototype mutation  
`__proto__: value` 또는 `"__proto__": value` 같은 형식의 속성 정의는 `__proto__`라는 이름의 속성을 만들지 않습니다. 대신, value로 오브젝트나 `null`이 주어지면, 만들어진 오브젝트의 `[[Prototype]]`을 그 값으로 변경합니다. (value가 오브젝트나 null이 아니면 변경되지 않습니다.)
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
(역자 주: 위 모든 항목은 true입니다.)

오브젝트 리터럴 안에서는 단 하나의 prototype mutation만 허용됩니다: 여러개의 mutation은 syntax error입니다.

콜론(:)을 사용하지 않은 속성 정의는 prototype mutation이 아닙니다: 다른 이름을 사용한 유사한 정의와 동일하게 동작하는 속성 정의입니다. (역자 주: mutation이 아닌, \_\_proto\_\_이름을 갖는 새로운 속성을 만든다는 뜻으로 이해됩니다.)
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
(역자 주: 위 항목은 모두 true입니다.)


다시 Object.prototype.\_\_proto\_\_로 돌아오겠습니다.
## Description
`__proto__` getter 함수는 오브젝트의 `[[Prototype]]`이라는 내부적인 속성의 값을 드러냅니다. 오브젝트 리터럴을 사용해 만들어진 오브젝트의 경우 이 값은 Object.prototype이 됩니다. 어레이 리터럴을 이용해 만들어진 오브젝트의 경우엔 Array.prototype이 그 값이 됩니다. 함수에 경우, 이 값은 Function.protototype이 됩니다. new fun(fun은 자바스크립트의 기본 제공 빌트인 생성자 함수라고 하겠습니다. 즉 Array, Boolean, Date, Number, Object, String 등등을 말하며 자바스크립트가 진화하며 추가된 새로운 생성자들도 포함합니다.)을 이용해 만들어진 오브젝트의 경우, 이 값은 항상 fun.prototype이 됩니다. fun이 스크립트에 새로이 정의한 함수일 때 new fun을 이용해 만들어진 오브젝트의 경우에는, 이 값은 fun.prototype의 값이 됩니다. (즉, 생성자가 명시적으로 어떤 오브젝트를 리턴하지 않았다면, 혹은 인스턴스가 만들어지고 나서 fun.prototype이 재할당 되었다면.) (역자 주: (new func()).\_\_proto\_\_ === func.prototype 가 true인 것은 func가 새로 정의한 경우든, 기본 빌트인 생성자인 경우든 똑같습니다. 다만 새로 정의한 함수의 경우에는 함수 선언시 어떤 오브젝트를 리턴하도록 명시가 가능하고, prototype속성이 가리키는 오브젝트도 재할당이 가능하기 때문에 경우를 나눠 한번 더 얘기한 것 같습니다.)

`__proto__` setter 함수는 오브젝트의 `[[Prototype]]`변경을 가능케 합니다. 해당 오브젝트는 반드시 확장 가능해야(extensible) 하며 `Object.isExtensible()`로 확인할 수 있습니다: 그렇지 않은 경우, TypeError가 발생합니다. 제공하는 값은 반드시 오브젝트이거나 null이어야 합니다. 다른 값의 경우 아무 일도 일어나지 않을 것입니다.

prototype이 어떻게 상속에 사용되는지 이해하려면 가이드 아티클인 [Inheritance and the prototype chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)을 보십시오.


`__proto__`속성은 Object.prototype에 있는 간단한 accessor 속성이며 getter와 setter 함수로 구성되어 있습니다. `__proto__`를 향한 어떤 속성 접근이 결국 Object.prototype을 참고하는 경우엔 이 속성을 찾을 수 있지만, Object.prototype을 참고하지 않는 접근의 경우엔 찾을 수 없습니다. 만약 Object.prototype을 참고하기 전에 어떤 다른 `__proto__` 속성이 발견되면 이 속성은 Object.prototype에서 찾은 속성을 숨길 것입니다. (역자 주: shadowing을 말하는 것 같습니다.)


(역자 주: 크롬 콘솔을 이용해 오브젝트들의 내용을 보다보면, \_\_proto\_\_를 통해 prototype객체와 연결되고, 체인을 형성하는 것을 알 수 있습니다. 그러나 실제로 이 체인을 연결하는 고리는 `[[Prototype]]`이라는 내부 속성이며, \_\_proto\_\_는 이 `[[Prototype]]`을 보거나 설정할 수 있게 해주는 장치일 뿐입니다.)
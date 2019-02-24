이전에 작성한 [mdn 번역 + 주석 포스트](../2019-01-30-prototype-객체와-__proto__)가 내용이 너무 길어서 prototype 이해에 집중할 수 있는 정리 포스팅을 작성하겠습니다.

# prototype?
우리가 prototype을 가장 먼저 접하는 예제는 아마 둘 중 하나일 것입니다.
 - 기본 내장 객체의 prototype 확장
 - 상속을 위해 생성자 함수의 .prototype 속성을 변경(속성/메서드 추가)

위 예제들에서 소개하는 방법만 알고 있어도 상속을 구현하는 데에는 문제가 없을 것 같습니다.

공부를 하고 나니 이런 deep한 동작을 알고있으면 코드를 실행하지 않고 결과를 예측하거나, '왜 이게 되지?' 라는 물음에 대답할 수 있는 부가적인 능력이 생길 것입니다. 그리고 간단한 문제를 복잡하게 생각하는 부작용도 생길 수 있습니다.

하지만 알아야겠죠?

우리가 말하는 prototype의 실체는 다음과 같습니다.
 - prototype object (.prototype 속성이 가리키는 object)
 - \_\_proto\_\_ (prototype chain의 link 역할)

(이 둘을 구성 요소로 해서 모든 객체가 만들어지고, 상속도 일어납니다.)

# 코드로 파악해봅시다
우선 생성자 함수에 상속할 속성/메서드를 추가하는 예제를 통해 이 숨겨진 실체를 눈으로 보며 파악해 보겠습니다.

설명을 위한 전체 코드는 다음과 같습니다.
```javascript
function Human() {};
Human.prototype.eye = 2;
var baby = new Human();
console.log(baby.eye); // 2
```
line by line으로 살펴보겠습니다.
```javascript
function Human() {}; // 생성자 함수를 정의합니다.
```
자바스크립트에서 함수는 오브젝트입니다. 그런데 함수는 특별히, 선언시에 두 개의 오브젝트가 만들어집니다.
 - Human `function object` : 선언한 함수
 - prototype `object` : 자동으로 만들어진 object

각각이 무엇으로 구성돼있는지 알면 좋습니다.
1. Human `function object`
 - `.prototype` property
 - `__proto__` property

2. prototype `object`
 - `.constructor` property
 - `__proto__` property



<!-- 내장 객체의 prototype 확장 예제를 살펴보겠습니다.
```javascript
Array.prototype.

``` -->





# 혼동할 수 있는 case : new 생성자 (construction/instantiation)
# Webpack
  
- JavaScript 파일들의 로딩 순서를 보장해줍니다.
- HTTP 요청을 각 파일마다 보내지 않고 모든 내용에 대해 한 번의 HTTP 요청만으로 얻어올 수 있도록 해줍니다.
- bundle.js 파일로 묶어줍니다.

[네이버 D2 블로그](https://d2.naver.com/helloworld/0239818)

# ES6 Modules

[StackOverflow의 정리글](https://stackoverflow.com/questions/36795819/when-should-i-use-curly-braces-for-es6-import/36796281#36796281)

## Named Export
```javascript
// file.js
export const A
```
이렇게 내보낸것은

```javascript
// otherFile.js
import { A } from "./file.js"
```
이렇게 받아옵니다.

이 경우 export로 내보내는 내용이 오브젝트가 되어, import시 destructuring assignment 문법(ES6)으로 A변수에 할당하는 것입니다. 따라서 export 하는 문서에서 **정한 이름을 그대로 가져와야** 합니다.

만약 이름을 newName으로 바꾸고 싶다면,
```javascript
// otherFile.js
import { A : newName } from "./file.js"
```
이렇게 하면 A 변수는 선언되지 않고, newName 변수에 할당하게 됩니다.


## Default Export
```javascript
// file.js
export default A
```
이렇게 내보낸것은

```javascript
// otherFile.js
import a from "./file.js"
```
이렇게 받아옵니다.
import 시 변수 이름은 export시의 이름과 **같을 필요는 없습니다.**

default export는 문서 내에 하나만 선언할 수 있고, named export는 여러개 선언할 수 있습니다.

만약
```javascript
// file.js
export default "default exported value"
export const someFunction = function() {}
export const something = 1
```
이렇게 내보낸다면

```javascript
// otherFile.js
import a, { someFunction, something } from "./file.js"
console.log(a, someFunction, something)
//=> "default exported value" function.. 1
```
이렇게 받아올 수 있습니다.



# React Virtual DOM

Virtual DOM을 Vanila 자바스크립트로 구현하며 이해해보자[How to write your own virtual DOM](https://medium.com/@deathmood/how-to-write-your-own-virtual-dom-ee74acc13060)
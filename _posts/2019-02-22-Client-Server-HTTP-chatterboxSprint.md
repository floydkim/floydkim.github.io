# Client - Server Model
(session 호용님)
서버 : 자원을 제공하는 존재 (요청에 대한 응답)

protocol이라는 약속, 규약에 따라 메시지를 교환한다.
API(Application Programming Interface)를 바탕으로 원격 서버에 요청(RPC; Remote Procedure Call)을 하고, 응답에 대해 적절한 형태로 화면에 표시한다.

클라이언트는 터미널이라고도 부름. (웹 브라우저, cli 터미널, 등등) (ftp 프로그램도 클라이언트겠네?)

1. request - responce pattern
  http의 패턴임. 요청을 해야 응답이 온다. (응답이 안올수도, 요청전달이안될수도있다)

  HTTP자체는 동기적임. 유청>응답>요청>응답.   물론 JS는 비동기니까 헷갈리지말자.


2. push technology (server push)
 서버에서 일방적으로 클라이언트에 전달함.
 클라이언트를 서버에 등록(subscribe)하는것이 선행되어야 함. (Publish-Subscribe)
 요청에의한게 아니라서 비동기적임.

 웹소켓-푸쉬구현.   http는 원래 요청응답 기반인데, 최근 버전(http3)에서 지원할 예정?이다.




# HTTP
(session 호용님)
슬라이드를 보는게 좋겠다.

  HTTP 스펙을 다는 못읽어도 발췌독이라도 해야한다.

-HTTP는 특정 상태를 담고있지 않음(stateless) ; 이전 요청이나 다음 요청을 기억하지 않음
-연결 상태를 유지시키지 않음 (connectionless)
-Request-Response Cycle


HTTP 2.0 에서는 여러 파일을 한꺼번에 전달하고, 각각에 대한 응답을 한꺼번에 받는다. (multiplexing ?)

GET POST PUT DELETE (DB의 CRUD와 매핑되니까 생각해보자 -> REST)
요청,제출,교체,삭제

post; 게시물작성, 로그인시 인증요청 등
put; 요청에 의한 서버에 변화가 없을때… 기존데이터를 교체하는용도로 쓸수있다고한다. 이해안됨

“HTTP Response status code”
200 : 성공
304 : 요청에 대한 응답이 수정되지 않음 (캐시된것 불러온 경우)
403 : 컨텐츠에 접근할 권한 없음
404 : 요청받은 리소스를 사용할 수 없음
500 : 서버가 처리할 수없는 요청

400번대는 요청이 잘못된경우 / 500번대는 응답이 잘못되는경우(서버에 버그가 있는경우)


RESTFUL API 는 다음에 설명.


CURL : cURL
  curl -vL http://codestates.com
  curl —head http://..
  curl -X GET "http://www…"

POSTMAN ; GUI로 POST메서드를 포함한 request와 response를 까볼수있다.




**chatterbox sprint**

- 서버와 자유롭게 통신할 수 있다
  - XMLHttpRequest (XHR)의 등장
- 페이지 깜빡임 없이 seamless하게 작동한다
  - Javascript와 DOM 이용

이 두 요구를 충족하는 기법. AJAX (Asynchronous Javascript And XML)

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://52.78.213.9:3000/messages');

// 요청의 상태 변화를 추적합니다
xhr.onreadystatechange = function(){
	if(xhr.readyState !== 4) return;
	// readyState 4: 완료

	if(xhr.status === 200) {
        // status 200: 성공
		console.log(xhr.responseText); // 서버로부터 온 응답
	} else {
		console.log('에러: ' + xhr.status); // 요청 도중 에러 발생
	}
}

xhr.send(); // 요청 전송
```

using jQuery
```javascript
$.get('http://52.78.213.9:3000/messages', function(response) {
  // response: 서버로부터 온 응답
});
```

fetch API
```javascript
fetch('http://52.78.213.9:3000/messages')
.then(function(response) {
  return response.json();
})
.then(function(json) {
  // json 형태로 전달받은 서버로부터의 응답
  console.log(son);
});
```

관련 resources
https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
https://jakearchibald.com/2015/thats-so-fetch/
https://hacks.mozilla.org/2015/03/this-api-is-so-fetching/
최신 기술이라고 fetch가 전부 좋은 것은 아니며, 여전히 XMLHttpRequest는 많이 쓰이는 기술이므로, fetch와 XMLHttpRequest의 차이점(https://stackoverflow.com/questions/35549547/what-is-the-difference-between-the-fetch-api-and-xmlhttprequest)을 확인한 후 사용하는 것이 좋음
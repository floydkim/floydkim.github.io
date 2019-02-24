# Stack
 - LIFO (Last In First Out). 목록의 끝(top)에서만 모든 접근(in/out)이 일어납니다.
    - 자바스크립트에서 함수의 execution context를 쌓는 call stack에 사용되고 있습니다.
 - 데이터 추가를 push / 꺼내는것(삭제)을 pop 이라고 합니다.
 <div><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Lifo_stack.png/700px-Lifo_stack.png"><h6>이미지 출처: wikipedia</h6><br></div>

 - 스택이 비어있을 때 pop하면 stack underflow 에러가 나며, 꽉 차있을때 push를 하면 stack overflow 에러가 납니다.
     - 자바스크립트에서 이러한 기본적인 스택을 구현하려면 최대 사이즈를 정의하는 상수를 도입해야 할 것입니다.

스택 구조는 전통적인 Array로 구현할 수 있고, linked list를 사용해 구현할 수도 있습니다.
linked list로 구현시 스택의 size를 유동적으로 만들 수 있어서 overflow 에러를 걱정하지 않아도 되는 장점이 있습니다.

자바스크립트에서의 배열은 linked list로 구현되어 있어, C언어 처럼 미리 array size를 정해 메모리 공간을 확보하는 준비가 필요하지 않습니다.


# Queue
<div><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/52/Data_Queue.svg/600px-Data_Queue.svg.png"><h6>이미지 출처: wikipedia</h6><br></div>

 - FIFO (First In First Out). 목록의 한쪽 끝(front)에서 output, 나머지 한쪽(tail)에서 input이 일어납니다.
 - 선형(linear)과 환형(..)이 있습니다.
     - 선형 : 크기가 제한되어있습니다. 큐가 꽉 찬 상태에서, dequeue해서 앞이 비워진 다음, 그 빈 공간을 쓰려면 모든 내용을 한칸씩 옮겨줘야 입력가능한 빈 공간이 마련된다는 단점이 있습니다.
     - 환형 : 위에 언급한 선형 큐의 단점(배열이용시 큐 안에 빈공간이 있어도 입력단에 데이터가 차있으면 오버플로우가 발생함)을 해결한 형태입니다. front 위치에 데이터가 있으면 대신에 tail 위치에 데이터를 추가합니다.(front가 큐의 끝에 닿으면 큐의 맨 앞으로 자료를 보내어 원형으로 연결)

 - linked list로 큐를 구현하면 큐의 길이를 쉽게 늘릴 수 있고, 오버플로우가 발생하지 않습니다. 필요시 환형으로도 만들 수 있지만 그러지 않아도 삽입과 삭제가 제한되지 않아 편리합니다.

 - 입력된 시간 순서대로 처리할 필요가 있는 상황에 주로 이용됩니다. (자바스크립트의 task queue)

 - enqueue : tail이 가리키는 위치에 저장 / dequeue : head가 가리키는 위치를 지우고 한칸씩 옮김
 
 
# Linked List
<div><img src="https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1335/2939.png"><h6>이미지 출처: 생활 코딩</h6><br></div>

 - head : 리스트의 처음 노드를 가리키는 포인터 (리스트가 비어있으면 head 는 null)
 - 각 노드는 Data field 와 Link field 로 구성됨. 각각 데이터와 다음링크 주소를 담는다.
 
메서드 : 처음에 추가 / 특정노드 다음에 추가 / 특정 노드 삭제

[Linked List 대 Array List](http://www.nextree.co.kr/p6506/)
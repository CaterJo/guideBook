# 엄격모드(strict mode)
- 문법과 런타임 동작 모두 검사
- 실수를 에러로 변화
- 변수 사용 단순화
- eval, arguments의  단순화
- 가기 es버전에서 정의될 문법을 금지한다.

## 규칙
1. 선언하지 않은 변수를 사용할 수 없다.
2. 읽기 전용객체에 쓰는것이 불가능하다.
(Object.defineProperties()를 통해 읽기전용 속성을 만들 수 있다.)
3. get으로 선언된 객체는 수정할 수 없다.
4. extensible 특성이 false로 설정된 객체에 속성을 확장할 수 없다.(확장불가 객체)
	- (Object.preventExtension()을 통해 객체가 확장되는것을 막을 수 있다.)
5. configurable 속성이 false로 설정된 객체는 속성을 변경 및 삭제 할 수 없다.
 	- (Object.defineProperty)를 통해 속성을 부여할때 configurable 옵션을 통해 설정변경 가능 여부를 결정할 수 있다.
6. 함수에 동일한 매개 변수 이름을 선언하는것이 불가능하다
7. private values의 속성 설정이 불가능하다.
	- 엄격모드가 아닌경우 이런 코드는 무시된다.
8. with을 사용할 수 없다
9. eval함수는 주변 스코프에 새로운 변수를 추가하지 않는다.
	- 일반적으로 eval("var x;")는 변수 x를 주위 함수나 전역 스코프에 추가한다.
	- 즉 지역변수나 전역변수 x가 있더라도 참조에서 독립적인 값을 갖게 된다는 얘기다.
10. arguments를 변수 또는 함수, 매개변수의 이름으로 사용할 수 없다.
11. 매개변수를 함수내에서 임의로 수정하더라도 arguments의 값이 수정되지 않는다.
12. arguments.callee를 지원하지 않는다.
13. this값이 null 또는 undefined인 경우 전역객체(window)로 변환되지 않는다.
14. caller, callee를 통한 stack 검색이 불가능하다.
15. argumnets의 caller를 지원하지 않는다.
16. 예약된 키워드의 이름으로 변수, 함수를 만들 수 없다.

## 엄격모드에 주의사항
비-엄격모드(느슨모드)코드와 엄격모드 코드의 결합에 주의할것  
엄격모드 + 엄격모드는 괜찮지만 이 경우 구조적 충돌이 발생할 수 있다.  
이 경우 함수기준 엄격모드를 고려해라.  





## this


### 함수에서 사용
```js
function foo() {
  // 함수-레벨 strict mode 문법
  'use strict';
  console.log('function',this)
}
// function undefined
```

### 메서드에서 사용
```js
var obj ={
    say: function(){

    'use strict'

    console.log('Method',this)
    }
}
// Method {say: ƒ}
```

# Redux-saga Test

## intro

`redux-saga-test-plan`은 사가(Saga) 제너레이터 함수를 테스트할 때, 실제 구현 로직과 테스트 코드가 갖는 커플링, 그리고 매뉴얼한 테스트에 대한 문제를 해결해준다.  
테스트에 선언적이고, 체이닝(chainable) API를 제공해서 실제 구현체인 사가에서 원하는 이펙트만을 테스트할 수 있도록 도와준다. 이외 어떤 이펙트들을 나타내는지, 이펙트들의 순서는 어떻게 되는지 신경쓰지 않고 걱정하지 않도록 한다. redux-saga의 런타임을 함께 사용하므로, 통합 테스트를 할 수도 있고, redux-saga-test-plan에 내장된 이펙트 목킹(mocking)을 활용해 유닛 테스트도 작성할 수 있다.

- 실제 구현로직과 커플링을 제거한다.

## 사전 준비

- jest
- babel-jest
  - 테스트를 할 때 ES6문법을 사용하기 위해서 설치한다.

## redux-saga-test-plan의 특징

- expectSaga에서 오직 관심이 있는 이펙트만 테스트할 수 있다.
  매뉴얼하게 사가의 이펙트들을 모두 확인할 필요가 없고, 구현 로직과의 커플링을 없앨 수 있다.
- 선언적이고 체이닝이 가능한 API로 사가를 테스트하는데 간단한 준비만 하면 된다. 지금까지 본 다른 솔루션들은 명령형의 API와 더 많은 준비 단계가 필요했고, 특정 API만 테스트할 수 있었다
- 사가를 리듀서와 함께 테스트할 수 있다.
- 여러 레이어로 깊게 포크된 사가를 테스트할 수 있다.
- 내장된 목킹으로 정적/동적 providers를 사용할 수 있다.
- 사가가 특정 이펙트를 yield하지 않았는지로 확인할 수 있다.
  특정 type의 액션의 put 이펙트를 그 액션의 payload와 관계없이 테스트할 수 있다.

## 왜 saga는 테스트하기 쉬운가?

Saga는 기본적으로 비동기 액션을 모니터링하는 Watcher Saga와 실제로 비동기 작업을 수행하는 Worker Saga로 구성된다
그 중 Worker Saga는 yield 표현을 이용하여 비동기로 특정 객체를 반환한다.  
그리고 Saga들을 실제적으로 핸들링하여 Redux(Store)와 연결하는 Saga Middleware(이하 미들웨어)가 있다.

미들웨어는 (Worker) Saga가 반환하는 Promise나 단순 Object를 핸들링 할 수 있다.  
쉽게 말해 미들웨어 내부에서 프로미스를 처리하여 단순 객체를 반환하며 이 객체만 테스트하면된다.

```js
put({ type: "INCREMENT" }); // => { PUT: {type: 'INCREMENT'} }
call(delay, 1000); // => { CALL: {fn: delay, args: [1000]}}
```

각 이펙트가 반환하는 자바스크립트 객체의 형태가 일치하는 지 테스트하면 된다.

## 무엇을 테스트하나?

## redux-saga-test-plan

```js
import { testSaga } from "redux-saga-test-plan";

import { fetchSudokuSaga } from "sagas/sudoku.saga";
import { getBoard } from "services/sudoku.service";

describe("Sudoku Saga test", () => {
  test("Should fetch sudoku board well", () => {
    let saga = testSaga(fetchSudokuSaga, {
      type: "sudoku/GET_BOARD",
      payload: "easy",
    });
    saga.next().call(getBoard, "easy");
    saga.next();
    saga.next().isDone();
  });

  test("Should throw error", () => {
    let saga = testSaga(fetchSudokuSaga, {
      type: "sudoku/GET_BOARD",
      payload: "easy",
    });
    saga.next().call(getBoard, "easy");
    saga.throw(new Error("test"));
    saga.next().isDone();
  });
});
```

## redux-saga를 reducer와 함께 테스트하기

```js
import { put } from "redux-saga/effects";
import { expectSaga } from "redux-saga-test-plan";

const initialDog = {
  name: "Tucker",
  age: 11,
};

function reducer(state = initialDog, action) {
  if (action.type === "HAVE_BIRTHDAY") {
    return {
      ...state,
      age: state.age + 1,
    };
  }

  return state;
}

function* saga() {
  yield put({ type: "HAVE_BIRTHDAY" });
}

it("handles reducers and store state", () => {
  return expectSaga(saga)
    .withReducer(reducer)

    .hasFinalState({
      name: "Tucker",
      age: 12, // <-- age changes in store state
    })

    .run();
});
```

### redux-saga-test-plan의 장점

- 리듀서와 함께 테스트할 수 있다.
- 내장된 목킹함수를 제공한다.
- 해당 이펙트가 정상적으로 실행되는지 테스트한다.
- 확인하고 싶은 이펙트만 선택적으로 테스트가 가능하다

## ref

- [docs-Testing](https://redux-saga.js.org/docs/advanced/Testing.html)
- [gitbook-redux-saga-test-plan](http://redux-saga-test-plan.jeremyfairbank.com/)
- [tool없이 saga테스트하기](https://nukeguys.github.io/dev/redux-saga-test/)
- [Redux-saga 테스트 코드 작성하기](https://medium.com/@sangboaklee/redux-saga-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0-1fc13f7fd279)
- [git-redux-saga-test-plan](https://github.com/jfairbank/redux-saga-test-plan)
- [redux-saga-test-plan-tutorials](https://ui.toast.com/weekly-pick/ko_20180514/)
- [Redux-Saga](https://www.vobour.com/00-redux-saga-)
- [testing-3-react-testing](https://jbee.io/react/testing-3-react-testing/)
- [evaluating-redux-saga-test-libraries](https://blog.scottlogic.com/2018/01/16/evaluating-redux-saga-test-libraries.html)
- [redux-saga-test-plan](https://ui.toast.com/weekly-pick/ko_20180514/)
- [왜 saga는 테스트하기 쉬운가?](https://mskims.github.io/redux-saga-in-korean/introduction/BeginnerTutorial.html)

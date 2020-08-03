# React에서 상태관리 하기

---

## 상태

상태(state)란, **변할 수 있는 것**을 의미. 데이터로 바꿔 말할 수도 있다.

원래 react에서는 상태가 변하면 UI가 변한다. (상위 컴포넌트의 state가 변하면, 그 아래 포함된 컴포넌트를 포함해 전부 rerendering)

프로젝트가 복잡해지면서 여러 개의 상태가 여러 개의 view를 바꾸게 된다.

전역 상태가 필요해지고, 상태 관리 로직을 따로 분리하고자 하는 필요성이 생긴다.

---

## context API

일일이 prop을 넘겨주지 않아도 컴포넌트 트리 전체에 데이터 전달이 가능하다.

(cf. 횡적 관심사는 Higher-order Component(HOC)로...)

react 16 버전부터 사용 가능해졌기 때문에, 그 전에 만들어진 프로젝트에서는 context API를 활용할 수 없었다.

---

단순히 prop을 넘겨주는 목적만 가지고 있다면, redux를 사용할 필요 없이 context API만을 사용할 수도 있다.

다만, 프로젝트의 규모가 커서 상태관리 로직을 분리해야 하는 필요성이 있거나, 비동기 작업이 많을 경우에는 redux와 같은 상태관리 라이브러리를 쓰는 쪽이 낫다.

---

## flux와 redux

react에서의 복잡한 상태를 관리하기 위해, flux라는 디자인 패턴이 생겨났다.

여러 불편한 점을 해소하고자 flux에 사용할 수 있는 생태계가 조성되기 시작했다.

Dan Abramov도 그런 일환으로 아래와 같은 기능을 만들어 발표했다.

- hot loader: flux에서는 코드 수정 후 새로고침을 하지 않으면 상태끼리 충돌이 나는데, 입력된 상태를 그대로 두고 코드를 수정할 수 있도록 하는 기능
- time travel debugging: 상태 변경의 redo/undo 가 가능

---

이후 `reducer`라는 개념을 가져와서, flux를 대체하는 redux를 발표.

- flux는 store가 여러 개이고, callback 함수로 store를 업데이트 한다.
- redux는 store가 1개이고, store를 변경하는 reducer를 여러 개 둔다. 이때, reducer 간에 합성이 가능해서 공통로직을 재사용하기가 좋다.

---

- flux에 비해, reducer는 개발자의 편의성이 향상 되었다. (hot loader, time travel debugging...)
- flux에 비해, 미들웨어 생태계가 구축 되었다. (redux-thunk, redux-saga, 로깅...)

redux의 미들웨어를 사용하면 아래와 같은 동작이 가능하다.

- 특정 조건에 따라 action을 무시하도록 할 수 있다
- action을 로깅할 수 있다
- dispatch된 action을 수정해서, reducer에 전달할 수 있다
- 특정 action으로 다른 action을 발생시킬 수 있다
- 특정 action으로 특정 자바스크립트를 실행시킬 수 있다
- action을 모니터링 할 수 있다

---

## redux-thunk & redux-saga

(간단하게만 정리했는데, 코드로 보여드릴 수 있도록 조만간 다시 정리해 보겠습니다.)

---

### redux-thunk

원래 redux에서는 action 객체를 dispatch 함으로써 단순한 동기 작업만 가능하다.

redux-thunk는 비동기 처리를 위해서 인자로 dispatch 함수를 받아서, action 객체가 아니라 함수를 dispatch 한다.

특정 작업을 나중에 하도록 함수 형태로 함수를 감싼다.

---

### redux-saga

redux-saga는 Generator 함수 * 로 구성되어 있으며, 까다로운 비동기 작업을 조금 더 쉽게 하도록 도와준다.

Generator: 순차적으로 값을 반환한다. `function* (){}` 형태로 쓴다.

action을 모니터링 하고 있다가, 특정 action이 발생하면, 특정 작업 (함수 실행, 다른 action을 dispatch, 현재 상태를 return...) 을 수행할 수 있다. 

redux-thunk에서는 action에 응답을 줄 수 없다.

그리고 기존 요청을 취소하거나, 요청이 실패했을 때 재요청을 하는 것도 간편하다.

---

## 우리 프로젝트에 redux를 적용하는 게 좋을까? 그리고, 적용할 수 있을까?

(이것도 간단하게만 정리해 봤습니다.)

---

### 바꿔야 하는 이유

- flux는 더이상 업데이트가 없고, 생태계도 발전이 없다.
- 프론트엔드 개발자 풀을 생각하면, flux보다 redux에 익숙한 사람이 훨씬 많을 것이다.
- redux에는 개발자가 편할 수 있는 기능이 많다.
- redux의 미들웨어를 사용하면, 현재 프로젝트에서 꼬여 있는 비동기 처리를 해결할 수도 있다.
- react가 hook으로 업데이트 되면서, redux를 편하게 쓸 수 있는 redux hook이 생겼다. 새로 작성하는 코드가 hook으로 쓰여진다면, 코드량을 획기적으로 줄이고 이해하기 쉬운 코드를 쓸 수 있다.

---

### 바꾸지 말아야 하는 이유

- flux나 redux 간에 엄청난 차이가 있는 것은 아니다. redux를 이해한다면 flux도 쉽게 이해할 수 있다.
- 잘 돌아가는 코드를 바꾸는 것은 비효율적인 일일 수 있다.
- 그럼에도 flux를 redux로 갈아치우려면 굉장히 오랜 시간이 필요하고, 당분간은 코드 중복이 필연적으로 발생한다.

---

### 만약 바꾼다면

- store 단위를 reducer 단위로 생각하여 store를 한 개씩 redux로 대체한다. 이때, store 간에 독립되지 않고 영향을 주고 받는 부분이 있다면 풀어낸다.
- 현재 store는 기능 단위와 데이터 단위가 섞여 있다. 이를 전부 기능 단위로 분리해서 재사용 가능한 reducer를 뽑아 낸다. 이때, 파일 작성 방식은 ducks 패턴 (관련된 reducer와 action 등이 한 파일에 있다) 을 적용한다.
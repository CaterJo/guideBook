# Apollo

# 특징

1. 선언적 데이터 조회
React가 렌더링 될 때 데이터를 로드하고 쿼리에서 사용하는 변수가 바뀌면 다시 로드함
데이터, 로딩, 에러를 상태로 관리 -> React hook과 잘 어울림
2. 개발환경
타입스크립트 지원, 크롬 개발자 도구 지원, VSCode 플러그인 지원
3. 캐시
타입별 ID 기반 캐시 사용 (데이터에 식별자가 꼭 있어야 함)
페이지가 렌더링 되면 캐시에 있는 데이터를 먼저 로드한 후 네트워크로 조회한 데이터로 갱신
FetchPolicy에서 캐시와 네트워크 설정을 할 수 있음
4. 로컬 상태 관리
로컬 상태를 GraphQL로 관리
Redux, MobX 대체
5. SSR 지원
컴포넌트에서 사용할 쿼리를 뽑아 호출하고 페이지 렌더링 할 때 로컬 캐시로 제공
SSR을 적용할 쿼리와 아닌 쿼리 구분 가능
6. Batch 기능
여러 번 나눠서 호출하는 쿼리를 하나로 합쳐줌
요청이 최적화되는 장점이 있지만, 가장 느린 쿼리가 응답할 때까지 기다린다는 단점이 있음
TIP - SSR에서는 batch를 사용하고 CSR에서는 batch를 끔
7.  PersistedQueries 기능
GraphQL Query의 특징은 단일 엔드포인트에 POST방식으로 필요한 거대한 쿼리를 매번 요청함
이 방식은 유연하고 편리하지만 캐시와 오버헤드가 문제 - POST는 네트워크 캐시를 사용할 수 없고 거대한 쿼리는 매번 오버헤드임
PersistedQueries는 요청을 hash key로 치환하고 GET 방식을 사용. 캐시 사용 가능하고 거대한 쿼리를 사용하지 않아도 됨 - 서버 지원 필요
8. Mock 지원
GraphQL은 요청하는 사람이 응답할 데이터를 정의할 수 있다는 특징 때문에 mock을 작성하는 것도 굉장히 유연함
필요한 데이터를 mock 데이터로 미리 만들어 작업하고 백엔드 개발이 완료되면 타입스크립트의 리팩토링 기능으로 변수명을 맞춰주면 됨
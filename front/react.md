# React

## React의 렌더링

### :one: 초기 렌더링 (_Mounting_)

-   사용자가 애플리케이션에 진입시 DOM에 나타날 때 1번 렌더링
-   함수형 컴포넌트는 함수가 실행, 반환된 JSX가 DOM에 그려짐.

:gear: 트리거

-   `<Component />`가 처음 사용될 때.

### :two: 리렌더링 (_Re-rendering_)

이미 마운트된 컴포넌트가 다시 렌더링되는 것을 의미. 여러가지 조건들이 존재

#### Ⅰ. 상태(_state_)가 변경될 때

-   `useState`, `useReducer`등을 통해 관리되는 값이 변경되면 해당 컴포넌트 리렌더링

-   판단 기준: 얕은 비교(_shallow comparison_)

```js
const [count, setCount] = useState(0)
setCount(1)

// 이전 값과 다르면 리렌더리 발생
```

#### Ⅱ. props가 변경될 때

-   부모 컴포넌트에서 전달받은 props가 변경될 시 → 해당하는 자식 컴포넌트는 리렌더링

-   판단 기준: 얕은 비교(_shallow comparison_)

```js
<ChildComponent value={someValue} />

// someValue 가 바뀌면 ChildrenComponent => 리렌더링
```

#### Ⅲ. 부모 컴포넌트가 리렌더링 될 때

-   부모 컴포넌트의 리렌더링 => 자식 컴포넌트의 리렌더링
-   단, 자식 컴포넌트가 `React.memo`로 감싸져 있으며, props가 동일하다면 리렌더링 방지

#### Ⅳ. context 변경 시(_useContext_)

-   _context_ 값이 변경될 때 해당 context를 사용하는 모든 컴포넌트 **리렌더링**
-   단, 사용하지 않는 컴포넌트는 영향을 주지 않음

#### Ⅴ. forceUpdate 호출 시 (클래스 컴포넌트)

-   **강제로 리렌더링** 발생
-   현재는 함수형 컴포넌트와 훅 중심으로 바뀌어서 많이 쓰이지 않음 (`useState`, `useReducer`, `useRef`)

#### Ⅵ. Key의 값이 변경될 때

-   리스트 렌더링 시 _key_ 값이 변경될 때 해당 컴포넌트 재생성
-   :bulb: 리렌더링이 아닌 **언마운트 + 마운트** 발생

### :three: 리렌더링이 발생하지 않는 경우

-   상태 `or` props가 동일한 경우 (얕은 비교 판단이 true)
-   `React.memo` or `PureComponent`로 감싸고, props가 같은 경우
-   `shouldComponentUpdate` or `React.memo`의 비교 함수(_areEqual_)로 **리렌더링 조건 제어 가능**

### :four: 다양한 리렌더링 최적화 방법

-   `React.memo`
-   `useMemo`, `useCallback`

    -   `useMemo`:
    -   `useCallback`:

    | 잘 쓰면 효과적인 상황                       | 과하게 쓰면 역효과 생기는 경우                  |
    | ------------------------------------------- | ----------------------------------------------- |
    | 자식 컴포넌트가 React.memo로 감싸져 있을 때 | 함수 내부가 가볍고 자식 리렌더링이 상관 없을 때 |
    | useEffect, useMemo 의존성 배열에 들어갈 때  | 의존성이 많아서 계속 재생성되는 경우            |
    | 같은 함수 참조 유지가 성능에 영향 있을 때   | 캐싱된 함수가 별 의미 없을 때                   |

-   `useTransition`, `useDeferredValue` (React 18 이상)
-   `virtualization` (e.g. react-window, react-virtualized)

### :five: 주의사항 :rotating_light:

-   **상태** or **props**가 바뀌면 **_무조건 렌더링 함수 실행_**
-   **But**, 실제 DOM은 변화가 있어야만 업데이트, (React는 Virtual DOM에서 변화 감지후 실제 DOM에 반영: _Reconciliation_)
-   리렌더링 ≠ DOM 업데이트 (리렌더링이 발생하여도 DOM은 바뀌지 않을 수 있다.)

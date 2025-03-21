# web page rendering

## 개요

부트캠프 과정으로 React를 배우게 되었는데, React의 Single Page Application이 리액트의 중심이란건 배웠지만,

그 외에 어떤것들이 있는지 무엇이 다르길래 SPA방식의 라이브러리 또는 프레임워크가 떠오른 것인지

그리고 최근에 떠오른는 기술들은 무엇이 있는지 알아보고자 한다.

## 목차

### 1. [웹 페이지 렌더링이란?](#1-웹페이지-렌더링이란-what-is-a-web-page-rendering)

### 2. [웹 페이지의 렌더링 과정](#2-웹페이지의-렌더링-과정-web-page-rendering-process)

### 3. [React의 렌더링 프로세스](#3-react의-렌더링-프로세스-react-render-process)

### 4. [그 외의 다양한 렌더링 프로세스]()

### 5. [정리]()

##

## [1. 웹 페이지 렌더링이란? (What is a Web Page Rendering)](#1-웹-페이지-렌더링이란)

**정의**: 사용자의 브라우저에서 HTML, CSS, JS 등을 해석해 화면에 보여주는 과정

**목표**: 빠르게, 정확하게, 부드럽게 UI 출력

**💡 관점**

-   사용자 체감 속도 (퍼포먼스)
-   검색 엔진 최적화 (SEO)
-   개발 효율성 (코드 유지보수)

## [2. 웹 페이지의 렌더링 과정 (Web Page Rendering process)](#2-웹-페이지의-렌더링-과정)

### 전체 흐름도

1.  `network`를 통해 `(HTML, CSS, js)수신` → [`(DOM, CSSOM)`](#1-html-css를-통해-dom-cssom-생성) 생성
2.  [`Render Tree`](#2-render-tree-생성)
3.  [`Layout`](#3-layout-생성)
4.  [`Paintion`](#4-painting-작업)

![](./md_image/2025-03-19-22-30-19.png)

### 1. HTML, CSS를 통해 DOM, CSSOM 생성

> <details>
>
> <summary> $\textsf{\color{magenta}{< 본문 >}}$ </summary>
>
> ![](./md_image/2025-03-19-22-37-21.png) > **DOM**
>
> -   HTML을 브라우저가 해석하기 편하게 변환한 객체 트리
> -   HTML로 작성한 요소들의 배치와 모양을 기술한 모든 정보 존재
>
> **CSSOM**
>
> -   CSS로 작성해놓은 요소들의 스타일 관련된 모든 정보 존재
>
> </details>

### 2. Render Tree 생성

> <details>
>
> <summary> $\textsf{\color{magenta}{< 본문 >}}$ </summary>
>
> ![](./md_image/2025-03-19-22-39-19.png)
>
> -   **`DOM`** & **`CSSOM`** 을 합쳐서 렌더트리 생성
> -   **웹페이지의 청사진** 이라고도 불림 (배치와 모양 스타일 모두 존재)
>
> </details>

### 3. Layout 생성

> <details>
>
> <summary> $\textsf{\color{magenta}{< 본문 >}}$ </summary>
>
> ![](./md_image/2025-03-19-22-42-03.png)
>
> -   **`Render Tree`** 기반으로 실제 웹 페이지에 요소들의 배치를 결정하는 작업
>
> </details>

### 4. Painting 작업

> <details>
>
> <summary> $\textsf{\color{magenta}{< 본문 >}}$ </summary>
>
> ![](./md_image/2025-03-19-22-42-56.png)
>
> -   실제로 요소들을 화면에 그려내는 과정
>
> </details>

### 5. javascript를 통한 DOM 업데이트

> <details>
>
> <summary> $\textsf{\color{magenta}{< 본문 >}}$ </summary>
>
> ![](./md_image/2025-03-19-22-44-59.png)
>
> -   `javascript`를 통해 `DOM` 업데이트는 `Critical Rendering Path`를 다시 실행함
> -   각각 `Reflow`, `Repaint`라는 특별한 이름을 지을 정도로 `Layout`과 `painting` 작업은 연산이 많이 필요한 시간이 걸림
> -   그래서 페이지를 업데이트 할 때 DOM의 수정을 최소화 해야함
> -   그러나 서비스의 규모가 커질수록 이는 힘든 일
> -   여기서 React의 Single Page Application (SPA)이 획기적인 성능을 보여주게 되어 프론트의 대세가 됌
>
> </details>

## [3. React의 렌더링 프로세스 (React render process)](#3-react의-렌더링-프로세스)

### 리액트의 렌더링 과정

:one: **렌더링을 유발하는 단계**

-   `createRoot` 실행 or `state`의 업데이트

:two: **렌더링으로 컴포넌트를 호출하는 단계**

-   **가상DOM**을 통한 **재조정** (_Reconciliation_)이 일어나는 단계 :bulb:
-   `createRoot`로 렌더링이 발생\
    　 → 루트 컴포넌트 호출
-   `state`의 업데이트로 렌더링 발생\
    　 → 해당 `state`가 속한 컴포넌트를 호출

:three: **커밋 단계**

-   실제 `DOM`에 적용하는 작업
-   첫번째 커밋인 경우:\
    　 → apeendChild를 사용하여 스크린에 있는 모든 노드 생성
-   첫번째 커밋이 아닌 경우:\
    　 → 최소한의 작업을 통해 변경사항만을 실제 DOM에 적용\
    　 → **변경사항은 렌더링 중에 계산** :star:\

- :pushpin: 1, 2단계를 합쳐서 **Render Phase** / 3단계를 **Commit Phase**로 부름
    - **Render Phase**: 컴포넌트 계산 및 업데이트 사항 파악
    - **Commit Phase**: 변경사항을 DOM에 반영


#### 리액트의 렌더링이 일어나는 시점

:one: **최초 렌더링**

-   사용자가 처음 어플리케이션에 들어올 때, 화면을 보여주기 위해 최초 렌더링을 실행

:two: **리렌더링**

-   처음 어플리케이션에 들어온 후 최초 렌더링이 발생한 이후 발생하는 **모든 렌더링**

:three: **함수형 컴포넌트의 `useState`의 상태변화**

-   state의 변화 = **컴포넌트의 상태변화**
-   렌더링 발생

:four: **props의 변화**

-   부모로부터 전달받는 props의 변화 = **자식 컴포넌트의 상태변경**
-   => 리렌더링 발생

:five: **컴포넌트의 key props의 변경**

-   리액트에서 key는 명시적으로 선언한 상태가 아니여도 모든 컴포넌트에서 사용 가능한 **특수한 props**
-   일반적으로 key는 하위 컴포넌트를 선언할 때 사용

:six: useReducer()의 두번째 배열 요소가 실행되는 경우

:seven: 클래스형 컴포넌트에서 리렌더링 되는 경우

Stack Reconciler => Fiber Reconciler


### 

### 리액트의 Fiber Reconciler
- 왼쪽 자식 오른쪽 형제 이진 트리 (_Left-child-right-sibling-tree_) 구조
- 트리를 이용하여 컴포넌트의 구조를 객체로 변환 (_React Element_), 렌더링하고자하는 모든 정보를 구체화해서 담아둠
- 리액트에서 자주 사용하는 props들이 그 정보들을 저장하는것 (_type, key, ref, props, id, children ..._) 
- 이 리액트 엘리먼트로 가상돔 생성 (객체 값)이므로 수정 및 생성 삭제에 연산이 크게 감소

Reconciliation (재조정) 작업
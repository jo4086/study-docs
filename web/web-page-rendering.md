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

:one: **렌더링을 유발하는 단계**
   - `createRoot` 실행 or `state`의 업데이트

:two: **렌더링으로 컴포넌트를 호출하는 단계**
   - **ㄴ:one:** ( `createRoot`로 렌더링이 발생 ) → ( 루트 컴포넌트 호출 )
   - **ㄴ:two:** ( `state`의 업데이트로 렌더링 발생 ) → ( 해당 `state`가 속한 컴포넌트를 호출 )

:three: **커밋 단계**
   - 실제 `DOM`에 적용하는 작업
   - **ㄴ:one:** 첫번째 커밋인 경우:\
     　　　- apeendChild를 사용하여 스크린에 있는 모든 노드 생성
   - **ㄴ:two:** 첫번째 커밋이 아닌 경우:\
      　　　- 최소한의 작업을 통해 변경사항만을 실제 DOM에 적용\
      　　　- ⭐ 변경사항은 렌더링 중에 계산
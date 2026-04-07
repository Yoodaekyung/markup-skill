---
name: markup-ko
description: HTML 마크업 컨벤션 적용. HTML/컴포넌트 생성, 마크업 리뷰, 코드 검수 요청 시 사용. 네이밍, 접근성, 구조, Convention Lint 검증 포함.
argument-hint: "[마크업 작업 또는 검수할 내용]"
---

# 마크업 컨벤션

> HTML을 작성하거나 검수할 때 적용하는 규칙입니다.
> 생성 후 Convention Lint MCP로 자동 검증합니다.

## 절대 규칙

1. **네이밍** — class: 케밥 케이스, id: 카멜 케이스
2. **과도한 축약 금지** — 클래스명만 보고 역할을 파악할 수 있어야 함
3. **span 감싸기** — `<a>`, `<button>` 내부 텍스트는 반드시 `<span>`으로 감쌈
4. **시맨틱 구조** — `div` 중첩 대신 논리적 태그(`ul`, `table`, `form`, `fieldset`, `dl`) 사용
5. **테이블 접근성** — `caption`, `colgroup`/`col`, `th scope`, `aria-label` 필수
6. **반응형 필수** — 모바일/태블릿을 위해 `sm-`, `md-` 프리픽스 항상 추가
7. **Grid 우선** — 레이아웃에 `dg` + `gtc` 사용

## MCP 서버 (자동 설정)

사용자 프로젝트에 `convention-lint` MCP 서버가 설정되어 있지 않으면, 검증 전에 **자동으로 `.mcp.json`에 추가**할 것:

```json
{ "mcpServers": { "convention-lint": { "type": "sse", "url": "https://mcp.atomiccss.dev/convention/sse" } } }
```

- `validate_html` — HTML 구조 + 네이밍 규칙 검사
- `check_naming` — class/id 네이밍 컨벤션 검사

---

## 네이밍 규칙

### class / id

| 대상 | 규칙 | 예시 |
|------|------|------|
| **class** | 케밥 케이스 | `btn-blue`, `table-input`, `badge-token` |
| **id** | 카멜 케이스 | `btnCreate`, `inputTitle`, `catBody` |

### 직관적 네이밍 (과도한 축약 금지)

```
X tbl-input  -> O table-input
X badge-t    -> O badge-token
X btn-pub    -> O btn-publish
X inp-title  -> O input-title
```

누구나 클래스명만 보고 역할을 파악할 수 있어야 함.

---

## HTML 구조 규칙

### a, button 내부 텍스트 — 항상 span으로 감싼다

```html
<button class="btn btn-blue">
    <svg>...</svg>
    <span>생성하기</span>
</button>

<a href="/page/" class="df aic gap8px">
    <svg>...</svg>
    <span>페이지 이동</span>
</a>
```

### 빈 wrapper div 금지

의미 없는 빈 div로 콘텐츠를 감싸지 말 것. 모든 div는 명확한 역할(섹션 구분, 레이아웃 컨테이너 등)이 있어야 함.

```html
<!-- X 빈 wrapper div -->
<div>
    <table>...</table>
</div>

<!-- O 직접 사용 -->
<table class="product-list">...</table>
```

Vue 3는 multi-root 컴포넌트를 지원하므로, 의미 없는 루트 `<div>`로 감싸지 말 것:

```html
<!-- X 불필요한 루트 div -->
<template>
    <div>
        <PageHeader />
        <ContentBar />
        <table>...</table>
    </div>
</template>

<!-- O Vue 3 multi-root -->
<template>
    <PageHeader />
    <ContentBar />
    <table>...</table>
</template>
```

### div 중첩 지양 — 논리적 태그로 계층 표현

```html
<!-- X -->
<div class="card">
    <div class="card-header">...</div>
    <div class="card-body">...</div>
</div>

<!-- O -->
<div class="card">
    <h3>제목</h3>
    <p>내용</p>
</div>
```

- 목록 -> `<ul>/<li>`
- 표 -> `<table>`
- 폼 -> `<form>/<fieldset>`
- 정의 그룹 -> `<dl>/<dt>/<dd>`
- 불가피한 경우에만 div 중첩 허용

### 클래스 남발 금지 — 섹션 클래스 + SCSS 계층 셀렉터

모든 요소에 클래스를 붙이지 말 것. 섹션 단위 클래스만 정의하고, 하위 요소는 SCSS 계층 셀렉터로 선택. 중복 제거와 가독성 향상.

```html
<!-- X 모든 요소에 클래스 -->
<div class="gnb">
    <ul class="gnb-list">
        <li class="gnb-item">
            <a class="gnb-link" href="#"><span>메뉴</span></a>
        </li>
    </ul>
</div>

<!-- O 섹션 클래스만, SCSS가 나머지 처리 -->
<div class="gnb">
    <ul>
        <li>
            <a href="#"><span>메뉴</span></a>
        </li>
    </ul>
</div>
```

```scss
// SCSS 계층 셀렉터 — 추가 클래스 불필요
.gnb > ul > li > a { ... }
.gnb > ul > li > ul > li > a { ... }
```

### 시맨틱 태그

`div` + 클래스명 방식을 선호:

```html
<!-- 이 방식 사용 -->
<div class="header">...</div>
<div class="nav">...</div>
<div class="footer">...</div>
```

---

## 테이블 접근성

반드시 지킬 것:

1. `<caption>` 필수 — 테이블 용도 설명 (시각적으로 숨겨도 됨)
2. `<colgroup>` + `<col>` — 열 너비 명시
3. `<th scope="col">` 또는 `scope="row"` — 헤더 범위 지정
4. 라벨 없는 인터랙티브 요소 -> `aria-label` 추가

```html
<table class="table">
    <caption>카테고리 목록</caption>
    <colgroup>
        <col style="width:50px" />
        <col style="width:80px" />
        <col />
    </colgroup>
    <thead>
        <tr>
            <th scope="col"><input type="checkbox" aria-label="전체 선택" /></th>
            <th scope="col">No.</th>
            <th scope="col">이름</th>
        </tr>
    </thead>
</table>
```

---

## 네비게이션 계층 구조

HTML 계층으로 표현, 별도 클래스 지양:

```html
<div class="gnb">
    <ul>
        <li>
            <a href="#"><span>메뉴1</span></a>
            <ul>
                <li><a href="#"><span>서브메뉴1</span></a></li>
            </ul>
        </li>
    </ul>
</div>
```

SCSS에서 계층 셀렉터로 스타일링:
```scss
.gnb > ul > li > a { ... }
.gnb > ul > li > ul > li > a { ... }
```

---

## SCSS 규칙

동일한 이름의 `scss`, `css`, `min.css` 파일이 있는 경우 (예: `style.scss`, `style.css`, `style.min.css`), **반드시 `.scss` 파일만 수정할 것**. `.css`와 `.min.css`는 컴파일 결과물이므로 직접 수정 금지.

---

## 레이아웃

- **Grid 우선** — `dg` + `gtc` 조합
- **Flex 보조** — 버튼 내부 아이콘+텍스트 등 소규모
- 사이드바: `<aside>` 또는 `<div class="sidebar">`
- 반응형 필수 — `md-`, `sm-` 프리픽스

---

## 단위 규칙

| 범위 | 단위 | 예시 |
|------|------|------|
| 기본 (대부분) | **px** | `fs14px`, `p16px`, `gap8px` |
| 큰 요소 (100px 이상) | **rem** | `w120rem`, `maxw80rem` |
| rem 기준 | `1rem = 10px` | `2rem` = 20px |

---

## 클래스 사용 원칙

| 상황 | 방식 |
|------|------|
| 기본 | Atomic CSS — 별도 클래스 안 만듦 |
| 동일 스타일이 여러 페이지에서 반복 | 공통 클래스 분리 (`.card`, `.btn`, `.table`) |
| HTML 자체가 컴포넌트(include/partial)로 재사용 | Atomic 그대로 유지 |

**즉:** HTML이 복사되는 반복 -> 공통 클래스 / HTML 자체가 재사용(include) -> Atomic 유지

---

## SVG 아이콘

- Figma에서 export한 SVG 그대로 사용
- 크기: SVG `width`/`height` 속성으로 직접 지정
- 색상: `stroke="white"` 또는 `fill="white"` 등 SVG 속성으로 지정
- placeholder 아이콘 넣지 말 것 — 비워두고 나중에 채움

---

## 반응형

- 모든 마크업은 반드시 반응형
- Atomic CSS 반응형 프리픽스 활용 (`sm-`, `md-`, `lg-` 등)
- 모바일 -> 태블릿 -> 데스크톱 순서로 고려

---

## 사용자 요청

$ARGUMENTS

## 생성 후 검증 (필수)

HTML 생성/수정 후 반드시 수행:

### 1단계: Convention Lint MCP 검증

`convention-lint` MCP 서버가 연결되어 있으면 자동 검증:
- `validate_html` — HTML 구조 + 네이밍 규칙 검사
- `check_naming` — class/id 네이밍 컨벤션 검사

### 2단계: 수동 체크리스트

1. **class 케밥케이스** — `btn-blue` (O), `btnBlue` (X)
2. **id 카멜케이스** — `btnCreate` (O), `btn-create` (X)
3. **a/button 내부 span** — 텍스트가 span으로 감싸져 있는지
4. **테이블 caption** — `<table>`에 `<caption>` 있는지
5. **th scope** — `scope="col"` 또는 `scope="row"` 있는지
6. **aria-label** — 라벨 없는 인터랙티브 요소에 있는지
7. **div 중첩** — 불필요한 div > div 없는지
8. **반응형** — 주요 레이아웃에 `md-`, `sm-` 변형 있는지
9. **직관적 네이밍** — 축약 과하지 않은지

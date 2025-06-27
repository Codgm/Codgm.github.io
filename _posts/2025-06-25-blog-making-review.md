---
layout: single
title: "Github Jekyll 기반 개인 블로그 제작 회고"
subtitle: "테마 커스터마이징, 검색, 다크/라이트, 댓글, 실전 노하우까지"
date: 2025-06-25
author: "SEO GYUMIN"
author_profile; true
categories: ["블로그제작", "프로젝트"]
tags: ["Jekyll", "Minimal Mistakes", "블로그", "커스터마이징", "회고", "Coding"]
comments: true
share: true
related: true
toc: true
---

## Jekyll(Minimal Mistakes) 기반 개인 블로그 제작 회고

## **1. 시작하며**

- Jekyll과 Minimal Mistakes 테마로 블로그를 시작했습니다.
- 단순히 글을 올리는 공간이 아니라, **나만의 색상, UX, 기능**을 갖춘 블로그를 만들고자 했습니다.
- 오픈소스 테마의 구조를 분석하고, 원하는 대로 커스터마이징하는 과정에서 Jekyll의 빌드 구조와 Liquid 문법, SCSS, JS의 상호작용을 깊이 이해하게 됐습니다.
- 블로그를 직접 운영하며, 실사용자 입장에서 필요한 기능과 디자인을 고민하게 됐습니다.

## **2. 테마 커스터마이징**

- `_custom.scss`에서 CSS 변수로 **다크/라이트 테마** 색상을 일괄적으로 관리했습니다. 주요 색상, 배경, 텍스트, 포인트 컬러, 카드/코드/버튼/네비게이션 등 모든 UI 요소를 변수로 통일해 유지보수성을 높였습니다.

```scss
// _sass/minimal-mistakes/_custom.scss
body[data-theme="dark"] {
  --bg-primary: #0f0f1a;
  --accent-primary: #a855f7;
  --text-primary: #ffffff;
  ...
}
body[data-theme="light"] {
  --bg-primary: #fff;
  --accent-primary: #7c3aed;
  --text-primary: #1a1a1a;
  ...
}
```

- masthead, greedy-nav, 푸터, sidebar 등 주요 UI를 테마에 맞게 세밀하게 조정했습니다. 예를 들어, 네비게이션과 푸터의 배경/글자색, hover 효과, border-radius, box-shadow 등도 테마별로 다르게 적용했습니다.

```scss
// _sass/minimal-mistakes/_custom.scss
.masthead {
  background: color-mix(in srgb, var(--bg-primary) 95%, transparent);
  border-bottom: 1.5px solid var(--border-color);
  ...
}
.greedy-nav a, .masthead__menu a {
  color: var(--text-primary);
  border-radius: 8px;
  transition: all 0.2s ease;
}
```

- skip navigation, 불필요한 마크업, 중복 padding/margin 등은 과감히 제거해 코드와 UI를 간결하게 만들었습니다.
- 테마 토글 버튼을 masthead.html과 custom.js에서 구현하여, body에 data-theme 속성을 동적으로 적용했습니다. 토글 버튼은 Search 메뉴 옆에 고정시켜 접근성을 높였습니다.

```html
<!-- _includes/masthead.html (일부) -->
{% if link.title == 'Search' %}
  <button class="dark-light-toggle" id="darkLightToggle" title="테마 변경">
    <span class="icon30 sun">☀️</span>
    <span class="icon30 moon">🌙</span>
  </button>
{% endif %}
```

```js
// assets/js/custom.js (일부)
document.addEventListener('DOMContentLoaded', function () {
  const themeToggle = document.querySelector('#darkLightToggle');
  function applyTheme(theme) {
    document.body.setAttribute('data-theme', theme);
    ...
  }
  const savedTheme = localStorage.getItem('theme');
  if (savedTheme) {
    applyTheme(savedTheme);
  } else {
    applyTheme('dark');
  }
  if (themeToggle) {
    themeToggle.addEventListener('click', function() {
      const current = document.body.getAttribute('data-theme');
      const next = current === 'light' ? 'dark' : 'light';
      applyTheme(next);
      localStorage.setItem('theme', next);
    });
  }
});
```

- 반응형 디자인도 신경써서, 모바일 환경에서 sidebar, 카드, 네비게이션, 테마 토글 버튼의 위치와 크기를 최적화했습니다.

## **3. 검색 기능 개선**

- 기존 Lunr 기반 검색에서 Google CSE로도 전환할 수 있도록 구조를 파악하고 설정했습니다. `_config.yml`에서 search_provider를 쉽게 바꿀 수 있도록 했고, 실제로 Algolia, Lunr, Google CSE 등 다양한 검색 엔진을 실험했습니다.

```yml
# _config.yml (일부)
search: true
search_provider: algolia # lunr, algolia, google_cse 등
```

- 검색창을 상단 고정, 오버레이, 네비게이션 토글 등 다양한 방식으로 시도했습니다. 특히, 네비게이션의 "Search" 메뉴 클릭 시 오버레이 검색창이 뜨도록 JS/HTML 구조를 변경해 UX를 개선했습니다.

```js
// assets/js/custom.js (일부)
var navSearch = document.querySelector('.nav-search-link');
var overlay = document.getElementById('searchOverlay');
if (navSearch && overlay) {
  navSearch.addEventListener('click', function(e) {
    e.preventDefault();
    overlay.classList.add('active');
    ...
  });
}
```

- 검색 결과는 카드형 UI로, 다크/라이트 테마 모두 자연스럽게 적용되도록 했습니다. 검색 결과가 많을 때도 가독성을 해치지 않도록 카드 배경, 텍스트, 하이라이트 색상을 테마 변수로 통일했습니다.

```scss
// _sass/minimal-mistakes/_custom.scss
.card {
  background: var(--card-bg);
  color: var(--text-primary);
  border-radius: 18px;
  ...
}
```

## **4. 댓글/리액션/Disqus 다크모드 연동**

- Disqus(디스커스) 댓글 영역이 iframe이라 SCSS로 직접 커스텀할 수 없었습니다. 대신, JS에서 사이트의 data-theme을 감지해 Disqus에 postMessage로 테마를 전달하도록 구현했습니다.

```html
<!-- _includes/comments-providers/disqus.html (일부) -->
<script>
  var disqus_config = function () {
    this.callbacks.onReady = [function() {
      var theme = document.body.getAttribute('data-theme') || 'light';
      var iframe = document.getElementById('disqus_thread')?.querySelector('iframe');
      if (iframe) {
        iframe.contentWindow.postMessage({
          type: 'set-theme',
          theme: theme
        }, 'https://disqus.com');
      }
    }];
  };
  var observer = new MutationObserver(function() {
    var theme = document.body.getAttribute('data-theme') || 'light';
    updateDisqusTheme(theme);
  });
  observer.observe(document.body, { attributes: true, attributeFilter: ['data-theme'] });
</script>
```

- 테마 토글 시에도 MutationObserver로 자동 동기화되도록 했습니다. 즉, 사용자가 다크/라이트 테마를 바꿀 때마다 댓글 영역도 즉시 테마가 맞춰집니다.
- 리액션, 정렬, 공유 등 모든 텍스트/아이콘이 테마에 맞게 바뀌도록 했습니다. 댓글 영역 외에도, 공유 버튼, 리액션 아이콘 등도 테마별로 색상과 hover 효과를 다르게 적용했습니다.
- 댓글 기능은 _config.yml에서 provider만 바꾸면 Disqus, Giscus, Utterances 등 다양한 서비스로 쉽게 전환할 수 있도록 구조화했습니다.

## **5. 코드/디자인 세부 개선**

- 코드블록 스타일에서 border, radius, box-shadow 등 불필요한 부분을 제거하고, 색상 변수를 통일했습니다. 코드블록, 인라인 코드, pre 태그 모두 다크/라이트에 맞는 배경과 글자색, border, radius를 적용했습니다.

```scss
// _sass/minimal-mistakes/_custom.scss
.highlight, pre, code {
  background: var(--code-bg) !important;
  color: var(--code-text) !important;
  font-family: 'JetBrains Mono', 'Fira Code', 'Monaco', monospace;
  ...
}
code:not(pre code) {
  background: var(--hover-bg) !important;
  color: var(--accent-primary) !important;
  padding: 3px 8px;
  border-radius: 6px;
  border: 1px solid var(--border-color);
}
```

- sidebar, pagination, meta, share, comments 등 모든 컴포넌트의 색상, 간격, hover 효과를 테마에 맞게 반복적으로 개선했습니다. 예를 들어, pagination의 현재 페이지, hover, disabled 상태까지 모두 변수로 관리했습니다.

```scss
// _sass/minimal-mistakes/_custom.scss
.pagination li a {
  background: transparent;
  color: var(--text-secondary);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  ...
}
.pagination li a:hover {
  background: var(--accent-primary);
  color: var(--pagination-hover-text);
}
```

- 404, about, 각종 archive 페이지 등도 세부적으로 다듬었습니다. 각 페이지의 배경, 카드, 타이틀, 링크, 버튼 등도 일관성 있게 커스터마이징했습니다.
- 반응형 레이아웃, 스크롤바, selection, 링크 스타일 등도 세밀하게 조정해, 모바일/PC 어디서나 일관된 경험을 제공하도록 했습니다.

## **6. 시행착오 & 배운 점**

- JS/CSS 변수명 불일치로 테마 토글이 동작하지 않는 문제, body에 data-theme를 둘지 html에 둘지 고민하며 해결했습니다. 실제로 여러 번 변수명을 잘못 써서 테마가 적용되지 않는 버그를 겪었습니다.
- 검색창 위치와 동작(상단 고정, 오버레이, 네비게이션 연동 등)을 여러 번 실험했습니다. 오버레이 방식이 UX에 가장 적합하다고 판단해 적용했습니다.
- Disqus 등 외부 서비스와 다크모드 연동은 JS postMessage로 해결했습니다. iframe 구조의 한계와 postMessage의 활용법을 실전에서 익혔습니다.
- 이미지 경로, 파일 위치, Jekyll의 baseurl/url 설정 등도 꼼꼼히 체크해야 했습니다. 특히, 커스텀 도메인이나 GitHub Pages에서 경로가 꼬이지 않도록 _config.yml의 url/baseurl 세팅을 반복적으로 점검했습니다.
- SCSS 변수, JS 로컬스토리지, MutationObserver 등 다양한 웹 기술을 실전에서 활용하며, 유지보수성과 확장성을 모두 고려하게 됐습니다.

## **7. 마치며**

- 단순히 테마만 바꾼 것이 아니라, **내가 원하는 UX/디자인/기능**을 직접 구현하며 Jekyll 블로그에 대한 이해가 깊어졌습니다.
- 시행착오를 겪으며 CSS/JS 구조, Jekyll의 동작 원리, 외부 서비스 연동까지 폭넓게 경험했습니다. 특히, 오픈소스 테마를 내 입맛대로 바꾸는 과정에서 웹 프론트엔드의 다양한 노하우를 쌓았습니다.
- 앞으로도 새로운 기능과 디자인을 계속 실험하며 성장할 예정입니다. 예를 들어, 다국어 지원, 접근성 개선, SEO 최적화, 더 다양한 댓글/검색/공유 기능 등도 도전해보고 싶습니다.
- 블로그 커스터마이징을 직접 해보면, 단순히 예쁜 테마를 고르는 것 이상의 실전 경험과 성장의 기회를 얻을 수 있습니다.

---

**최종 결과:**
- 다크/라이트 테마를 완벽하게 지원했습니다. (body[data-theme], CSS 변수, JS 토글, 로컬스토리지, MutationObserver 등 활용)
- 네비게이션, 푸터, 목차, 코드, 댓글, 검색 등 모든 UI/UX를 세밀하게 커스터마이징했습니다. (모든 컴포넌트의 색상/간격/hover/반응형까지 일관성 있게 적용)
- 검색, 댓글, 리액션, 코드블록 등 실전에서 자주 쓰는 기능을 내 스타일로 완성했습니다. (검색 엔진 전환, 댓글 서비스 전환, 실시간 테마 동기화 등)

> 블로그 커스터마이징, 직접 해보면 정말 많이 배울 수 있습니다! 🚀 
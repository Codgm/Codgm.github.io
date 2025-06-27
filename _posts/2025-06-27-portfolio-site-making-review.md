---
layout: single
title: "Next.js 기반 포트폴리오 사이트 제작 회고"
subtitle: "SEO, 반응형, 커스텀 커서, 프로젝트 카드 확장, 실전 노하우까지"
author: "SEO GYUMIN"
categories: ["포트폴리오 사이트 제작", "프로젝트"]
tags: ["Next.js", "TypeScript", "포트폴리오", "커스터마이징", "회고", "Coding"]
author_profile: true
comments: true
share: true
related: true
toc: true
---

## Next.js 기반 포트폴리오 사이트 제작 회고

## **1. 시작하며**

- Next.js와 TypeScript로 포트폴리오 사이트를 직접 설계하고 개발했습니다.
- 단순한 정적 페이지가 아니라, 데이터 중심 설계, 커스텀 UI/UX, SEO, 반응형, 실전 배포까지 실무에서 필요한 요소를 모두 경험했습니다.
- 프로젝트를 진행하며, 유지보수성과 확장성을 고려한 구조로 설계하는 것이 얼마나 중요한지 체감했습니다.

## **2. SEO 및 글로벌 대응 커스터마이징**

- `app/layout.tsx`에서 SEO를 위한 메타데이터, 오픈그래프, 트위터 카드, 키워드 등을 직접 추가했습니다.
- 실제로 구글, 네이버 등 검색엔진에서 '서규민 포트폴리오', 'Codgm Portfolio' 등으로 검색 시 사이트가 노출될 수 있도록 키워드와 설명을 신경써서 작성했습니다.
- `<html lang="ko,en">`로 다국어 접근성을 높였고, 글로벌 사용자도 쉽게 접근할 수 있도록 했습니다.
- SEO 최적화의 효과로, 사이트가 검색엔진에 빠르게 색인되고, 소셜 미디어 공유 시 미리보기 이미지와 설명이 잘 노출되는 것을 경험했습니다.

```ts
export const metadata = {
  title: "...",
  description: "...",
  keywords: ["...", "...", ...],
  openGraph: { ... },
  twitter: { ... },
};
```

## **3. 모바일 UX, 반응형, 접근성**

- 모바일 환경에서는 커스텀 커서가 보이지 않도록 조건부 렌더링을 적용했습니다.
- 모든 주요 섹션은 반응형 레이아웃으로 설계했고, 버튼/링크/이미지에 alt, aria-label 등 접근성 속성을 신경써서 작성했습니다.
- 실제 모바일 기기에서 테스트하며, 터치 UX와 레이아웃 깨짐, 커서 비노출 등 세부적인 부분까지 점검했습니다.
- 반응형 메뉴(햄버거 버튼), 스크롤에 따라 변화하는 헤더, 부드러운 스크롤 이동 등 다양한 UI/UX를 직접 구현했습니다.

```tsx
const [isMobile, setIsMobile] = useState(false);
useEffect(() => {
  if (typeof window !== "undefined") {
    const checkMobile = () => {
      const ua = navigator.userAgent;
      return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(ua);
    };
    setIsMobile(checkMobile());
  }
}, []);
...
{!isMobile && <CustomCursor />}
```

## **4. 데이터 중심 설계와 프로젝트 카드 확장**

- `data/portfolioData.ts`에서 프로젝트, 스킬, 소개, 경력, 학력 등 모든 데이터를 객체로 관리했습니다.
- 데이터만 추가하면 UI가 자동으로 확장되어 유지보수성이 매우 높아졌습니다.
- Skills, About, Projects, Contact 등 모든 섹션이 데이터 기반으로 동작해, 확장성과 일관성을 확보했습니다.
- `ProjectCard.tsx`에서는 blog 링크가 있을 때만 "📝 Details" 버튼이 나타나도록 조건부 렌더링을 적용했습니다.

```ts
export const projects = [
  {
    title: "...",
    desc: "...",
    stack: "...",
    link: "...",
    github: "...",
    blog: "..."
  },
  // ...
];
```

```tsx
{blog && (
  <a href={blog} ...>📝 Details</a>
)}
```

## **5. 커스텀 UI/UX와 부가 효과**

### 5-1. 커스텀 커서와 트레일 효과

- `components/CustomCursor.tsx`에서 마우스 위치에 따라 커서와 트레일 이펙트를 직접 구현했습니다.
- 커서와 트레일이 부드럽게 따라다니며, 사이트의 개성을 살렸습니다.
- 모바일에서는 UX를 위해 커서가 아예 렌더링되지 않습니다.

```tsx
useEffect(() => {
  const cursor = document.getElementById('cursor');
  const handleMouseMove = (e: MouseEvent) => {
    if (cursor) {
      cursor.style.left = e.clientX + 'px';
      cursor.style.top = e.clientY + 'px';
    }
    // 트레일 효과
    const trail = document.createElement('div');
    trail.className = 'cursor-trail';
    trail.style.left = e.clientX + 'px';
    trail.style.top = e.clientY + 'px';
    document.body.appendChild(trail);
    setTimeout(() => { trail.remove(); }, 500);
  };
  document.addEventListener('mousemove', handleMouseMove);
  return () => document.removeEventListener('mousemove', handleMouseMove);
}, []);
```

### 5-2. 배경 애니메이션

- `components/BackgroundEffects.tsx`에서 다양한 배경 효과를 직접 DOM 조작으로 구현했습니다.
- 랜덤 플로팅 요소, 매트릭스 레인, 회로 패턴, 데이터 스트림 등 다양한 애니메이션을 각각 함수로 분리해 관리했습니다.
- 윈도우 리사이즈 시에도 애니메이션이 자연스럽게 재생성되도록 했습니다.

```tsx
const createMatrixRain = () => {
  const container = document.getElementById('matrixRain');
  if (!container) return;
  container.innerHTML = '';
  const columns = Math.floor(window.innerWidth / 20);
  const matrixChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()';
  for (let i = 0; i < columns; i++) {
    const column = document.createElement('div');
    column.className = 'matrix-column';
    column.style.left = `${i * 20}px`;
    column.style.animationDelay = `${Math.random() * 3}s`;
    column.style.animationDuration = `${3 + Math.random() * 2}s`;
    let chars = '';
    for (let j = 0; j < 20; j++) {
      chars += matrixChars[Math.floor(Math.random() * matrixChars.length)] + '<br>';
    }
    column.innerHTML = chars;
    container.appendChild(column);
  }
};
```

## **6. 주요 섹션별 컴포넌트 구조**

### 6-1. 헤더/내비게이션

- 스크롤에 따라 스타일이 바뀌고, 모바일에서는 햄버거 메뉴로 전환되어 UX를 높였습니다.
- 부드러운 스크롤 이동, 접근성, 반응형 등 다양한 UI/UX를 직접 구현했습니다.

```tsx
const navItems = [
  { name: 'Home', href: '#...' },
  { name: 'About', href: '#...' },
  // ...
];
<nav className={`${styles.nav} ${isMenuOpen ? styles.open : ''}`}>
  {navItems.map((item) => (
    <a
      key={item.name}
      href={item.href}
      onClick={handleNavClick}
      className={styles.navLink}
    >
      {item.name}
    </a>
  ))}
</nav>
```

### 6-2. AboutSection

- 소개, 경력, 학력 정보를 모두 데이터 기반으로 렌더링했습니다.
- 경력, 학력도 각각 timeline, 리스트 형태로 시각화하여, 한눈에 경로를 파악할 수 있도록 했습니다.

```tsx
<div className={styles.aboutText}>
  <h3 className={styles.aboutName}>{about.name}</h3>
  {about.description.map((text, index) => (
    <p key={index} className={styles.aboutDescription}>
      {text}
    </p>
  ))}
  <div className={styles.aboutStats}>
    {about.stats.map((stat, index) => (
      <div key={index} className={styles.stat}>
        <span className={styles.statNumber}>{stat.number}</span>
        <span className={styles.statLabel}>{stat.label}</span>
      </div>
    ))}
  </div>
</div>
```

### 6-3. SkillsSection

- 탭별로 스킬을 분류하고, 각 스킬의 숙련도, 경험, 프로젝트 수를 시각적으로 보여줬습니다.
- 탭 클릭 시 해당 카테고리의 스킬만 보여주고, 마우스 오버 시 상세 툴팁이 나타나도록 했습니다.

{% raw %}
```tsx
<div className={styles.skillsTabs}>
  {skillCategories.map((category, index) => (
    <button
      key={category.title}
      className={`${styles.skillTab} ${selectedCategory === index ? styles.active : ''}`}
      onClick={() => setSelectedCategory(index)}
      style={{ '--tab-color': category.color } as React.CSSProperties}
    >
      <span className={styles.tabIcon}>{category.icon}</span>
      {category.title}
    </button>
  ))}
</div>
...
<div className={styles.skillsGrid}>
  {skillCategories[selectedCategory].skills.map((skill) => (
    <div
      key={skill.name}
      className={styles.skillCard}
      onMouseEnter={() => setHoveredSkill(skill.name)}
      onMouseLeave={() => setHoveredSkill(null)}
    >
      <div className={styles.skillHeader}>
        <h3 className={styles.skillName}>{skill.name}</h3>
        <div className={styles.skillLevel}>{skill.level}%</div>
      </div>
      <div className={styles.skillBar}>
        <div 
          className={styles.skillProgress} 
          style={{ 
            width: `${skill.level}%`,
            backgroundColor: skillCategories[selectedCategory].color
          }}
        ></div>
      </div>
      ...
    </div>
  ))}
</div>
```
{% endraw %}

### 6-4. ProjectsSection & ProjectCard

- 프로젝트 리스트를 map으로 렌더링하고, 각 카드는 props로 받아서 표시했습니다.
- 각 프로젝트는 Live Demo, GitHub, Details(블로그) 버튼을 조건부로 보여줍니다.

```tsx
<div className={styles.links}>
  <a href={link} ...>🔗 Live Demo</a>
  {github && <a href={github} ...>📂 GitHub</a>}
  {blog && <a href={blog} ...>📝 Details</a>}
</div>
```

### 6-5. ContactSection

- 이메일, GitHub 등 연락처 정보를 배열로 관리하고, map으로 렌더링했습니다.
- 접근성을 위해 이메일은 mailto, GitHub는 새 창으로 열리도록 처리했습니다.

```tsx
const contactInfo = [
  {
    icon: "...",
    label: "Email",
    value: "...@....com",
    link: "mailto:...@....com"
  },
  {
    icon: "...",
    label: "GitHub",
    value: "github.com/...",
    link: "https://github.com/..."
  },
];
...
{contactInfo.map((contact) => (
  <a 
    key={contact.label}
    href={contact.link} 
    className={styles.contactLink}
    target={contact.link.startsWith('http') ? "_blank" : undefined}
    rel={contact.link.startsWith('http') ? "noopener noreferrer" : undefined}
  >
    {contact.icon} {contact.label}
  </a>
))}
```

## **7. 실전 배포 및 운영 경험**

- Vercel을 통한 CI/CD, 커스텀 도메인, 환경변수 관리, 빌드 최적화 등 실전 운영 경험을 쌓았습니다.
- 구글 서치 콘솔에 사이트맵을 등록하고, 색인 요청을 통해 검색 노출 속도를 높였습니다.
- 소셜 미디어 공유 시 미리보기 이미지, 설명, 타이틀이 정확히 노출되는 것을 확인했습니다.

## **8. 시행착오 & 배운 점**

- 모바일 UX, SEO, 데이터 중심 설계, 커스텀 UI/UX, 실전 배포 등 실무에서 자주 마주치는 문제를 직접 해결하며 성장했습니다.
- 코드와 데이터의 분리, 컴포넌트화, 조건부 렌더링, DOM 조작, 접근성 등 다양한 웹 개발 노하우를 실제로 체득했습니다.
- 각종 애니메이션, 반응형, 접근성, SEO 등 실전에서 바로 적용 가능한 노하우를 쌓았습니다.

## **9. 마치며**

- 단순한 포트폴리오가 아니라, 실전에서 필요한 모든 기능을 직접 구현하며 Next.js, TypeScript, SEO, 반응형, 데이터 설계 등 다양한 역량을 키웠습니다.
- 앞으로도 새로운 기능과 디자인을 계속 실험하며 성장할 예정입니다.

> 직접 커스터마이징하며 실전 경험을 쌓는 것이 가장 큰 성장의 기회였습니다! 🚀 
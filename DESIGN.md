# Design System — 딸깍AI

## Product Context
- **What this is:** AI 업무 자동화 구축 서비스
- **Who it's for:** 반복 업무에 시간을 뺏기고 있는 소규모 사업자 (직원 1~20명)
- **Space/industry:** AI 자동화 구축/외주 (한국 시장)
- **Project type:** Marketing site (Astro SSG)
- **Core value prop:** "1달 안에 핵심 업무 하나를 AI로 해결" — 대기업 SI의 절반 이하 비용으로 실제 작동하는 자동화 구축

## Aesthetic Direction
- **Direction:** 풍부한 전문가 — 콘텐츠가 밀도 있게 채워진 전문 서비스 사이트
- **Decoration level:** Intentional — 카드, 그리드, 탭 UI를 적극 활용하되 장식적 요소는 절제
- **Mood:** 신뢰할 수 있는 전문가. 풍부한 정보량으로 전문성을 보여주되, 위압적이지 않은
- **Anti-patterns:** 가짜 후기 금지, 비디오 플레이스홀더 금지 (실제 콘텐츠만 표시)

## Typography
- **Display/Hero:** Satoshi (900, 700) — 임팩트 있는 영문 헤드라인
- **Body:** Pretendard Variable — 한글 가독성 최고, 한국 서비스 사이트 표준
- **UI/Labels:** Pretendard Variable (600)
- **Data/Tables:** Pretendard Variable (tabular-nums)
- **Code:** JetBrains Mono
- **Loading:**
  - Pretendard: `https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/variable/pretendardvariable-dynamic-subset.min.css`
  - Satoshi: `https://api.fontshare.com/v2/css?f[]=satoshi@400,500,700,900&display=swap`
- **Scale:**
  - xs: 0.75rem (12px)
  - sm: 0.85rem (13.6px)
  - base: 1rem (16px)
  - lg: 1.1rem (17.6px)
  - xl: 1.3rem (20.8px)
  - 2xl: 1.8rem (28.8px)
  - 3xl: clamp(2rem, 4vw, 3rem)
  - hero: clamp(2.5rem, 5vw, 4rem)

## Color
- **Approach:** Restrained — 따뜻한 뉴트럴 + 틸 액센트 1개
- **Primary accent:** #0D9488 (Teal) — 신뢰감, 전문성, AI/기술 느낌이면서 따뜻함
- **Accent hover:** #0B7E73
- **Accent soft:** #CCFBF1 (배지, 하이라이트 배경)
- **Gradient:** linear-gradient(135deg, #0D9488, #06B6D4) — 기존 보라색 그라디언트 대체
- **Text primary:** #1A1A18
- **Text secondary:** #5C5B56
- **Text muted:** #9C9A93
- **Background:** #FAFAF8 (따뜻한 오프화이트)
- **Surface:** #FFFFFF (카드, 패널)
- **Surface alt:** #F3F2EE (대비 배경)
- **Border:** #E5E3DB
- **Semantic:** success #16A34A, warning #CA8A04, error #DC2626, info #0284C7
- **Dark mode:** 향후 고려. 현재는 라이트 모드만.

## Spacing
- **Base unit:** 8px
- **Density:** Comfortable — 소상공인 타겟이므로 충분한 여백
- **Scale:** 2xs(2) xs(4) sm(8) md(16) lg(24) xl(32) 2xl(48) 3xl(64)

## Layout
- **Approach:** Grid-disciplined — 기존 카드 그리드, 탭 UI 유지
- **Grid:** 1-col (mobile) / 2-col (tablet) / 3-col (desktop) for cards
- **Max content width:** 1200px
- **Border radius:** sm:4px, md:8px, lg:12px, xl:16px, full:9999px

## Motion
- **Approach:** Intentional — fade-up 입장 애니메이션 유지, 과도한 것은 제거
- **Easing:** enter(ease-out) exit(ease-in) move(ease-in-out)
- **Duration:** micro(50-100ms) short(150-250ms) medium(250-400ms)

## Content Rules
- 가짜 후기/리뷰 금지 — 실제 케이스 스터디만 사용
- 비디오 플레이스홀더 금지 — 실제 영상이 없으면 섹션 제거
- 사례 섹션을 최우선 배치 — 실제 해결 사례가 핵심 콘텐츠
- 가격은 명확하게 표시 (숨기지 않음)
- Outcome-first 카피 — "n8n 워크플로우 구축"이 아닌 "주당 5시간 절감"
- 사례가 없는 초기 상태에서는 "첫 사례를 준비하고 있습니다" + CTA로 솔직하게 표현

## Decisions Log
| Date | Decision | Rationale |
|------|----------|-----------|
| 2025-03-25 | 보라색 → 틸 액센트 | AI 서비스 카테고리에서 차별화. 신뢰감+따뜻함 |
| 2025-03-25 | Pretendard + Satoshi 채택 | 한글 가독성 최고 + 영문 디스플레이 감각 |
| 2025-03-25 | 가짜 후기 제거 | 고객 0명 상태에서 가짜 후기는 신뢰 파괴 |
| 2025-03-25 | 기존 풍부한 레이아웃 유지 | 미니멀보다 콘텐츠 밀도가 높은 것이 전문성 전달에 효과적 |

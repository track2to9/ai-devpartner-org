# 딸깍AI — AI 업무 자동화 구축 서비스

Astro 6 기반 마케팅 사이트. 소규모 사업자 대상 AI 업무 자동화 구축.

## Design System
Always read DESIGN.md before making any visual or UI decisions.
All font choices, colors, spacing, and aesthetic direction are defined there.
Do not deviate without explicit user approval.
In QA mode, flag any code that doesn't match DESIGN.md.

## Build
```bash
npm run dev    # 개발 서버
npm run build  # 프로덕션 빌드
```

## Structure
- `src/pages/` — 페이지 (index, contact, case-study, thank-you, terms, privacy)
- `src/components/` — 컴포넌트 (Header, Footer)
- `src/layouts/` — 레이아웃 (BaseLayout)

## Brand
- 고객 대면 브랜드: 딸깍AI
- 도메인: ai.devpartner.org
- 서비스 전략: 4주 구축 (1주 업무분석→2~3주 구축→4주 테스트+인수인계) + 콘텐츠(블로그/유튜브/링크드인)
- 포지셔닝: AI 업무 자동화 구축 (대기업 SI의 절반 이하 비용으로 4주 안에 핵심 업무 자동화)
- 핵심 메시지: "당신 업무에서 가장 중요한 한 가지, 1달 안에 AI로 해결합니다."
- 가격대: 구축 200~400만원, 유지보수 50~150만원/월
- 신뢰 채널: 유튜브/블로그/링크드인 (직접 영업이 아닌 검색유입+신뢰축적 역할)

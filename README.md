# web — Pointer 사이트 (+ MADA 제품/약관/지원)

메인은 **팀(Pointer)** 소개이고, 제품 목록에서 **MADA**로 들어가면 앱 소개 +
앱스토어/플레이스토어 제출에 필요한 **지원 URL·개인정보 처리방침 URL**이 나온다.
빌드 도구·프레임워크 없음 — HTML/CSS + 작은 vanilla JS(`app.js`, 스크롤 애니메이션).

```
web/
  index.html      Pointer 홈 — 히어로 + #about(우리가 일하는 방식) + #products(MADA 카드)
  mada.html       MADA 제품 페이지 (앱 소개 + 지원·약관 링크)
  support.html    고객 지원  (스토어 Support URL 대상)
  privacy.html    개인정보 처리방침
  terms.html      서비스 이용약관
  location.html   위치기반서비스 이용약관
  styles.css
  app.js          로드/스크롤 진입 애니메이션, 헤더 스크롤 상태 (prefers-reduced-motion 존중)
  assets/mada-logo.png   (frontend/assets/app/logo/mada-logo.png 사본)
```

헤더: Pointer 페이지는 `● Pointer` 워드마크, MADA 계열 페이지는 `Pointer → [MADA 로고]`
(로고는 테두리·프레임·텍스트 없이 이미지만, 36px). 우측 내비게이션은 전 페이지 공통
(`소개` / `제품` / `지원`).

## 로컬 미리보기

```bash
open web/index.html
# 또는 (상대경로 확인용) — 포트 8002 이상
cd web && python3 -m http.server 8002    # http://localhost:8002
```

## 배포

`pointer-mada.com` 연결까지 포함한 단계별 방법은 **[`DEPLOY.md`](DEPLOY.md)** 참고.
요약: 빌드 없는 정적 파일이라 `web/` 디렉터리를 그대로 publish (Cloudflare Pages 권장,
output directory `web`).

## 제출 전 채워야 할 것 (TODO)

- `support@mada.app` → 실제 MADA 문의 이메일 (mada.html, support.html, privacy.html, location.html, footer)
- `hello@pointer.team` → 실제 팀 문의 이메일 (index.html)
- `[담당자명]` → 개인정보 보호책임자 / 위치정보 관리책임자 이름 (privacy.html, location.html)
- `Pointer` 회사 표기 → 정식 사업자명이 있으면 교체
- 시행일자 `2026년 8월 30일` → 실제 시행일
- **Pointer 홈(index.html) 소개 문구** — 현재 임시 문구, 실제 팀 소개로 교체

약관 본문은 `frontend/src/constants/legal.ts`의 `LEGAL_TEXTS` 국문 초안과 같은 내용이다.
한쪽을 고치면 다른 쪽도 같이 갱신한다.

## Next.js로 갈 시점

지금 범위(마케팅 + 약관 + 지원)는 라우팅·SSR·빌드가 필요 없어 정적이 더 빠르고
관리도 쉽다. 아래가 생기면 Next.js(또는 Astro)를 검토한다:

- 제품이 여러 개가 되고 공통 레이아웃/컴포넌트 재사용이 늘 때
- 5개 언어(ko/en/ja/zh-hant/zh-hans) i18n 라우팅이 필요할 때
- 블로그·변경로그·문서 등 콘텐츠가 늘어 MDX/CMS가 필요할 때
- 서버가 받아 처리하는 폼(문의 접수 등), 뉴스레터, OG 이미지 생성 등이 필요할 때

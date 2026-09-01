# 배포 — pointer-mada.com

`web/`는 빌드가 없는 순수 정적 사이트(HTML/CSS/JS)다. 모든 링크가 상대경로라
(`assets/…`, `styles.css`, `mada.html` …) 도메인 루트에 그대로 올리면 된다.
아래 셋 중 하나를 고른다. **Cloudflare Pages를 권장** — 모노레포 하위 폴더 + 커스텀
도메인 연결이 가장 간단하다.

사전 준비: 도메인 `pointer-mada.com`을 등록기관(가비아/Namecheap/Cloudflare Registrar 등)에서
보유하고 있어야 한다.

---

## A. Cloudflare Pages (권장)

1. https://dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** → `p-ointer/pololo` 선택.
2. 빌드 설정:
   - **Production branch**: `main` (또는 배포용 브랜치)
   - **Framework preset**: None
   - **Build command**: 비움
   - **Build output directory**: `web`
   - (Root directory 옵션이 보이면 `web`으로, 그리고 output은 `/`)
3. **Save and Deploy** → `xxxx.pages.dev` 주소로 먼저 뜬다.
4. 프로젝트 → **Custom domains** → **Set up a domain** → `pointer-mada.com` 입력.
   - 도메인 DNS가 이미 Cloudflare에 있으면 레코드가 자동 추가된다.
   - 아니면 안내되는 CNAME(예: `pointer-mada.com` → `xxxx.pages.dev`)을 현재 DNS에 추가.
     apex 도메인은 Cloudflare가 CNAME flattening으로 처리한다.
5. `www.pointer-mada.com`도 같이 추가하고, 한쪽을 canonical로 리다이렉트
   (Custom domains에서 redirect 설정).
6. HTTPS 인증서는 자동 발급(수 분~수십 분).

이후 `main`에 push하면 자동 재배포된다.

---

## B. GitHub Pages (Actions로 web/ 만 배포)

GitHub Pages는 임의의 `web/` 폴더를 바로 못 고르므로, Actions로 `web/`만 올린다.

1. 레포에 `.github/workflows/pages.yml` 추가:

   ```yaml
   name: Deploy web to Pages
   on:
     push:
       branches: [main]
       paths: ["web/**", ".github/workflows/pages.yml"]
   permissions:
     contents: read
     pages: write
     id-token: write
   concurrency:
     group: pages
     cancel-in-progress: true
   jobs:
     deploy:
       runs-on: ubuntu-latest
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       steps:
         - uses: actions/checkout@v4
         - uses: actions/configure-pages@v5
         - uses: actions/upload-pages-artifact@v3
           with:
             path: web
         - id: deployment
           uses: actions/deploy-pages@v4
   ```

2. 커스텀 도메인 고정용 파일 생성:

   ```bash
   echo "pointer-mada.com" > web/CNAME
   ```

3. GitHub 레포 → **Settings → Pages** → **Source: GitHub Actions**.
4. 같은 화면 **Custom domain**에 `pointer-mada.com` 입력 → **Enforce HTTPS** 체크.
5. DNS 레코드 추가 (도메인 등록기관 또는 DNS 제공자에서):
   - apex `pointer-mada.com` → **A 레코드 4개**
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - (선택) 같은 이름 **AAAA 4개**
     `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`
   - `www.pointer-mada.com` → **CNAME** `p-ointer.github.io`
6. DNS 전파 후(수 분~수 시간) HTTPS 자동 발급.

---

## C. Netlify

1. https://app.netlify.com → **Add new site → Import an existing project** → `p-ointer/pololo`.
2. 빌드 설정:
   - **Base directory**: `web`
   - **Build command**: 비움
   - **Publish directory**: `web`
3. 배포 후 **Domain management → Add custom domain** → `pointer-mada.com`.
4. DNS:
   - Netlify DNS를 쓰면 등록기관에서 네임서버만 Netlify 것으로 변경.
   - 외부 DNS 유지 시: apex `A` → `75.2.60.5`, `www` → CNAME `<site>.netlify.app`.
5. **HTTPS**는 Let's Encrypt로 자동.

---

## 배포 전 로컬 확인

```bash
cd web && python3 -m http.server 8002   # http://localhost:8002 — 링크·이미지·앵커 확인
```

## 제출 URL

- 마케팅 URL: `https://pointer-mada.com/` 또는 `https://pointer-mada.com/mada.html`
- 지원 URL(App Store Connect / Play Console): `https://pointer-mada.com/support.html`
- 개인정보처리방침 URL: `https://pointer-mada.com/privacy.html`

(도메인 붙이기 전 임시로는 `*.pages.dev` / `*.netlify.app` / `p-ointer.github.io` 주소를
그대로 제출해도 된다.)

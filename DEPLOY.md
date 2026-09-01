# 배포 — point-mada.com

이 레포(`p-ointer/web`)는 빌드가 없는 순수 정적 사이트(HTML/CSS/JS)다. 모든 링크가
상대경로라(`assets/…`, `styles.css`, `mada.html` …) 파일을 도메인 루트에 그대로 올리면 된다.
아래 셋 중 하나를 고른다. **Cloudflare Pages를 권장**.

사전 준비: 도메인 `point-mada.com`을 등록기관(가비아/Namecheap/Cloudflare Registrar 등)에서
보유하고 있어야 한다.

---

## A. Cloudflare Pages (권장)

1. https://dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** → `p-ointer/web` 선택.
2. 빌드 설정:
   - **Production branch**: `main`
   - **Framework preset**: None
   - **Build command**: 비움
   - **Build output directory**: `/` (레포 루트)
3. **Save and Deploy** → `xxxx.pages.dev` 주소로 먼저 뜬다.
4. 프로젝트 → **Custom domains** → **Set up a domain** → `point-mada.com` 입력.
   - 도메인 DNS가 이미 Cloudflare에 있으면 레코드가 자동 추가된다.
   - 아니면 안내되는 CNAME(예: `point-mada.com` → `xxxx.pages.dev`)을 현재 DNS에 추가.
     apex 도메인은 Cloudflare가 CNAME flattening으로 처리한다.
5. `www.point-mada.com`도 같이 추가하고, 한쪽을 canonical로 리다이렉트
   (Custom domains에서 redirect 설정).
6. HTTPS 인증서는 자동 발급(수 분~수십 분).

이후 `main`에 push하면 자동 재배포된다.

---

## B. GitHub Pages

전용 레포라 Actions 없이 브랜치 배포로 바로 된다.

1. 커스텀 도메인 고정용 파일을 레포 루트에 생성:

   ```bash
   echo "point-mada.com" > CNAME
   ```

2. GitHub 레포 → **Settings → Pages** → **Source: Deploy from a branch** →
   Branch `main` / 폴더 `/ (root)`.
3. 같은 화면 **Custom domain**에 `point-mada.com` 입력 → **Enforce HTTPS** 체크.
4. DNS 레코드 추가 (도메인 등록기관 또는 DNS 제공자에서):
   - apex `point-mada.com` → **A 레코드 4개**
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - (선택) 같은 이름 **AAAA 4개**
     `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`
   - `www.point-mada.com` → **CNAME** `p-ointer.github.io`
5. DNS 전파 후(수 분~수 시간) HTTPS 자동 발급.

---

## C. Netlify

1. https://app.netlify.com → **Add new site → Import an existing project** → `p-ointer/web`.
2. 빌드 설정:
   - **Build command**: 비움
   - **Publish directory**: `.` (레포 루트)
3. 배포 후 **Domain management → Add custom domain** → `point-mada.com`.
4. DNS:
   - Netlify DNS를 쓰면 등록기관에서 네임서버만 Netlify 것으로 변경.
   - 외부 DNS 유지 시: apex `A` → `75.2.60.5`, `www` → CNAME `<site>.netlify.app`.
5. **HTTPS**는 Let's Encrypt로 자동.

---

## 배포 전 로컬 확인

```bash
python3 -m http.server 8002   # http://localhost:8002 — 링크·이미지·앵커 확인
```

## 제출 URL

- 마케팅 URL: `https://point-mada.com/` 또는 `https://point-mada.com/mada.html`
- 지원 URL(App Store Connect / Play Console): `https://point-mada.com/support.html`
- 개인정보처리방침 URL: `https://point-mada.com/privacy.html`

(도메인 붙이기 전 임시로는 `*.pages.dev` / `*.netlify.app` / `p-ointer.github.io` 주소를
그대로 제출해도 된다.)

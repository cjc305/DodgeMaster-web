---
doc: dodgemaster/web/2026-05-18/site-spec
title: DodgeMaster 官方網站 spec — TW 1.0 首發版
created: 2026-05-18T01:00:00+08:00
updated: 2026-05-18T01:00:00+08:00
author: claude-opus-4-7
follows: docs/impl/phase-g-featured-launch-tw.md
trigger: |
  Phase G G-WEB-01 ~ G-WEB-06 需要一個完整 site spec。此檔定義所有 web 觸點的
  資訊架構、SEO、a11y、效能預算，給 Claude 全自動產生 HTML。
language: zh-Hant primary, code in English
schema_version: 1
---

## TL;DR

1. **Hosting** = GitHub Pages free（路徑 `docs/web/` → `https://cjc305.github.io/DodgeMaster/`）。1.0 不買獨立 domain。
2. **語言** = zh-Hant only。lang="zh-Hant" rel="alternate"。
3. **6 個 HTML 檔** = index / privacy / terms / support / press / changelog。共 < 30KB gzipped。
4. **效能預算**：Lighthouse mobile ≥ 90 / FCP < 1.5s / 主頁 ≤ 100KB total。
5. **a11y**：semantic HTML / aria-label / 對比 ≥ 4.5:1 / 鍵盤可導覽。
6. **SEO**：JSON-LD MobileApplication + OpenGraph + sitemap.xml + robots.txt。

## Status Table

| ID | severity | status | summary |
|---|---|---|---|
| [W-IA-01](#w-ia-01-資訊架構--url-規劃) | blocker | done | 6 HTML + sitemap + robots + .nojekyll ✅ 2026-05-18 |
| [W-HTML-01](#w-html-01-index-html-landing-page) | blocker | done | Hero / Features / How to play / 商業模式 ✅ |
| [W-HTML-02](#w-html-02-privacy-html) | blocker | done | PIPA 12 sections ✅ |
| [W-HTML-03](#w-html-03-terms-html) | blocker | done | 消保法 § 19-1 + 12 sections ✅ |
| [W-HTML-04](#w-html-04-support-html) | blocker | done | 11 FAQ + bug report template ✅ |
| [W-HTML-05](#w-html-05-press-html) | p1 | partial | zh+en boilerplate × 3 + fact sheet ✅；待 designer asset zip |
| [W-HTML-06](#w-html-06-changelog-html) | p2 | done | v1.0 + v1.1 planning ✅ |
| [W-CSS-01](#w-css-01-設計系統--css-tokens) | blocker | done | tokens + 暗色 / 亮色 / 系統字 ✅ |
| [W-SEO-01](#w-seo-01-meta--json-ld--sitemap) | p1 | done | OG + JSON-LD MobileApplication + sitemap (6 URL) + robots 4 fields ✅ |
| [W-A11Y-01](#w-a11y-01-a11y-合規) | p1 | partial | semantic HTML / aria-current / lang="zh-Hant" ✅；待部署後 axe-core 跑 |
| [W-PERF-01](#w-perf-01-lighthouse-mobile--90) | p1 | partial | 系統字 / inline CSS / no JS ✅；待部署後 Lighthouse 跑 |
| [W-DEPLOY-01](#w-deploy-01-github-pages-部署--ssl) | blocker | not_started | yves 啟用 Pages（Settings → Pages → /docs/web）|

---

## W-IA-01: 資訊架構 + URL 規劃

```yaml
id: W-IA-01
severity: blocker
status: not_started
owner: claude
eta_days: 0.1
blocker_for: [W-HTML-*]
```

### Evidence

無（從零建立）。

### Fix

URL 結構（GitHub Pages base = `https://cjc305.github.io/DodgeMaster/`）：

```
/                  → docs/web/index.html       (landing — G-WEB-01)
/privacy           → docs/web/privacy.html     (G-WEB-02)
/terms             → docs/web/terms.html       (G-WEB-03)
/support           → docs/web/support.html     (G-WEB-04)
/press             → docs/web/press.html       (G-WEB-05)
/changelog         → docs/web/changelog.html   (G-WEB-06)
/sitemap.xml       → docs/web/sitemap.xml      (W-SEO-01)
/robots.txt        → docs/web/robots.txt       (W-SEO-01)
/press-kit.zip     → docs/web/press-kit.zip    (G-MEDIA-* outputs)
/assets/...        → docs/web/assets/          (images / fonts)
/.nojekyll         → docs/web/.nojekyll        (GitHub Pages bypass Jekyll)
```

Note：GitHub Pages 預設 trailing-slash URL；以上設定確保 `/privacy` 解析到 `privacy.html`。

導覽（每頁 header 統一）：
```
Home | 隱私 | 條款 | 客服 | 媒體
```

Footer（每頁統一）：
```
© 2026 Crealize Studio. All rights reserved.
support@dodge-master.tw | Made in Taiwan
[隱私][條款][客服][變更紀錄]
```

### Acceptance criteria

- [ ] AC-1: 6 個 HTML + sitemap.xml + robots.txt + .nojekyll 全建
- [ ] AC-2: 每頁 header / footer 完全一致（用 include 或手寫但 grep diff 0）
- [ ] AC-3: 每頁有 canonical URL 標籤

### Verification

```bash
ls docs/web/*.html | wc -l
# Expected: 6

# header 一致性
for f in docs/web/*.html; do md5sum <(grep -A20 '<header' "$f"); done | awk '{print $1}' | sort -u | wc -l
# Expected: 1 (全部 header 一致)
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | GitHub Pages 對 `/privacy` 無自動 trailing-slash 解析 | 用 `privacy.html` 全名 + 設 redirect 或 _config.yml |

### Rollback

```bash
git rm -r docs/web/
```

---

## W-HTML-01: index.html — Landing page

```yaml
id: W-HTML-01
severity: blocker
status: not_started
owner: claude
eta_days: 0.3
blocker_for: [Play Console Marketing URL]
related: [G-WEB-01]
```

### Evidence

無。

### Fix

骨架：

```html
<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>DodgeMaster — 閃避是一門藝術</title>
  <meta name="description" content="DodgeMaster 是一款精準動作遊戲。100 關手作關卡，獨家時間流速操控，零強迫廣告。">
  <link rel="canonical" href="https://cjc305.github.io/DodgeMaster/">
  <link rel="icon" href="/DodgeMaster/assets/icon-192.png">

  <!-- OpenGraph -->
  <meta property="og:title" content="DodgeMaster — 閃避是一門藝術">
  <meta property="og:description" content="精準動作遊戲。100 關手作關卡 + 時間流速。">
  <meta property="og:image" content="https://cjc305.github.io/DodgeMaster/assets/og-1200x630.png">
  <meta property="og:locale" content="zh_TW">
  <meta property="og:type" content="website">

  <!-- JSON-LD MobileApplication -->
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "MobileApplication",
    "name": "DodgeMaster",
    "operatingSystem": "ANDROID, iOS",
    "applicationCategory": "GameApplication",
    "offers": { "@type": "Offer", "price": "0", "priceCurrency": "TWD" },
    "aggregateRating": null,
    "publisher": { "@type": "Organization", "name": "Crealize Studio" }
  }
  </script>

  <link rel="stylesheet" href="/DodgeMaster/assets/site.css">
</head>
<body>
  <header>
    <nav aria-label="主導覽">
      <a href="/DodgeMaster/" aria-current="page">首頁</a>
      <a href="/DodgeMaster/privacy.html">隱私</a>
      <a href="/DodgeMaster/terms.html">條款</a>
      <a href="/DodgeMaster/support.html">客服</a>
      <a href="/DodgeMaster/press.html">媒體</a>
    </nav>
  </header>

  <main>
    <section class="hero">
      <h1>DodgeMaster</h1>
      <p class="slogan">閃避是一門藝術</p>
      <p class="tagline">100 關手作關卡 · 時間流速操控 · 零強迫廣告</p>
      <div class="cta">
        <a href="https://play.google.com/store/apps/details?id=ai.smartrich.dodgemaster"
           aria-label="從 Google Play 下載">
          <img src="/DodgeMaster/assets/play-badge-zh.png" alt="Google Play 下載" width="200">
        </a>
        <a href="https://apps.apple.com/tw/app/dodgemaster/id<id>"
           aria-label="從 App Store 下載">
          <img src="/DodgeMaster/assets/app-store-badge-zh.png" alt="App Store 下載" width="200">
        </a>
      </div>
    </section>

    <section class="features" aria-labelledby="features-title">
      <h2 id="features-title">三大特色</h2>
      <ul>
        <li><strong>時間流速操控</strong> — 長按螢幕讓世界慢下來，看清每個障礙</li>
        <li><strong>100 關手作</strong> — 每關獨立設計，難度循序漸進</li>
        <li><strong>零強迫廣告</strong> — 廣告只在你主動時才播，絕不打斷遊戲</li>
      </ul>
    </section>

    <section class="screenshots" aria-labelledby="ss-title">
      <h2 id="ss-title">遊戲畫面</h2>
      <div class="grid">
        <img loading="lazy" src="/DodgeMaster/assets/scr1.webp" alt="主畫面，閃避紅色障礙" width="540" height="960">
        <img loading="lazy" src="/DodgeMaster/assets/scr2.webp" alt="時間流速效果" width="540" height="960">
        <img loading="lazy" src="/DodgeMaster/assets/scr3.webp" alt="飾品商店" width="540" height="960">
      </div>
    </section>
  </main>

  <footer>
    <p>© 2026 Crealize Studio. Made in Taiwan.</p>
    <p>
      <a href="/DodgeMaster/privacy.html">隱私</a> ·
      <a href="/DodgeMaster/terms.html">條款</a> ·
      <a href="/DodgeMaster/support.html">客服</a> ·
      <a href="/DodgeMaster/changelog.html">更新紀錄</a>
    </p>
    <p>聯絡：<a href="mailto:support@dodge-master.tw">support@dodge-master.tw</a></p>
  </footer>
</body>
</html>
```

### Acceptance criteria

- [ ] AC-1: HTML 通過 validator.w3.org 0 errors
- [ ] AC-2: og:image 1200×630 PNG 存在
- [ ] AC-3: JSON-LD valid（Google Rich Results Test pass）
- [ ] AC-4: Lighthouse mobile ≥ 90 全四項
- [ ] AC-5: 載入字節 < 100KB total（含 CSS + 3 個圖）

### Verification

```bash
# HTML validity
curl -sX POST -H "Content-Type: text/html" \
  --data-binary @docs/web/index.html \
  https://validator.w3.org/nu/?out=json \
  | jq '.messages | map(select(.type=="error")) | length'
# Expected: 0

# Lighthouse
lighthouse https://cjc305.github.io/DodgeMaster/ --only-categories=performance,accessibility,best-practices,seo \
  --output=json --output-path=/tmp/lh-home.json --chrome-flags="--headless"
cat /tmp/lh-home.json | jq '.categories | to_entries | map(.value.score) | min'
# Expected: ≥ 0.9
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | Play / App Store badge 圖未取得 | 使用 Google / Apple 官方 badge generator |
| R-2 | 字型載入慢 | 用系統字型（PingFang TC / Noto Sans TC fallback），不嵌外部 webfont |

### Rollback

```bash
git rm docs/web/index.html
```

---

## W-HTML-02: privacy.html

```yaml
id: W-HTML-02
severity: blocker
status: not_started
owner: claude
eta_days: 0.3
blocker_for: [雙平台上架]
related: [G-WEB-02, G-LEGAL-01]
```

### Evidence

```
FILE: docs/privacy/index.html  (Phase F F-S04 staged，已有框架；本 issue 完成 zh-Hant + PIPA)
```

### Fix

完整 zh-Hant 內容見 [G-WEB-02](../impl/phase-g-featured-launch-tw.md#g-web-02-privacy-policy-zh-hant-完整版) 規格。10 個段落結構必含：

1. 適用範圍與更新
2. 收集資料種類（PIPA § 8）
3. 蒐集目的
4. 使用 / 保存期間
5. 共享對象
6. 使用者權利（PIPA § 11）
7. 兒童（13 歲以下）
8. 安全措施 + 跨境傳輸
9. 聯絡方式
10. 變更通知 + 司法管轄

```html
<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>隱私權政策 — DodgeMaster</title>
  <meta name="description" content="DodgeMaster 隱私權政策。依中華民國個人資料保護法 § 8 告知。">
  <link rel="canonical" href="https://cjc305.github.io/DodgeMaster/privacy.html">
  <link rel="stylesheet" href="/DodgeMaster/assets/site.css">
</head>
<body>
  <!-- header 同 index.html -->

  <main>
    <article>
      <h1>隱私權政策</h1>
      <p><em>最後更新：2026-XX-XX</em></p>

      <nav aria-label="本頁目錄">
        <ol>
          <li><a href="#scope">適用範圍</a></li>
          <li><a href="#data-types">收集資料種類</a></li>
          <!-- ... 10 項 ... -->
        </ol>
      </nav>

      <section id="scope">
        <h2>1. 適用範圍與更新</h2>
        <p>本政策適用於 Crealize Studio（下稱「我們」）開發之 DodgeMaster 應用程式...</p>
      </section>

      <section id="data-types">
        <h2>2. 收集資料種類（依個人資料保護法 § 8）</h2>
        <p>我們<strong>不主動收集</strong>您的姓名、生日、地址等個人資料...</p>
        <h3>2.1 透過 Google AdMob 廣告 SDK</h3>
        <ul>
          <li>裝置廣告識別碼（GAID / IDFA）</li>
          <li>概略地點（依 IP 推算國家）</li>
          <li>裝置型號、作業系統版本、螢幕尺寸</li>
        </ul>
        <p>詳見 <a href="https://policies.google.com/technologies/ads" rel="external nofollow">Google 廣告隱私政策</a>。</p>
        <!-- ... -->
      </section>

      <!-- ... 其他 8 段 ... -->
    </article>
  </main>

  <!-- footer 同 index.html -->
</body>
</html>
```

### Acceptance criteria

- [ ] AC-1: 10 個 H2 段落齊全
- [ ] AC-2: 含「個人資料保護法」「§ 8」「§ 11」3 個關鍵字
- [ ] AC-3: 含「AdMob」「Google」「廣告識別碼」3 個關鍵字
- [ ] AC-4: 與 PrivacyInfo.xcprivacy（F-S08）+ Play Data Safety Form（F-S05）三處宣告一致
- [ ] AC-5: 文中所有 § / 條款引用為真實條文

### Verification

```bash
curl -s https://cjc305.github.io/DodgeMaster/privacy.html | grep -c '<h2'
# Expected: 10

curl -s https://cjc305.github.io/DodgeMaster/privacy.html | grep -ciE "個人資料保護法|§\s*8|§\s*11"
# Expected: ≥ 3

curl -s https://cjc305.github.io/DodgeMaster/privacy.html | grep -ciE "AdMob|GAID|廣告識別"
# Expected: ≥ 3
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 法條引用錯誤 | 比對 [全國法規資料庫](https://law.moj.gov.tw/LawClass/LawAll.aspx?pcode=I0050021) |
| R-2 | 與 ASC / Play 不一致 | F-S05 + F-S08 + 本檔同 PR 變更 |

### Rollback

```bash
git rm docs/web/privacy.html
```

---

## W-HTML-03: terms.html

```yaml
id: W-HTML-03
severity: blocker
status: not_started
owner: claude
eta_days: 0.2
blocker_for: [雙平台上架]
related: [G-WEB-03, G-LEGAL-02]
```

### Evidence

無。

### Fix

完整 zh-Hant 內容見 [G-WEB-03](../impl/phase-g-featured-launch-tw.md#g-web-03-terms-of-service-zh-hant)。12 個段落結構。

骨架同 W-HTML-02。重點段落：

```html
<section id="refund">
  <h2>4. 內購與退款</h2>
  <p>依消費者保護法 § 19-1，本應用程式之內購商品（DodgeCoin / FullPack）屬「非有形提供之數位內容服務」...</p>
  <p>您按下「確認購買」按鈕即視為同意排除 7 日鑑賞期...</p>
  <p>如需申請退款，請使用以下管道：</p>
  <ul>
    <li>Google Play：<a href="https://play.google.com/store/account/orderhistory" rel="external">訂單記錄</a></li>
    <li>Apple App Store：<a href="https://reportaproblem.apple.com" rel="external">回報問題</a></li>
    <li>直接聯絡：<a href="mailto:support@dodge-master.tw">support@dodge-master.tw</a></li>
  </ul>
</section>

<section id="jurisdiction">
  <h2>10. 適用法律與管轄</h2>
  <p>本條款依中華民國法律解釋；雙方因本條款發生爭訟，合意以台灣台北地方法院為第一審管轄法院...</p>
</section>
```

### Acceptance criteria

- [ ] AC-1: 12 個 H2 段落齊全
- [ ] AC-2: 含「消費者保護法 § 19-1」「鑑賞期」「中華民國」3 個關鍵字
- [ ] AC-3: 退款管道含 Google Play / Apple / Email 三個連結

### Verification

```bash
curl -s https://cjc305.github.io/DodgeMaster/terms.html | grep -c '<h2'
# Expected: 12

curl -s https://cjc305.github.io/DodgeMaster/terms.html | grep -ciE "消費者保護法|§\s*19-1|鑑賞期"
# Expected: ≥ 3
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 條款違反 § 12（消保法不可排除事項）| 保留法定權利條款 |

### Rollback

```bash
git rm docs/web/terms.html
```

---

## W-HTML-04: support.html

```yaml
id: W-HTML-04
severity: blocker
status: not_started
owner: claude
eta_days: 0.3
blocker_for: [Play / ASC Support URL 必填]
related: [G-WEB-04]
```

### Evidence

無。

### Fix

```html
<main>
  <h1>客服中心</h1>

  <section>
    <h2>聯絡我們</h2>
    <p>Email: <a href="mailto:support@dodge-master.tw">support@dodge-master.tw</a></p>
    <p>一般問題回覆時間：48 小時內</p>
  </section>

  <section id="faq">
    <h2>常見問題</h2>

    <details>
      <summary>我的金幣不見了？</summary>
      <p>遊戲資料儲存於本機，如解除安裝再重灌，所有資料將遺失（依設計，本機保護隱私）...</p>
    </details>

    <details>
      <summary>看完廣告沒給獎勵？</summary>
      <p>請確認以下幾項：1. 廣告是否完整播放完畢？2. 網路是否斷線？3. 是否已達每日 10 次上限？...</p>
    </details>

    <!-- 共 10+ 個 details -->
  </section>

  <section id="bug-report">
    <h2>回報 Bug</h2>
    <p>請寄信至 support@dodge-master.tw 並附上：</p>
    <ul>
      <li>裝置型號 + Android / iOS 版本</li>
      <li>App 版本（設定 → 關於）</li>
      <li>重現步驟</li>
      <li>截圖 / 錄影（可選）</li>
    </ul>
  </section>

  <section id="refund-link">
    <h2>退款</h2>
    <p>詳見 <a href="/DodgeMaster/terms.html#refund">使用條款 § 4</a>。</p>
  </section>
</main>
```

### Acceptance criteria

- [ ] AC-1: ≥ 10 個 `<details>` FAQ
- [ ] AC-2: 含真實 email（測試信驗證可收）
- [ ] AC-3: bug report 模板 4 個欄位齊全

### Verification

```bash
curl -s https://cjc305.github.io/DodgeMaster/support.html | grep -c '<details'
# Expected: ≥ 10

# 測試信
echo "test" | mail -s "DodgeMaster support test" support@dodge-master.tw
# 人工確認收到
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 24h 無回 = 1★ review | Gmail filter 自動標 + Telegram push |

### Rollback

```bash
git rm docs/web/support.html
```

---

## W-HTML-05: press.html

```yaml
id: W-HTML-05
severity: p1
status: not_started
owner: claude
eta_days: 0.3
blocker_for: [媒體 outreach]
related: [G-WEB-05]
```

### Evidence

無。

### Fix

```html
<main>
  <h1>媒體中心 / Press</h1>
  <p lang="en">Press kit for DodgeMaster — a precision platformer with time slow mechanic.</p>

  <section>
    <h2>關於 DodgeMaster / About</h2>
    <p class="boilerplate-30">DodgeMaster 是一款精準動作遊戲，由台灣獨立工作室 Crealize 製作。</p>
    <p class="boilerplate-50" lang="en">DodgeMaster is a precision platformer with original time-slow mechanic, made by indie studio Crealize Studio in Taiwan.</p>
    <p class="boilerplate-150" lang="en">DodgeMaster is a precision platformer for Android and iOS. Players navigate 100 hand-crafted levels by tapping to jump and long-pressing to slow time. The game features a cosmetic-only DodgeCoin economy with zero forced ads, designed to respect player attention. Made by indie studio Crealize Studio in Taiwan.</p>
  </section>

  <section>
    <h2>素材下載 / Assets</h2>
    <ul>
      <li><a href="/DodgeMaster/press-kit.zip" download>完整 Press Kit ZIP (~30MB)</a></li>
      <li>個別素材：
        <ul>
          <li><a href="/DodgeMaster/assets/icon-1024.png">App Icon 1024×1024</a></li>
          <li><a href="/DodgeMaster/assets/feature-graphic-1024x500.png">Feature Graphic</a></li>
          <li><a href="/DodgeMaster/assets/screenshots.zip">6 張截圖 ZIP</a></li>
          <li><a href="https://youtu.be/...">官方 Trailer (YouTube)</a></li>
        </ul>
      </li>
    </ul>
  </section>

  <section>
    <h2>事實表 / Fact Sheet</h2>
    <table>
      <tr><th>遊戲名稱 / Title</th><td>DodgeMaster</td></tr>
      <tr><th>開發者 / Developer</th><td>Crealize Studio (Taiwan)</td></tr>
      <tr><th>平台 / Platform</th><td>Android 7.1+, iOS 15+</td></tr>
      <tr><th>類型 / Genre</th><td>Precision platformer / Arcade</td></tr>
      <tr><th>價格 / Price</th><td>免費（含 IAP）/ Free (with IAP)</td></tr>
      <tr><th>語言 / Language</th><td>繁體中文（1.0）</td></tr>
      <tr><th>分級 / Rating</th><td>PEGI 3 / ESRB Everyone</td></tr>
      <tr><th>上線日 / Launch</th><td>2026-XX-XX</td></tr>
    </table>
  </section>

  <section>
    <h2>聯絡 / Contact</h2>
    <p>Press inquiries: <a href="mailto:press@dodge-master.tw">press@dodge-master.tw</a></p>
    <p>素材使用授權：媒體可自由使用本頁素材報導，無需事先通知。</p>
  </section>
</main>
```

### Acceptance criteria

- [ ] AC-1: 30 / 50 / 150 字 boilerplate 齊全（zh + en）
- [ ] AC-2: press-kit.zip 可下載（≤ 50MB）
- [ ] AC-3: 事實表完整 8 列
- [ ] AC-4: 雙語（zh 主、en 副）

### Verification

```bash
curl -sI https://cjc305.github.io/DodgeMaster/press-kit.zip | head -1
# Expected: 200

curl -s https://cjc305.github.io/DodgeMaster/press.html | grep -c "boilerplate-"
# Expected: 3
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | zip 超過 GitHub Pages 100MB 上限 | 用 GitHub Releases 託管 |

### Rollback

```bash
git rm docs/web/press.html docs/web/press-kit.zip
```

---

## W-HTML-06: changelog.html

```yaml
id: W-HTML-06
severity: p2
status: not_started
owner: claude
eta_days: 0.1
blocker_for: [透明度]
related: [G-WEB-06]
```

### Evidence

無。

### Fix

```html
<main>
  <h1>更新紀錄</h1>

  <article>
    <h2>v1.0.0 — 2026-XX-XX</h2>
    <ul>
      <li>✨ 初版上線（Taiwan 區）</li>
      <li>🎮 100 關手作關卡 + 60 秒 FTUE</li>
      <li>⏱️ Time Slow 時間流速操控</li>
      <li>💎 14 種飾品（球皮膚 / 拖尾 / 背景）</li>
      <li>🎬 完整買斷包 NT$ 600（≈ $18.88）</li>
      <li>🎯 30 個成就</li>
      <li>📱 Android 7.1+ / iOS 15+ 支援</li>
    </ul>
  </article>

  <!-- 每次 release 在頂部加新 article -->
</main>
```

### Acceptance criteria

- [ ] AC-1: 與 Play Console release notes / ASC What's New 同步
- [ ] AC-2: 每次 release 自動加新 entry（可手動或 CI）

### Verification

```bash
curl -s https://cjc305.github.io/DodgeMaster/changelog.html | grep -c "<article"
# Expected: ≥ 1
```

### Risks

無關鍵。

### Rollback

```bash
git rm docs/web/changelog.html
```

---

## W-CSS-01: 設計系統 / CSS tokens

```yaml
id: W-CSS-01
severity: blocker
status: not_started
owner: claude
eta_days: 0.25
blocker_for: [W-HTML-*]
```

### Evidence

無。

### Fix

```css
/* FILE: docs/web/assets/site.css */
:root {
  --color-bg: #0e0e10;
  --color-fg: #f3f3f5;
  --color-accent: #ff5b6e;
  --color-muted: #8a8a93;

  --font-system: -apple-system, BlinkMacSystemFont, "PingFang TC", "Microsoft JhengHei", "Noto Sans TC", sans-serif;

  --space-1: 4px;
  --space-2: 8px;
  --space-3: 16px;
  --space-4: 24px;
  --space-5: 40px;
  --space-6: 64px;

  --radius-sm: 4px;
  --radius-md: 12px;
  --radius-lg: 24px;

  --max-w: 720px;
}

@media (prefers-color-scheme: light) {
  :root {
    --color-bg: #ffffff;
    --color-fg: #18181b;
    --color-accent: #d4364c;
    --color-muted: #6b6b73;
  }
}

* { box-sizing: border-box; }
html { -webkit-text-size-adjust: 100%; scroll-behavior: smooth; }
body {
  margin: 0;
  font-family: var(--font-system);
  background: var(--color-bg);
  color: var(--color-fg);
  line-height: 1.6;
}
main { max-width: var(--max-w); margin: 0 auto; padding: var(--space-4) var(--space-3); }
header, footer { padding: var(--space-3); border-bottom: 1px solid color-mix(in srgb, var(--color-fg) 10%, transparent); }
footer { border-top: 1px solid color-mix(in srgb, var(--color-fg) 10%, transparent); border-bottom: 0; text-align: center; }
nav a { color: var(--color-fg); text-decoration: none; margin-right: var(--space-3); }
nav a[aria-current="page"] { border-bottom: 2px solid var(--color-accent); }
h1, h2, h3 { line-height: 1.25; }
a { color: var(--color-accent); }
a:focus-visible { outline: 2px solid var(--color-accent); outline-offset: 2px; }
.hero { text-align: center; padding: var(--space-6) 0; }
.slogan { font-size: 1.5rem; color: var(--color-accent); font-weight: 600; }
.cta { display: flex; gap: var(--space-3); justify-content: center; flex-wrap: wrap; }
.features ul, .screenshots .grid { padding: 0; list-style: none; }
.screenshots .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: var(--space-3); }
.screenshots img { width: 100%; height: auto; border-radius: var(--radius-md); }
details { margin: var(--space-3) 0; padding: var(--space-3); border-radius: var(--radius-md); background: color-mix(in srgb, var(--color-fg) 5%, transparent); }
summary { cursor: pointer; font-weight: 600; }
table { width: 100%; border-collapse: collapse; }
th, td { padding: var(--space-2); text-align: left; border-bottom: 1px solid color-mix(in srgb, var(--color-fg) 10%, transparent); }
@media (max-width: 480px) {
  main { padding: var(--space-3) var(--space-2); }
  .slogan { font-size: 1.25rem; }
}
@media (prefers-reduced-motion: reduce) {
  html { scroll-behavior: auto; }
}
```

### Acceptance criteria

- [ ] AC-1: 暗色 / 亮色都正常（用 macOS Safari 切系統 appearance 測）
- [ ] AC-2: < 480px 視窗 layout 不破
- [ ] AC-3: prefers-reduced-motion 尊重
- [ ] AC-4: 對比度 ≥ 4.5:1（用 WebAIM contrast checker）
- [ ] AC-5: 檔案 ≤ 4KB gzipped

### Verification

```bash
gzip -c docs/web/assets/site.css | wc -c
# Expected: ≤ 4096

# 對比度（用 puppeteer 或人工）
# 暗色：#0e0e10 (bg) vs #f3f3f5 (fg) = ratio ~16.7:1 ✅
# 亮色：#ffffff vs #18181b = ratio ~17.6:1 ✅
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | PingFang TC 在非 Apple 平台不存在 | fallback chain 已含 Microsoft JhengHei + Noto Sans TC + sans-serif |

### Rollback

```bash
git rm docs/web/assets/site.css
```

---

## W-SEO-01: Meta / JSON-LD / sitemap / robots

```yaml
id: W-SEO-01
severity: p1
status: not_started
owner: claude
eta_days: 0.25
blocker_for: [Google index]
```

### Evidence

無。

### Fix

```xml
<!-- FILE: docs/web/sitemap.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://cjc305.github.io/DodgeMaster/</loc><changefreq>monthly</changefreq><priority>1.0</priority></url>
  <url><loc>https://cjc305.github.io/DodgeMaster/privacy.html</loc><changefreq>yearly</changefreq><priority>0.5</priority></url>
  <url><loc>https://cjc305.github.io/DodgeMaster/terms.html</loc><changefreq>yearly</changefreq><priority>0.5</priority></url>
  <url><loc>https://cjc305.github.io/DodgeMaster/support.html</loc><changefreq>monthly</changefreq><priority>0.7</priority></url>
  <url><loc>https://cjc305.github.io/DodgeMaster/press.html</loc><changefreq>monthly</changefreq><priority>0.7</priority></url>
  <url><loc>https://cjc305.github.io/DodgeMaster/changelog.html</loc><changefreq>weekly</changefreq><priority>0.6</priority></url>
</urlset>
```

```
# FILE: docs/web/robots.txt
User-agent: *
Allow: /
Sitemap: https://cjc305.github.io/DodgeMaster/sitemap.xml
```

每頁 JSON-LD（index 為主，其他簡化）：見 W-HTML-01 範例。

### Acceptance criteria

- [ ] AC-1: sitemap.xml W3C valid
- [ ] AC-2: robots.txt 4 行（依官方規範，不含 Feed-* 等自創欄位 — 見 feedback_seo_robots_txt.md）
- [ ] AC-3: Google Rich Results Test 對 index.html pass
- [ ] AC-4: 提交到 Google Search Console

### Verification

```bash
# sitemap valid
xmllint --noout https://cjc305.github.io/DodgeMaster/sitemap.xml && echo OK

# robots.txt 不含自創欄位
curl -s https://cjc305.github.io/DodgeMaster/robots.txt \
  | grep -v "^#" | awk -F: '{print $1}' | sort -u
# Expected: 只有 User-agent / Allow / Disallow / Sitemap 四個
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | robots.txt 加 Feed-* / Custom-* 自創欄位 → Google trust 下降 | 嚴格 4 欄位（feedback_seo_robots_txt.md 教訓）|

### Rollback

```bash
git rm docs/web/sitemap.xml docs/web/robots.txt
```

---

## W-A11Y-01: a11y 合規

```yaml
id: W-A11Y-01
severity: p1
status: not_started
owner: claude
eta_days: 0.25
blocker_for: [Lighthouse score, Featured a11y]
```

### Evidence

無。

### Fix

每頁強制：
- `<html lang="zh-Hant">`
- semantic：`<header> <nav> <main> <article> <section> <footer>`
- 每個 img 有 alt
- 每個 button / link 有 aria-label（icon-only）
- focus visible
- 鍵盤可導覽（Tab / Enter / Space）
- 對比度 ≥ 4.5:1
- prefers-reduced-motion

### Acceptance criteria

- [ ] AC-1: axe-core 0 critical
- [ ] AC-2: Lighthouse a11y ≥ 95
- [ ] AC-3: 用 VoiceOver / TalkBack 試讀全站可懂

### Verification

```bash
npm i -g @axe-core/cli
axe https://cjc305.github.io/DodgeMaster/ --tags wcag2aa
# Expected: 0 violations
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 顏色對比不夠 | CSS tokens 已確認 4.5:1+ |

### Rollback

無 — a11y 不可退步。

---

## W-PERF-01: Lighthouse mobile ≥ 90

```yaml
id: W-PERF-01
severity: p1
status: not_started
owner: claude
eta_days: 0.25
blocker_for: [Featured 印象]
```

### Evidence

無。

### Fix

- HTML inline critical CSS（< 4KB）+ 其餘 lazy
- 圖用 WebP，提供 width/height（防 CLS）
- `loading="lazy"` 在非首屏圖
- 無 JS（純 HTML/CSS）→ TBT = 0
- 字型用系統字（無外部 webfont 載入）
- 各頁 ≤ 100KB total bytes
- Cache-Control: max-age=86400（GitHub Pages 預設）

### Acceptance criteria

- [ ] AC-1: 每頁 Lighthouse mobile Performance ≥ 90
- [ ] AC-2: FCP < 1.5s（3G slow）
- [ ] AC-3: LCP < 2.5s
- [ ] AC-4: CLS < 0.1
- [ ] AC-5: 每頁 total bytes ≤ 100KB

### Verification

```bash
for page in "" "/privacy.html" "/terms.html" "/support.html" "/press.html"; do
  url="https://cjc305.github.io/DodgeMaster${page}"
  lighthouse "$url" --only-categories=performance \
    --output=json --output-path=/tmp/lh-$(basename ${page:-home}).json \
    --chrome-flags="--headless" --emulated-form-factor=mobile --throttling-method=devtools
  score=$(cat /tmp/lh-$(basename ${page:-home}).json | jq '.categories.performance.score')
  echo "$page: $score"
done
# Expected: 全部 ≥ 0.9

# Bytes 量
for page in "" "/privacy.html" "/terms.html" "/support.html" "/press.html"; do
  curl -s -o /dev/null -w "%{size_download}\n" "https://cjc305.github.io/DodgeMaster${page}"
done
# Expected: 全部 < 100000
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 截圖 PNG 太大拖累首屏 | 用 WebP + responsive srcset |

### Rollback

無 — perf 不可退步。

---

## W-DEPLOY-01: GitHub Pages 部署 + SSL

```yaml
id: W-DEPLOY-01
severity: blocker
status: not_started
owner: yves
eta_days: 0.1
blocker_for: [所有 G-WEB-*]
```

### Evidence

```bash
$ ls docs/.nojekyll 2>/dev/null
# 不存在
```

### Fix

```bash
# 1. 建 .nojekyll
touch docs/web/.nojekyll

# 2. yves 在 GitHub repo settings 啟用：
#    Settings → Pages → Source: Deploy from branch
#    Branch: main / Folder: /docs/web   (不是 /docs root，避開 spec / privacy 既有檔)
#
#    或者：建 docs/index.html 作為 redirect
#    Branch: main / Folder: /docs
```

考量到既有 `docs/privacy/` 已有檔，建議：

```
方案 A — 新路徑（推薦）：
  Branch: main / Folder: /docs/web
  URL base: https://cjc305.github.io/DodgeMaster/
  好處：與既有 docs/ engineering specs 完全分離

方案 B — 既有 docs/ 根：
  必須把 web/* 搬到 docs/ 根，與 spec docs 共存 → 混亂
```

選方案 A。

### Acceptance criteria

- [ ] AC-1: yves Settings → Pages → branch=main, folder=/docs/web
- [ ] AC-2: 5 分鐘後 `curl -sI https://cjc305.github.io/DodgeMaster/` → 200
- [ ] AC-3: HTTPS 自動啟用（GitHub Pages 預設 Let's Encrypt）
- [ ] AC-4: docs/web/.nojekyll 存在（避開 Jekyll 處理）

### Verification

```bash
URL="https://cjc305.github.io/DodgeMaster/"
curl -sI "$URL" | head -1
# Expected: HTTP/2 200

# SSL
curl -sI "$URL" | grep -i strict-transport
# Expected: 含 strict-transport-security
```

### Risks

| risk_id | description | mitigation |
|---|---|---|
| R-1 | 啟用 5 分鐘後仍 404 | GitHub Pages 首次部署有 ~10 min 延遲，等待 |
| R-2 | repo 是 private | Pages 對 free user 限 public repo |

### Rollback

Settings → Pages → Source: None → 即關閉。

---

## Cross-references

### 上游
- [docs/impl/phase-g-featured-launch-tw.md](../impl/phase-g-featured-launch-tw.md) — G-WEB-* 觸發此檔
- [docs/impl/phase-f-submission-optimization.md](../impl/phase-f-submission-optimization.md) — F-S04 privacy 既有起點

### 規則 / 教訓
- [feedback_seo_robots_txt.md](~/repos/Claude-Chronicle/_core/memory/shared/feedback_seo_robots_txt.md) — robots.txt 4 欄位限制
- [feedback_seo_aio_checklist.md](~/repos/Claude-Chronicle/_core/memory/shared/feedback_seo_aio_checklist.md) — SEO 主規範
- [feedback_cloudflare_universal_ssl_subdomain_depth.md](~/repos/Claude-Chronicle/_core/memory/shared/feedback_cloudflare_universal_ssl_subdomain_depth.md) — 1.0 不用 CF（GitHub Pages 已 SSL）

---

## Change log

| date | author | change |
|---|---|---|
| 2026-05-18 | claude-opus-4-7 | initial — 12 issues × 6-section matrix；對應 Phase G G-WEB-01~06 + W-CSS-01 / W-SEO-01 / W-A11Y-01 / W-PERF-01 / W-DEPLOY-01 |

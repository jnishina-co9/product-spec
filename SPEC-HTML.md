# プロダクト仕様書HTML化 デザイン設計仕様（SPEC-HTML.md）

本ファイルは、プロダクト仕様書（SPEC.md 等）をHTMLに変換する際のデザイン設計仕様書です。
AIはこのファイルを読み込み、定義されたデザインシステムに従って spec.html を生成してください。

---

## 出力ファイルのルール

| 項目 | ルール |
|---|---|
| ファイル名 | `spec.html`（固定） |
| 配置場所 | 変換対象の `.md` ファイルと同じフォルダ内 |
| 構成 | 1ファイル完結（CSS・JS すべて `<style>` / `<script>` タグ内に埋め込む） |

---

## デザインシステム

定義された以下のデザインシステムに従って spec.html を生成してください。

```css
:root {
  /* 4色パレット */
  --color-main:         #007E66;   /* メインカラー（ダークグリーン） */
  --color-base:         #FFFFFF;   /* ベースカラー（白） */
  --color-sub:          #E7EAE7;   /* サブカラー（ライトグレーグリーン）※ページ背景にも使用 */
  --color-accent:       #F25A86;   /* アクセントカラー（ピンク）※矢印などの差し色専用 */
  --color-text:         #333333;   /* テキストカラー */

  /* 派生色（4色から計算） */
  --color-text-sub:     #6B7B6B;   /* 補助テキスト */
  --color-border:       #D4D9D4;   /* ボーダー */
  --color-main-light:   #E8F4F1;   /* メインカラーの極薄（ホバー背景） */
  --color-accent-light: #FDEEF4;   /* アクセントカラーの極薄 */

  /* フォント（Google Fonts + システムフォントフォールバック） */
  --font-base:    'Noto Sans JP', 'Segoe UI', 'Hiragino Kaku Gothic ProN', 'Hiragino Sans', 'Yu Gothic', 'Meiryo', sans-serif;
  --font-accent:  'Outfit', 'Segoe UI', -apple-system, sans-serif;

  /* レイアウト */
  --sidebar-width: 260px;
}
```

### デザイン原則
- 枠線（border）・影（box-shadow）・角丸（border-radius）は **原則使用しない（フラットデザイン）**
- 色は4色パレット＋派生色のみ使用。それ以外の色を追加しない
- スペーシングは 8px の倍数を厳格に適用
- アクセントカラー（ピンク）は「アコーディオンの矢印アイコン」のみに使用する（背景・ボーダーへの多用禁止）

---

## レイアウト構成

```
┌─────────────────────────────────────────────────┐
│  PROGRESS BAR（ページ上部 固定 3px）              │
├──────────┬──────────────────────────────────────┤
│          │  PAGE HEADER（--color-main 背景）     │
│ SIDEBAR  │  タイトル / メタ情報バッジ              │
│ （固定）  ├──────────────────────────────────────┤
│          │  CONTENT                              │
│          │  ├─ Section 0: 基本情報             │
│ ナビ目次  │  │   動作ルール・注意点（縦並び）         │
│          │  ├─ Section 1〜N: 各セクション         │
│          │  │   アコーディオン群                  │
│          │  │   動作ルール・注意点ブロック           │
│          │  └─ Section N: おわりに                │
│          ├──────────────────────────────────────┤
│          │  PAGE FOOTER（コピーライトのみ）        │
└──────────┴──────────────────────────────────────┘
```

- サイドバー下部のフッター（作成日・コピーライト）は **配置しない**
- コピーライトはメインカラムの PAGE FOOTER にのみ表示する

---

## 文字サイズ設計（視覚的階層）

| レベル | 要素 | デスクトップ | スマホ（767px以下） | ウェイト |
|---|---|---|---|---|
| **L1（大見出し）** | セクション見出し（.section-title） | 20px | 16px | 700 |
| **L2（中見出し）** | アコーディオンタイトル / フロータイトル | 16px | 14px | 500 |
| **L3（本文）** | アコーディオン本文 / ブロック本文 | 16px | 14px | 400 |
| **L4（ラベル）** | ナビ項目 / テーブル項目名 | 14px | 13px | 400 |
| **L5（極小）** | タグ / カテゴリラベル / フッター | 12px | 11px | 700（ラベル） |

- 章番号（.section-num）は **24px（デスクトップ）/ 18px（スマホ）** で大きく表示し、`opacity: 0.4` で半透明にして装飾として機能させる

---

## セクション変換ルール

### Section 0（基本情報）
- 見出しは「基本情報」とする
- サイドバー目次のラベルも「00 基本情報」とする
- アプリカード枠内のタイトル（`.app-card__title`）は「Quick Summary」とする（英語表記）
- `<table>` でキーバリュー形式に変換
- 技術スタックは `.tech-tag` スパンで個別表示
- URLは `<a class="url-link">` でリンク化
- 直後に「アプリの動作ルール」「ご利用時の注意点」のブロックを `.block-grid` クラスで囲み配置（PCでは2カラム、タブレット以下で縦並びに自動切り替え）

### Section 1〜N（通常セクション）
- 通常セクション（Section 1〜N）のアコーディオンやブロックの数は固定ではありません。元ファイル（SPEC.md）に記述されている項目数と完全に一致させて、HTML上の要素を動的に増減（生成）させてください。

```html
<section class="spec-section" id="sec-{N}" aria-labelledby="title-{N}">
  <div class="section-header">
    <span class="section-num">0{N}</span>
    <h2 class="section-title" id="title-{N}">{セクション名}</h2>
  </div>
  <div class="section-body">
    <!-- アコーディオン群 -->
    <!-- 必要に応じて spec-block 群 -->
  </div>
</section>
```

### 最終セクション（おわりに）
- アコーディオンや見出しリストは使用しない。
- 背景色を白（`var(--color-base)`）にした単一のブロック内で、段落（`<p>`タグ）を分けて文章のみを記述する。
- サイドバーの目次では「Architecture」枠ではなく、「Conclusion」枠の下に独立して配置する。

---

## アコーディオンのタグ設計

タグの色はすべて **モノトーン（薄いグレー背景 + 濃いグレー文字）に統一** する。

```css
.accordion__tag {
  font-family: var(--font-accent);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  padding: 3px 8px;
  white-space: nowrap;
  background: var(--color-sub);
  color: var(--color-text-sub);
}
```

| タグクラス | 用途 | 表示テキスト例 |
|---|---|---|
| `accordion__tag--feature` | 機能説明 | 機能 |
| `accordion__tag--tech` | 技術・実装 | コア技術 / 描画処理 / ライブラリ / セキュリティ |
| `accordion__tag--design` | デザイン・UX | デザイン / UX / レスポンシブ |
| `accordion__tag--rationale` | 背景・理由 | 背景・動機 / 設計判断 |

---

## spec-block（動作ルール・注意点）の設計ルール

すべての `spec-block` は **背景色なし（transparent）・濃いグレーの縦線のみ** で統一する。複数並べる場合は横並び用のコンテナ（`.block-grid`）に入れるか、単独で縦並びにする。

```css
/* PCで2カラム、タブレット以下で1カラムにするコンテナ */
.block-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
}

.spec-block {
  padding: 16px 24px;
  background: transparent;
  border-left: 4px solid var(--color-text);
}

.spec-block__label {
  font-family: var(--font-accent);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  margin-bottom: 10px;
  color: var(--color-text);
}

/* 単独の spec-block が縦に連続する場合の余白 */
.spec-block + .spec-block {
  margin-top: 24px;
}

/* 先行要素（アコーディオン群、アプリカード等）の直後のブロックやグリッドには自動で上部余白を付与 */
.accordion + .spec-block,
.accordion + .block-grid,
.app-card + .spec-block,
.app-card + .block-grid {
  margin-top: 32px;
}
```

| ブロッククラス | 用途 |
|---|---|
| `spec-block--premise` | アプリの動作ルール |
| `spec-block--constraint` | ご利用時の注意点 |
| `spec-block--future` | 将来の展望 |

---

## デモ動画の設計ルール

仕様書にデモ動画を掲載する場合は、以下のルールに従って配置・コーディングを行います。

### 配置場所
- セクション 1（アプリ概要）の最下部、アコーディオン群の外側（`</div><!-- section-bodyの終わり -->` の直前）に配置し、常に表示されるようにします。

### HTML構成
- `.spec-block` クラスを使用し、見出しラベルを「デモ動画」とします。
- 動画は `.demo-video-wrapper` で包み、遅延ロードと自動再生用の属性（`preload="metadata" autoplay loop muted playsinline controls`）を付与します。

```html
<div class="spec-block">
  <div class="spec-block__label">デモ動画</div>
  <div class="demo-video-wrapper">
    <video src="demo.mp4" preload="metadata" autoplay loop muted playsinline controls>
      お使いのブラウザは動画の再生に対応していません。
    </video>
  </div>
</div>
```

### CSSスタイル
- 既存のデザインシステム（`--color-border`）と整合させ、中央寄せで大きめに表示します。

```css
.demo-video-wrapper {
  position: relative;
  width: 100%;
  max-width: 800px;
  margin: 16px auto 0 auto;
}

.demo-video-wrapper video {
  width: 100%;
  height: auto;
  display: block;
  border: 1px solid var(--color-border);
}
```

---

## サイドバー（目次）の構成ルール

- 左上のブランドタイトルは `href="#"` を設定した `<a>` タグ（`.sidebar__app-name`）として実装し、ページ最上部へのリンクとする
- タイトルにはホバー時に不透明度を下げる（`opacity: 0.7`）などのホバーエフェクトを設定し、リンクであることを明示する
- セクション番号は `nav__item-num` で2桁ゼロ埋め（00, 01, 02...）
- セクショングループラベル（Overview / Requirements 等）とメニュー項目のフォントは `--font-accent`（Outfit）を明示的に指定する
- サイドバー下部のフッター（作成日・コピーライト）は **配置しない**
- スクロール位置に応じて `is-active` クラスを自動付与（IntersectionObserver）

---

## アコーディオンの動作・余白設計

```css
/* 開閉矢印：アクセントカラー（ピンク）を使用 */
.accordion__icon { color: var(--color-accent); }

/* ホバー背景：薄いグリーンで統一 */
.accordion__trigger:hover { background: var(--color-main-light); }

/* 展開時の内側余白（上部12px確保） */
.accordion__body { padding: 12px 24px 24px; }

/* アコーディオン同士の間隔 */
.section-body { display: flex; flex-direction: column; gap: 16px; }
```

---

## フッター設計

```css
.page-footer {
  padding: 32px 64px 48px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: var(--color-text-sub);
}
```

```html
<footer class="page-footer" role="contentinfo">
  <span>© {年} {著作者名}</span>
  <span>本資料の転載・複製・改変を禁止します</span>
</footer>
```

---

## レスポンシブ対応（デスクトップファースト 4段階）

| 段階 | ブレイクポイント | 対応内容 |
|---|---|---|
| **XL（大画面）** | `min-width: 1440px` | サイドバー幅280px、余白・コンテンツ幅を拡大 |
| **L（デスクトップ）** | 1024〜1439px | **ベース（何もなし）** |
| **M（タブレット）** | `max-width: 1023px` | サイドバー幅220px、`block-grid` を1カラム化 |
| **S（スマホ）** | `max-width: 767px` | サイドバーを画面外に格納、右上メニューボタン・右下トップへ戻るボタン表示 |

---

## 処理フロー（フローダイアグラム）の設計ルール

- 番号は **半角数字（1, 2, 3...）** を使用（丸数字「①②③」は不可）
- 四角形（`.flow-step__num`）のサイズは **32px** で統一（PC・スマホ共通）
- フォントは **Outfit（`--font-accent`）** を明示的に指定する
- `.flow-step__left` に `align-self: stretch` を設定し、縦接続線が次の手順まで届くようにする
- 左右余白は他のコンテンツと揃える（`padding: 24px 24px 32px`）

---

## JavaScript 機能（必須）

| 機能 | 実装方法 |
|---|---|
| アコーディオン開閉 | `accordion__trigger` クリックで `.is-open` クラス toggle |
| 読書進捗バー | `scroll` イベントで `progressBar` の width を更新 |
| ナビゲーションハイライト | `scroll` イベント内で各セクションのY座標とスクロール位置を比較し `is-active` 付与（必ず下記の正解コードを使用すること） |
| モバイルサイドバー | 右上トグルボタン＋オーバーレイで開閉（767px以下のみ表示） |
| トップへ戻るボタン | 右下固定。スクロール200px超で出現（767px以下のみ表示） |

### ナビゲーションハイライトの正解コード
上下スクロール時の判定ズレを防ぐため、`IntersectionObserver` は使用せず、必ず以下のJSコードをそのまま実装してください。

```javascript
  const sections = Array.from(document.querySelectorAll('.spec-section[id]'));
  const navItems = document.querySelectorAll('.nav__item[id]');

  function updateActiveSection() {
    let currentId = '';
    const scrollY = window.scrollY;
    const windowHeight = window.innerHeight;
    const docHeight = document.documentElement.scrollHeight;

    // ページの一番下にいる場合は強制的に最後のセクションをアクティブにする
    if (scrollY + windowHeight >= docHeight - 5) {
      if (sections.length > 0) {
        currentId = sections[sections.length - 1].id.replace('sec-', '');
      }
    } else {
      // 画面上部から少し下の位置（オフセット）を基準に現在のセクションを判定
      const offset = 150;
      for (const section of sections) {
        if (section.offsetTop - offset <= scrollY) {
          currentId = section.id.replace('sec-', '');
        } else {
          break; // 以降のセクションはまだ到達していない
        }
      }
      // ページ最上部など、どのセクションにも到達していない場合は先頭セクションを表示
      if (!currentId && sections.length > 0) {
        currentId = sections[0].id.replace('sec-', '');
      }
    }

    if (currentId) {
      navItems.forEach(item => item.classList.remove('is-active'));
      const active = document.getElementById('nav-' + currentId);
      if (active) active.classList.add('is-active');
    }
  }

  window.addEventListener('scroll', updateActiveSection);
  updateActiveSection(); // 初期表示時
```

---

## 印刷（PDF）対応

```css
@media print {
  .sidebar, .reading-progress, .menu-toggle, .scroll-top-btn { display: none !important; }
  .main { margin-left: 0; }
  .accordion__body { display: block !important; }
  .accordion__icon { display: none; }
  .page-header { background: none; padding: 0 0 24px; border-bottom: 2px solid #000; }
  .page-header__title, .page-header__subtitle { color: #000; }
}
```

---

## 参照実装

- **リファレンスファイル**：`./SPEC-SAMPLE.html`（本フォルダに同梱）
- このファイルを正解のテンプレートとして参照し、新しいプロダクト仕様書HTMLを生成すること
- デザイン・構造・クラス名はこのファイルに準拠する

---

## 実行手順（AIへの指示）

1. 変換対象の `.md` ファイルを全文読み込む
2. セクション構成を解析し、サイドバーのナビゲーションを生成する
3. 各セクションをアコーディオン + spec-block に変換する
4. `spec.html` として同フォルダに出力する
5. 出力後、ユーザーに「作成完了」と報告し、変更点のサマリーを伝える

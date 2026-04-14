# GitHub Pages デモサイト 設計書

## 1. 概要

GitHub Pages の動作検証を兼ねた、インタラクティブデモのショーケースサイト。
4ページ構成で、それぞれ異なる描画・アニメーション技術を試す。

---

## 2. ページ構成

| パス | タイトル | 技術テーマ |
|------|----------|------------|
| `/` | ハブページ | Motion アニメーション・ルーティング |
| `/particle` | パーティクルアート | Canvas 2D・requestAnimationFrame・タッチイベント |
| `/clock` | インタラクティブ時計 | SVG・タイムゾーン・ダークモード |
| `/noise` | ノイズアート | Perlin noise・ジェネラティブアート |

---

## 3. 各ページ詳細

### `/` — ハブページ
- ミニマルなグリッドレイアウトで各デモへのカードリンク
- ロゴ・タイトルに Motion 演出（フェードイン・スライド等）
- トップから「動くサイト」という印象を与える

### `/particle` — パーティクルアート
- Canvas 2D でパーティクルを描画
- マウス・タッチに追従して粒子が反応
- Perlin noise ベースの有機的な動き、または引力・斥力の物理シミュレーション
- **技術検証ポイント:** requestAnimationFrame のパフォーマンス、タッチイベント対応

### `/clock` — インタラクティブ時計
- SVG でミニマルなアナログ時計を描画
- タイムゾーン切り替え機能
- ダークモード連動
- 秒針のスムーズな動き（CSS transition or Motion）
- **技術検証ポイント:** SVG アニメーション、リアルタイム更新

### `/noise` — ノイズアート
- Perlin noise をキャンバスに直接可視化するジェネラティブアート
- パラメータ（スケール・速度・色相）をスライダーでリアルタイム調整
- **技術検証ポイント:** ジェネラティブアートの基礎、シェーダーライクな描画

---

## 4. アーキテクチャ

```
[ローカル開発]
    │
    │  git push → main ブランチ
    ▼
[GitHub リポジトリ]
    │
    │  GitHub Actions
    │  └─ astro build → dist/
    │  └─ deploy → gh-pages ブランチ
    ▼
[GitHub Pages]
    https://<username>.github.io/<repo>/
```

### ディレクトリ構成

```
project/
├── .github/
│   └── workflows/
│       └── deploy.yml        # GitHub Actions デプロイ設定
├── public/
│   └── favicon.ico
├── src/
│   ├── components/
│   │   └── Nav.astro         # 共通ナビゲーション
│   ├── layouts/
│   │   └── Layout.astro      # 共通レイアウト（head, meta等）
│   ├── pages/
│   │   ├── index.astro       # ハブページ
│   │   ├── particle.astro    # パーティクルアート
│   │   ├── clock.astro       # インタラクティブ時計
│   │   └── noise.astro       # ノイズアート
│   └── styles/
│       └── global.css
├── astro.config.mjs
├── tailwind.config.mjs
├── tsconfig.json
└── package.json
```

---

## 5. 技術スタック

| カテゴリ | 技術 | 用途 |
|----------|------|------|
| フレームワーク | **Astro** | 静的サイト生成・マルチページルーティング |
| CSS | **Tailwind CSS v4** | レイアウト・ダークモード |
| 言語 | **TypeScript** | Canvas / SVG ロジック |
| アニメーション | **Motion** | ページ演出・トランジション |
| ホスティング | **GitHub Pages** | 静的配信（無料） |
| CI/CD | **GitHub Actions** | push で自動ビルド＆デプロイ |

---

## 6. GitHub Pages 設定の注意点

Astro でサブページ（`/particle` 等）を GitHub Pages に置く場合、`base` の設定が必須。

```js
// astro.config.mjs
export default defineConfig({
  site: 'https://<username>.github.io',
  base: '/<repo-name>',
})
```

これを忘れるとサブページへのリンクやアセットのパスが壊れる。

---

## 7. 実装順序（推奨）

1. Astro プロジェクト初期化 + GitHub Pages デプロイ設定
2. ハブページ（`/`）— レイアウト・ナビ・Motion 演出
3. `/clock` — SVG + リアルタイム更新（比較的シンプル）
4. `/noise` — Perlin noise の基礎実装
5. `/particle` — インタラクション込みの物理シミュレーション

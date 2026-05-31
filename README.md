# サイコソフィア診断 | AI-KNOW

> 4つの精神機能の優先順位を解明する深層自己理解診断。32問・約7分・24タイプ。MBTIを超えた、新しい自己認識の地平へ。

---

## 公開URL

| ページ | URL |
|---|---|
| LP（トップ） | https://tanitanistudio.github.io/psychosofia-diagnosis/lp.html |
| 診断クイズ（32問） | https://tanitanistudio.github.io/psychosofia-diagnosis/quiz.html |
| 診断結果 | https://tanitanistudio.github.io/psychosofia-diagnosis/result.html |

> 結果画面はクイズ完了後に localStorage 経由で表示される。デモとして `result.html?type=WELF` のようにクエリで強制表示も可能。

---

## ファイル構成

```
psychosofia-diagnosis/
├── index.html          # lp.html へのリダイレクト
├── lp.html             # ランディングページ
├── quiz.html           # 診断クイズ（32問・スコアリング）
├── result.html         # 診断結果ページ（24タイプ）
├── questions.json      # 設問データ（実装用）
├── questions.csv       # 設問一覧（人間レビュー用・設計根拠メモ付き）
├── lp-design-brief.md  # デザインブリーフ（カラーパレット・タイポ・UIスペック）
├── planning.md         # 企画書（エグゼクティブサマリー・KPI・技術仕様）
└── README.md
```

---

## サイコソフィアとは

アレクサンドル・アファナシエフ（1985）が提唱した性格類型論。人間の心理を **意志（Will）・感情（Emotion）・論理（Logic）・身体（Physics）** の4機能で捉え、その優先順位（1位→4位）が各人に固有の「精神の構造」を形成するとする。4機能の並び順は 4! = **24タイプ**。

---

## 技術仕様

- フロントエンド完結。サーバー不要。バニラ JavaScript（フレームワーク不使用）。
- スコアリングはクライアント側で完結し、localStorage に保存。
- 途中保存に対応（`aiknow_psychosofia_progress`・7日間有効）。
- フォント: Noto Serif JP（見出し）／ Noto Sans JP（本文）／ Inter（英数字ラベル）。
- カラー: ダークテーマ `--bg-deep:#080B14`。4機能カラーシステム（W:amber / E:rose / L:blue / F:sage）。
- レスポンシブ: モバイルファースト（640px / 720px / 1080px でブレイクポイント）。
- アクセシビリティ: WCAG AA 達成・`prefers-reduced-motion` 対応・キーボード操作完結。

---

## 診断ロジック

### 設問構成（32問 × 4機能）

| 機能 | 問数 | 逆転項目 | 主軸重み |
|---|---|---|---|
| W — 意志（Will） | 8問 | 3問 | 1.0〜1.4 |
| E — 感情（Emotion） | 8問 | 3問 | 1.0〜1.4 |
| L — 論理（Logic） | 8問 | 2問 | 1.0〜1.5 |
| F — 身体（Physics） | 8問 | 2問 | 1.0〜1.3 |

> 出題順は W→E→L→F の4軸循環シャッフル（連続で同じ機能が出ない設計）。逆転項目は前半・後半に分散配置。

### スコアリング式

```js
// 各設問への処理
const adjusted = (direction === 1) ? value : (8 - value);

// 主軸スコア加算
axisScores[axis] += adjusted * weight;

// 副軸寄与（cross-loading）
contributions.forEach(c => {
  axisScores[c.axis] += adjusted * c.weight;  // weight: 0.3
});
```

| 変数 | 説明 |
|---|---|
| `value` | ユーザー回答値（1〜7 の7段階リッカート） |
| `direction` | 正転項目 `+1`、逆転項目 `-1` |
| `weight` | 主軸 1.0〜1.5 ／ 副軸寄与（contributions） 0.3 |

### タイプ判定

```js
// 4軸スコアを降順ソート → タイプコードを生成
const sorted = Object.entries(scores).sort((a, b) => b[1] - a[1]);
const typeCode = sorted.map(([axis]) => axis).join(''); // 例: "WELF", "LEWF"
```

### 境界タイプ判定

```js
// 上位2軸のスコア差が理論最大値の5%未満 → 境界タイプフラグ
const maxTheoretical = 8 * 7 * 1.5; // ≈ 84
const isBorder = (sorted[0][1] - sorted[1][1]) / maxTheoretical < 0.05;
```

### タイプ一覧（24タイプ）

| コード | タイプ名 | コード | タイプ名 |
|---|---|---|---|
| WELF | ナポレオン | EWLF | 情熱家 |
| WEFL | 覇者 | EWFL | 詩人 |
| WLEF | 哲人将軍 | ELWF | 教師 |
| WLFE | 設計者 | ELFW | 作家 |
| WFLE | 武将 | EFLW | 芸術家 |
| WFEL | 先駆者 | EFWL | 審美家 |
| LEWF | ソクラテス | FWEL | 戦士 |
| LEFW | 分析家 | FWLE | 冒険家 |
| LWEF | 戦略家 | FELW | 癒し手 |
| LWFE | 建築家 | FEWL | 共感者 |
| LFEW | 職人 | FLWE | 職人気質 |
| LFWE | 技術者 | FLEW | ナチュラリスト |

---

## デザイン原則

| 項目 | 内容 |
|---|---|
| コンセプト | 「深淵の知」— 哲学的・内省的・知的神秘 |
| ターゲット | 20〜30代・MBTI/ソシオニクス経験者・自己理解に関心 |
| ヒーロービジュアル | 4機能カラーの CSS ぼかし球体（orb）が収束する抽象的な光景 |
| カラーシステム | W: `#E07B45` / E: `#C4668A` / L: `#5A9FD6` / F: `#6BAF8A` |
| ブランドゴールド | `#C9A84C`（ナビ・CTA・強調） |
| アニメーション | orb の緩やかな拡縮（`scale 1↔1.08 / 6〜9s`）、設問スライドアップ（`250ms`） |

---

## 開発者ノート

### ローカル検証

```bash
cd /path/to/psychosofia-diagnosis
python3 -m http.server 8000
open http://localhost:8000/lp.html
```

### デモ表示（任意タイプの結果画面）

```
result.html?type=WELF   # ナポレオン（W優位）
result.html?type=LEWF   # ソクラテス（L優位）
result.html?type=EFLW   # 芸術家（E優位）
result.html?type=FLEW   # ナチュラリスト（F優位）
```

### localStorage キー

| キー | 用途 | 有効期限 |
|---|---|---|
| `aiknow_psychosofia_progress` | 回答中の進捗（途中保存） | 7日間 |
| `aiknow_psychosofia_result` | 完了した最終結果 | 無期限 |

---

## 関連ドキュメント

- [企画書 (planning.md)](./planning.md) — エグゼクティブサマリー・KPI・エージェント連携フロー
- [デザインブリーフ (lp-design-brief.md)](./lp-design-brief.md) — カラーパレット・タイポ・UIスペック詳細
- [設問データ (questions.json)](./questions.json) — 32問の実装用データ（重み・逆転・副軸寄与）
- [設問レビュー用 (questions.csv)](./questions.csv) — 設計根拠メモ付き一覧

---

## ライセンス

© 2026 AI-KNOW. All rights reserved.  
サイコソフィア理論: A.Y. Afanasyev (1985) に基づく独自実装。

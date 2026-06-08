# CLAUDE.md — サイコソフィア診断 プロジェクト引き継ぎ

このファイルは Claude Code が自動で読み込む設定ファイルです。
新しいセッションを開始したら、まずこのファイルの内容を把握してください。

---

## プロジェクト概要

- **診断名**: サイコソフィア診断
- **ベース理論**: アファナシエフ型パーソナリティ類型（1985）
- **タイプ数**: 24タイプ（4機能の順列 4! = 24）
- **設問数**: 32問（7段階リッカート）
- **公開URL**: https://tanitanistudio.github.io/psychosofia-diagnosis/
- **GitHub**: https://github.com/tanitanistudio/psychosofia-diagnosis
- **ステータス**: 公開中（GitHub Pages）

---

## ディレクトリ構成

```
psychosofia-diagnosis/          ← このリポジトリのルート
├── lp.html                     # ランディングページ
├── quiz.html                   # 診断クイズ（32問）
├── result.html                 # 診断結果（24タイプ）
├── index.html                  # lp.html へのリダイレクト
├── questions.json              # 設問データ（実装用）
├── questions.csv               # 設問一覧（レビュー用）
├── lp-design-brief.md          # デザインブリーフ
├── planning.md                 # 企画書
├── img/                        # キャラクター画像（24枚・640x640px）
│   ├── VELF.png, EVLF.png … （タイプコード.png）
└── CLAUDE.md                   # このファイル
```

---

## 重要な技術仕様

### 機能軸の表記（必須）
**W ではなく V を使うこと**（Воля = ロシア語で意志）

| 記号 | 機能 | 色 |
|---|---|---|
| **V** | 意志（Will / Воля） | `#D6A84A`（ゴールド） |
| **E** | 感情（Emotion） | `#D84F8B`（ピンク） |
| **L** | 論理（Logic） | `#3B8CFF`（ブルー） |
| **F** | 身体（Physics） | `#78B95A`（グリーン） |

### タイプコード例
`VELF`（V=1位, E=2位, L=3位, F=4位）

### 全24タイプ一覧
| コード | 名前 | コード | 名前 |
|---|---|---|---|
| VELF | ナポレオン | EVLF | 情熱家 |
| VEFL | 覇者 | EVFL | 詩人 |
| VLEF | 哲人将軍 | ELVF | 教師 |
| VLFE | 設計者 | ELFV | 作家 |
| VFLE | 先駆者 | EFVL | 芸術家 |
| VFEL | 武将 | EFLV | 審美家 |
| LEVF | ソクラテス | FVEL | 戦士 |
| LEFV | 分析家 | FVLE | 冒険家 |
| LVEF | 戦略家 | FEVL | 癒し手 |
| LVFE | 建築家 | FELV | 共感者 |
| LFEV | 職人 | FLVE | 職人気質 |
| LFVE | 技術者 | FLEV | ナチュラリスト |

### localStorage キー
```
aiknow_psychosofia_progress   # 診断の途中保存（7日間有効）
aiknow_psychosofia_result     # 診断完了結果
```

### 画像パス
```
img/[タイプコード].png
例: img/VELF.png, img/LEVF.png
```

---

## カラーシステム（CSS変数）

```css
:root {
  --color-v-main: #D6A84A;   /* V軸（意志）ゴールド */
  --color-e-main: #D84F8B;   /* E軸（感情）ピンク */
  --color-l-main: #3B8CFF;   /* L軸（論理）ブルー */
  --color-f-main: #78B95A;   /* F軸（身体）グリーン */
  --color-bg:     #07101C;   /* 背景 */
  --color-card:   #10182A;   /* カード背景 */
  --color-text:   #F3EFE6;   /* テキスト */
}
```

---

## スコアリング（quiz.html の実装）

```js
// 各設問の処理
const adjusted = (direction === 1) ? value : (8 - value);
axisScores[axis] += adjusted * weight;
contributions.forEach(c => { axisScores[c.axis] += adjusted * c.weight; });

// タイプ判定: V/E/L/F の降順ソート → コード文字列
const typeCode = Object.entries(scores)
  .sort((a, b) => b[1] - a[1])
  .map(([k]) => k).join(''); // 例: "VELF"
```

---

## ローカル開発

```bash
# リポジトリのパス
cd /Users/tanitanistudio/Desktop/psychosofia-diagnosis

# ローカルサーバー起動
python3 -m http.server 8000
open http://localhost:8000/lp.html

# デモ用URL（任意タイプの結果表示）
open http://localhost:8000/result.html?type=VELF
```

## GitHub への反映

```bash
cd /Users/tanitanistudio/Desktop/psychosofia-diagnosis
git add -A
git commit -m "feat: ..."
git push origin main
# → 約1〜2分で GitHub Pages に反映
```

---

## 回答言語・作業スタイル

- **回答は日本語**で行うこと
- コメントも日本語で記載
- コード変更前に変更内容と影響範囲を説明してから実装すること
- ファイル変更後は必ず `git add / commit / push` まで行うこと
- **git add / commit / push はユーザーに確認せず自動で実行すること**

---

## 関連リポジトリ（参考）

| リポジトリ | 内容 | URL |
|---|---|---|
| psychosofia-diagnosis | このリポジトリ | https://github.com/tanitanistudio/psychosofia-diagnosis |
| claude-code-matching-app | マッチングアプリ（Laravel+React） | https://github.com/tanitanistudio/claude-code-matching-app |
| socionics-diagnosis | ソシオニクス診断 | https://github.com/tanitanistudio/socionics-diagnosis |

# ARK Tools (arktools) - プロジェクト開発コンテキスト & 引き継ぎ仕様書

このドキュメントは、本プロジェクト（ARK 座標変換ツール）の設計思想、これまでの開発経緯、実装仕様、UX上のこだわりをまとめた引き継ぎ用資料です。
新しいチャットセッションを開始した際にも、本ドキュメントを参照することで過去の経緯を 100% 把握して開発を継続できます。

---

## 1. プロジェクト基本情報

- **リポジトリ**: [https://github.com/TYanoLine/arktools](https://github.com/TYanoLine/arktools) (Public, branch: `main`)
- **ローカルリポジトリ パス**: `C:\trunk\git\arktools`
- **主要構成ファイル**:
  - `index.html` / `ark_coords_converter.html`: 座標変換ツールの本体（スタンドアロン単一ファイルWebアプリ）
  - `images/`: 各マップの高解像度正方形マップ画像（The Island, Scorched Earth, The Center, Aberration, Extinction）
  - `README.md`: リポジトリ概要ドキュメント
  - `.gitignore`: 環境設定・一時ファイル・デプロイ情報の除外ルール
  - `PROJECT_CONTEXT.md`: 本コンテキスト引き継ぎ書

> [!IMPORTANT]
> **デプロイ先・環境情報のコミット禁止ルール**:
> ローカルのデプロイ先パス（例: `C:\webapps\...`、IIS 物理パス等）はリポジトリにコミットしない運用です。

---

## 2. アプリケーション機能 & 設計思想

### 2.1. 座標変換ロジック
Unreal Engine の内部ワールド座標（X, Y）と、ゲーム内 GPS 緯度（Lat）・経度（Lon）を相互変換します。
公式Wikiの計算式および地上検証値（Ground Truth）に基づいて高精度にキャリブレーションされています。

- **変換式**:
  - 順計算: $\text{GPS} = \text{Shift} + \left( \frac{\text{UE\_Coord}}{\text{Multiplier}} \right)$
  - 逆計算: $\text{UE\_Coord} = (\text{GPS} - \text{Shift}) \times \text{Multiplier}$
  - 小数第1位に四捨五入して表示。
- **マップ別パラメータ (`MAP_CONFIGS`)**:
  - `the_island`: Shift Lat 50.0, Lon 50.0 / Multiplier Lat 8000.0, Lon 8000.0
  - `scorched_earth`: Shift Lat 50.0, Lon 50.0 / Multiplier Lat 8000.0, Lon 8000.0
  - `the_center`: Shift Lat 30.34, Lon 55.10 / Multiplier Lat 9584.0, Lon 9600.0
  - `aberration`: Shift Lat 50.0, Lon 50.0 / Multiplier Lat 8000.0, Lon 8000.0
  - `extinction`: Shift Lat 50.0, Lon 50.0 / Multiplier Lat 8000.0, Lon 8000.0

---

## 3. UI/UX の仕様とこだわり（ユーザー要望の反映経緯）

### 3.1. ARK 実機 HUD カラーテーマ
実機スクリーンショットから平均色およびテキスト発光色を抽出し、ARK: Survival Ascended / Evolved の HUD 質感を完全再現しています。

- **文字の色 & ふちの色**: `#8fe0eb` (`rgb(143, 224, 235)`)
  - テンキー外枠、吹き出し三角矢印、テンキーの数字キー文字、入力枠ハイライト、タイトル・アクセントカラー
- **塗りの平均色（背景）**: `#173642` (`rgb(23, 54, 66)`)
  - テンキー本体背景（オーバーレイ）、結果表示ボックス、カード背景
- **ダークベース**:
  - 全体背景: `#0c161c`
  - パネル背景: `#10252e`
  - 入力欄・キーボタン背景: `#112833` / `#122b35`

### 3.2. 専用ソフトウェアテンキー（吹き出し風オーバーレイ）
スマホや iPad mini での数字・マイナス入力を極限まで快適にするための独自テンキー。

- **オーバーレイ配置**:
  - `coord-section-wrap` 内で `position: absolute; top: calc(100% + 12px); z-index: 60;`。
  - 下部の結果表示やお気に入りリストを押し下げない。外側タップで自動クローズ。
- **吹き出しデザイン（Beak Pointer）**:
  - 現在フォーカスされている入力欄（X または Y）の `🔢` アイコンの中心を指す三角矢印（`::before`, `::after`）。
  - CSS変数 `--beak-pos` を JS で動的計算。X と Y の切り替え時に左右へスムーズにスライド移動。
- **高速連打への完全追従（入力無視の防止）**:
  - **`pointerdown` で即座に入力を反映（遅延0ms）**。
  - `click` イベントの間引きや合成遅延の影響を受けない。
  - ダブルタップ拡大抑制（`touchend` 300ms タイマー）の対象から `.virtual-keypad` および各種ボタンを除外。
- **ヘッダー構成**:
  - 画面を圧迫しないよう、ヘッダーは「入力先バッジ」「切替ボタン」「閉じるボタン」のみのコンパクト構成（Lat/Lon は非表示）。

### 3.3. 地図タッチ / ドラッグ逆算トグル
- タップやドラッグで画面スクロールしたい際の誤タッチ・誤入力を防ぐため、**デフォルト OFF** に設定。
- トグルボタンを ON に切り替えた時のみ、地図タッチ・ドラッグでの座標逆算・マーカー移動が有効化。

### 3.4. お気に入り（ブックマーク）管理
- `localStorage`（キー: `ark_coords_favorites_v1`）に保存。
- 座標にお好みの名前をつけて登録・解除可能。
- マップ上に星ピン（⭐）がプロットされ、ピンをタップするとその座標を即座に再読み込み。
- ヘッダーからアコーディオン形式で展開/折りたたみ可能。

### 3.5. 外部リンク連携
- タイトル下の説明文中の `GetAllLocation` は、別タブ（`target="_blank"`）で [ARK IDs (Creatures)](https://arkids.net/creatures) を開くアンカーリンクになっています。

---

## 4. 今後の開発・改修時のチェックリスト

- [ ] **カラーテーマの一貫性**: 新規要素追加時も `#8fe0eb`（文字・枠）と `#173642`（塗り）をベースにすること。
- [ ] **タップイベントの作法**: タッチデバイスでの連打取りこぼしを防ぐため、ボタンUIは `pointerdown` による即時判定を優先すること。
- [ ] **機密・環境情報の保護**: コミット前にローカル環境固有のパスが含まれていないことを確認すること。

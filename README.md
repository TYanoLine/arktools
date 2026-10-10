# ARK Tools (arktools)

ARK: Survival Ascended (ASA) / ARK: Survival Evolved (ASE) 向けの便利ツール集リポジトリです。

---

## 🛠️ 収録ツール

### 1. ARK 座標変換ツール (`index.html` / `ark_coords_converter.html`)
Unreal Engine の内部座標（`GetAllLocation` や `SetPlayerPos` 等の X, Y 座標値）を、ゲーム内の GPS 緯度（Lat）・経度（Lon）にリアルタイムで双方向変換する Web アプリケーションです。

- **対応マップ**:
  - The Island (アイランド)
  - Scorched Earth (スコーチド・アース)
  - The Center (ザ・センター)
  - Aberration (アベレーション)
  - Extinction (エクスティンクション)
- **特徴・機能**:
  - **公式Wiki準拠の高精度変換式** & 地上検証値（Ground Truth）に基づくキャリブレーション
  - **専用ソフトウェアテンキー（オーバーレイ/吹き出し）**: 数字・マイナス・小数点入力に特化、0msレイテンシの高速連打対応
  - **ARK 実機 HUD カラーテーマ**: ゲーム内HUDシアン (`#8fe0eb`) & パネル平均色 (`#173642`) を完全再現
  - **タッチ / ドラッグ逆算トグル**: 誤操作を防ぐ誤タッチ防止トグルスイッチ
  - **お気に入り（ブックマーク）管理**: ローカルストレージに座標・拠点を名前付きで保存、マップピン表示
  - **レスポンシブ対応**: PC、タブレット（iPad mini 等）、スマートフォンに最適化
  - **ARK IDs 連携**: [ARK IDs (Creatures)](https://arkids.net/creatures) への直接リンク

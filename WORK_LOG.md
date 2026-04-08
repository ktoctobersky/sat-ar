# Work Log - 2026-04-09

## 作業概要

HANDOFF.md に基づき、iPhone Safari での「START TRACKING」後にメイン画面に進まない問題を修正。
デバッグオーバーレイの追加 + 考えられる原因を網羅的に修正。

## 作業内容

### フェーズ1: 状況把握
- gitリポジトリは `sat-ar/` サブディレクトリ内にあることを確認
- `index.html` 1ファイル構成、satellite.js CDNのみ依存
- 既存コードにはデバッグログのdivがあったが `display:none` で不可視、`dlog()` 関数が未定義

### フェーズ2: デバッグオーバーレイ追加
- `debugLog` divの `display:none` を削除（デフォルト表示に変更）
- `dlog()` 関数を追加（画面表示 + console.log 両方に出力）
- グローバルエラーハンドラ（`error`, `unhandledrejection`）を追加
- ブート時にiOS判定とUA情報を表示
- startBtn, requestGeolocation, requestOrientation, requestCamera の各所に詳細ログ追加

### フェーズ3: iPhone起動問題の先回り修正（修正1〜6）

**修正1: getUserMedia のフォールバック**
- 4段階の constraint を順次試行:
  1. `{ exact: 'environment' }`
  2. `{ facingMode: 'environment' }`
  3. `{ ideal: 'environment' }`
  4. `{ video: true }` (最終手段)

**修正2: geolocation タイムアウト短縮**
- `timeout: 8000` → `5000`
- `enableHighAccuracy: true` → `false` (高精度は遅くなるため)

**修正3: requestOrientation の5秒タイムアウト**
- 権限ダイアログが応答しない場合、5秒後に自動続行
- 詳細な状態ログを追加

**修正4: startTracking 内の例外保護**
- `buildCompassBar()`, `updateTleFooter()`, `render()` を個別に try-catch

**修正5: TLE未ロード時の自動フォールバック**
- `render()` で `tleDatabase` が空なら `loadFallbackTLEs()` を呼ぶ

**修正6: start-btn の z-index**
- 元コードで既に `position: relative; z-index: 10;` が設定されていたので変更不要

### フェーズ4: コミット & プッシュ
- コミット: `fcee250`
- main ブランチにプッシュ済み

## 最終状態
- 現在の状況: 修正適用済み、GitHub Pagesにデプロイ待ち（通常数分で反映）
- デバッグオーバーレイが常時表示状態（問題解決後に非表示にする予定）

## 朝のユーザーへ

おはようございます!

### 現状
iPhone起動問題について、考えられる原因を6つ修正しました。
画面上部に緑色のデバッグログが表示されるようになっています。
これにより、どのステップで問題が起きているかが一目で分かります。

### iPhoneで試す手順
1. Safariを完全終了 (App Switcherで上スワイプ)
2. https://ktoctobersky.github.io/sat-ar/ を開く
3. URLバーを下に引っ張って強制リロード
4. START TRACKING をタップ
5. 3つの権限を全て許可
6. 画面がメインビュー（カメラ映像+衛星情報）に切り替われば成功

### もし動かなかったら
画面上部に緑のデバッグログが出ているはずです。
以下の情報が見えるので、どこで止まっているかが分かります:
- `startBtn: clicked` → ボタンは反応している
- `requestGeolocation: OK` → 位置情報取得成功
- `requestOrientation: permission state = granted` → モーション許可成功
- `requestCamera: got stream with constraint X` → カメラ取得成功
- `startBtn: startTracking done` → 全て成功

スクリーンショットを撮って共有してください。

### 次の一手
- デバッグログの内容次第で、問題箇所を特定して追加修正
- 問題が解決したら、デバッグオーバーレイを `?debug=1` パラメータでのトグル式に変更
- 15:01のISSパスまでに動作確認できれば理想的

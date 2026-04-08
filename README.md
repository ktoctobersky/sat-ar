# 🛰️ SAT.AR — アマチュア無線衛星 AR トラッカー

iPhoneを空にかざすと、**今まさに上空を飛んでいるアマチュア無線衛星の位置**がカメラ映像に重ねて表示されるWebアプリです。ISS(国際宇宙ステーション)を含む8機の衛星をリアルタイム追跡できます。

## ✨ デモ

👉 **[https://ktoctobersky.github.io/sat-ar/](https://ktoctobersky.github.io/sat-ar/)**

> ⚠️ iPhone Safariで開いてください。PCやAndroidでも位置計算は動きますが、AR機能(カメラ+方位センサー連動)はiOS向けに最適化されています。

## 🎯 何ができる？

- 📍 **現在地から見た衛星の位置**を、カメラ映像に重ねて表示
- 🧭 **端末の向き(方位+傾き)に連動**して衛星マーカーが空間上に定位
- 📡 追跡対象: **ISS, SO-50, AO-91, PO-101, FO-29, RS-44, AO-73, IO-117**
- 📻 各衛星の**受信周波数・モード**を表示(アマチュア無線での受信用)
- 🌅 地平線下の衛星も下部リストで一覧できる

## 📱 使い方

1. iPhoneのSafariで上記URLを開く
2. 「START TRACKING」をタップ
3. 以下の権限をすべて**許可**する:
   - **位置情報** — 観測地の特定に使用
   - **モーションと画面の向き** — 方位角・傾きの取得
   - **カメラ** — 背景映像の取得
4. iPhoneを空に向ける → カメラ映像に衛星マーカーが浮かぶ ✨
5. 画面中央のクロスヘアに衛星を合わせれば、その方向に衛星がいる

## 🛠 技術スタック

- **フレームワーク**: なし(Vanilla HTML/CSS/JS、1ファイル完結)
- **衛星軌道計算**: [satellite.js](https://github.com/shashwatak/satellite-js) (SGP4モデル)
- **センサーAPI**: 
  - `navigator.geolocation` (位置情報)
  - `DeviceOrientationEvent` + `webkitCompassHeading` (端末の方位・傾き)
  - `navigator.mediaDevices.getUserMedia` (背面カメラ)
- **描画**: CSS Transform + 絶対配置(WebGL不使用)

## 🎨 設計メモ

- 衛星位置は**TLE(Two-Line Element)データ**から計算
- 観測地からの方位角(azimuth)と仰角(elevation)を算出
- 端末の向きとの差分を画面座標に投影してマーカーを配置
- iPhoneのカメラFOVを約65度と仮定(モデルによって多少ズレあり)

## ⚠️ 既知の制限

| 項目 | 内容 |
|---|---|
| **TLEデータの鮮度** | ハードコードされたTLEは作成時点のもの。時間が経つと精度が低下(数km〜数十kmズレる可能性)。厳密な観測にはCelestrakから最新TLEを取得推奨。 |
| **磁北と真北の差** | コンパスは磁北基準。東京では約7-8度西にズレる(偏角補正未実装)。 |
| **磁気干渉** | iPhoneのコンパスは電気機器・金属の近くで精度が落ちる。屋外の開けた場所推奨。 |
| **カメラFOV固定** | 65度固定。iPhone Pro の超広角などではズレが生じる。 |
| **HTTPS必須** | iOS Safariのセンサー系APIはHTTPS環境でしか動作しない。 |

## 📡 追跡対象衛星

| 衛星 | 周波数 | モード | 用途 |
|---|---|---|---|
| **ISS** | 437.800 MHz | FM | クロスバンドレピータ(ダウンリンク) |
| **SO-50** | 436.795 MHz | FM | FM衛星(ダウンリンク) |
| **AO-91** | 145.960 MHz | FM | FM衛星 |
| **PO-101** | 145.900 MHz | FM | FM衛星 |
| **FO-29** | 435.795 MHz | SSB/CW | リニアトランスポンダ |
| **RS-44** | 435.640 MHz | SSB/CW | リニアトランスポンダ |
| **AO-73** | 145.935 MHz | SSB/CW | リニアトランスポンダ |
| **IO-117** | 435.310 MHz | Digital | デジピータ |

## 🔗 参考リンク

- [JAMSAT 衛星通過時刻予報](https://www.jamsat.or.jp/pred/tokyo/sat.html) - 東京の衛星パス予報
- [AMSAT Live Satellite Status](https://www.amsat.org/status/) - 衛星の運用状態
- [Celestrak Amateur TLE](https://celestrak.org/NORAD/elements/amateur.txt) - 最新TLEデータ
- [ARISS Japan](https://www.jarl.org/ariss/) - ISSアマチュア無線関連情報

## 📝 ライセンス

MIT License(お好みで変更してください)

## 🙏 謝辞

- [satellite.js](https://github.com/shashwatak/satellite-js) - 軌道計算ライブラリ
- アマチュア無線コミュニティの先達の皆さん

---

*Built for amateur radio enthusiasts who look up at the sky.* 📡✨

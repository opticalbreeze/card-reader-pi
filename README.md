# ICカード打刻システム - Raspberry Pi版

Raspberry Piで動作するICカードリーダーを使った勤怠打刻システムのクライアントです。

---

## 🚀 クイックスタート

### はじめてセットアップする方

📖 **[ゼロからセットアップガイド](docs/RASPBERRY_PI_SETUP_FROM_SCRATCH.md)** をご覧ください。

**5ステップで起動できます！**

1. ファイルをダウンロード
2. `./setup.sh` を実行（全自動セットアップ）
3. 再起動
4. 設定ファイルを編集（`python3 config.py`）
5. `./start_pi_simple.sh` で起動

### 便利なツール

- **自動セットアップ**: `./setup.sh` - 依存関係を全自動インストール
- **簡単起動**: `./start_pi_simple.sh` - ワンクリックで起動（仮想環境自動有効化）
- **詳細起動**: `./start_pi.sh` - USBデバイスや権限も確認して起動

---

## 🎯 システム概要

このシステムは、Raspberry PiでICカードリーダーを使ってカードを読み取り、サーバーに打刻データを送信する勤怠管理システムです。

### 主な機能

- ✅ ICカードの読み取り（Mifare、FeliCa等に対応）
- ✅ サーバーへの打刻データ送信
- ✅ 送信失敗時のローカルキャッシュ保存
- ✅ 自動リトライ機能（10分間隔）
- ✅ **GPIO制御**: RGB LEDと圧電ブザーで視覚・聴覚フィードバック
- ✅ **LCD表示**: I2C接続のLCDディスプレイに状態を表示（オプション）
- ✅ **エラー耐性**: LCD/GPIOはエラー時は自動的に無効化

### Raspberry Pi版の特徴

- 💡 **RGB LED（赤・緑・青・シアン）** で状態表示
- 🔔 **圧電ブザー** で音声フィードバック
- 📺 **LCD（I2C 1602）** でメッセージ表示
- 🔌 **GPIO制御** による拡張性
- 🛡️ **エラー耐性**: ハードウェアが接続されていなくても動作

---

## 📁 プロジェクト構成

```
card_reader_pi/
├── pi_client.py               # Raspberry Pi版クライアント（メイン）
├── config.py                  # 設定GUI（サーバーURL・LCD設定）
├── common_utils.py            # 共通ユーティリティ関数
├── constants.py               # 共通定数定義
├── gpio_config.py             # GPIO設定（LED・ブザー）
├── lcd_i2c.py                 # LCD制御（I2C 1602）
├── start_pi_simple.sh         # 起動スクリプト（シンプル版、推奨）
├── start_pi.sh                # 起動スクリプト（詳細版）
├── setup.sh                   # セットアップスクリプト
├── setup_autostart_fixed.sh   # 自動起動設定
├── requirements_pi.txt        # 依存パッケージ
├── client_config_sample.json  # 設定ファイルサンプル
└── docs/                      # ドキュメント
    ├── RASPBERRY_PI_SETUP_FROM_SCRATCH.md  # ゼロからセットアップ
    ├── PI_CLIENT_EXPLANATION.md            # pi_client.py完全解説（AI向け）
    ├── NO_NEW_VERSIONS.md                  # バージョン管理方針
    ├── RASPBERRY_PI_UPDATE.md              # 更新ガイド
    ├── RASPBERRY_PI_SETUP_GUIDE.md         # セットアップ詳細
    ├── TROUBLESHOOTING.md                  # トラブルシューティング
    └── HARDWARE_BUZZER_READER_GUIDE.md     # ハードウェア接続ガイド
```

---

## 🚀 セットアップ

### 前提条件

- Raspberry Pi OS（Raspberry Pi OS 11以上推奨）
- Python 3.7以上
- ICカードリーダー（Sony RC-S380等）

### ステップ1: リポジトリのクローン

```bash
git clone https://github.com/YOUR_USERNAME/card-reader-raspberry-pi.git
cd card-reader-raspberry-pi
```

### ステップ2: 自動セットアップ（推奨）

```bash
chmod +x setup.sh
./setup.sh
```

このスクリプトは以下を自動実行します：

- 仮想環境の作成
- 必要なPythonパッケージのインストール
- I2CとGPIOの有効化確認
- PC/SCグループへの追加

### ステップ3: 再起動

```bash
sudo reboot
```

### ステップ4: 設定

```bash
source venv/bin/activate
python3 config.py
```

GUIで以下を設定：

- サーバーURL（例: `http://192.168.1.217:5000`）
- LCD設定（I2Cアドレス、バス番号等）

### ステップ5: 起動

```bash
# 簡単起動（推奨）
./start_pi_simple.sh

# または詳細起動
./start_pi.sh
```

---

## 🔧 対応カードリーダー

### 動作確認済み

- **Sony RC-S380** (PaSoRi) - FeliCa対応
- **Sony RC-S330** (PaSoRi)
- **Circle CIR315 CL** - USB NFC Reader
- **ACS ACR122U**

### 動作予想

- Identiv uTrust 3700 F
- SCM SCL3711

---

## 📱 対応ICカード

- **Mifare Classic** (1K, 4K)
- **Mifare Ultralight** (C)
- **FeliCa** (Suica、PASMO、WAON、nanaco等)
- **ISO14443 Type A/B**

---

## 🖥️ ハードウェア接続

### 必須

- ICカードリーダー（USB接続）

### オプション（推奨）

- **LCDディスプレイ（I2C 1602）**
  - I2Cアドレス: 0x27（デフォルト）
  - SDA: GPIO 2（Pin 3）
  - SCL: GPIO 3（Pin 5）
  - 詳細: [ハードウェア接続ガイド](docs/HARDWARE_BUZZER_READER_GUIDE.md)

- **RGB LED**
  - 赤: GPIO 17（Pin 11）
  - 緑: GPIO 27（Pin 13）
  - 青: GPIO 22（Pin 15）
  - 詳細: [LED接続ガイド](docs/LED_CONNECTION_GUIDE.md)

- **圧電ブザー**
  - GPIO 18（Pin 12）
  - 詳細: [ブザー接続ガイド](docs/HARDWARE_BUZZER_READER_GUIDE.md)

---

## 📖 詳細ドキュメント

### 👥 ユーザー向け

- **[ゼロからセットアップガイド](docs/RASPBERRY_PI_SETUP_FROM_SCRATCH.md)** - 初めてセットアップする方向け
- **[セットアップ詳細ガイド](docs/RASPBERRY_PI_SETUP_GUIDE.md)** - 詳細なセットアップ手順
- **[更新ガイド](docs/RASPBERRY_PI_UPDATE.md)** - 最新版への更新方法
- **[トラブルシューティング](docs/TROUBLESHOOTING.md)** - よくある問題と解決方法
- **[ハードウェア接続ガイド](docs/HARDWARE_BUZZER_READER_GUIDE.md)** - ハードウェア接続方法

### 🤖 AI向け（開発者・AIアシスタント向け）

- **[pi_client.py完全解説](docs/PI_CLIENT_EXPLANATION.md)** - コードの動作とエラー処理を詳しく解説
- **[バージョン管理方針](docs/NO_NEW_VERSIONS.md)** - バージョン管理に関する方針

---

## 🔐 サーバー側の設定

このクライアントは、サーバー側のAPIにデータを送信します。

サーバー側のセットアップについては、サーバー用リポジトリを参照してください。

**接続先の設定:**
- デフォルト: `http://192.168.1.217:5000`
- 設定方法: `python3 config.py` でGUIから変更可能

---

## 🛠️ トラブルシューティング

### カードリーダーが認識されない

1. USBポートを確認
2. `lsusb` でデバイスを確認
3. 別のUSBポートで試す
4. 仮想環境で実行しているか確認

### サーバーに接続できない

1. サーバーが起動しているか確認
2. ファイアウォール設定を確認
3. 同じネットワークに接続しているか確認
4. `client_config.json` のサーバーIPを確認

### LCD/GPIOが動作しない

1. I2C、GPIOが有効か確認: `sudo raspi-config`
2. 権限を確認: `sudo usermod -a -G gpio,i2c $USER`
3. 再起動後、再度試行

### 仮想環境で起動できない

```bash
# 仮想環境を再作成
rm -rf venv
python3 -m venv venv
source venv/bin/activate
pip install -r requirements_pi.txt
```

詳細は [トラブルシューティング](docs/TROUBLESHOOTING.md) を参照してください。

---

## 📄 ライセンス

このプロジェクトはMITライセンスの下で公開されています。

---

## 🙏 謝辞

- nfcpy開発チーム
- pyscard開発チーム
- Flask開発チーム
- NFCカードリーダーメーカー各社

---

⭐ このプロジェクトが役に立ったら、スターをつけていただけると嬉しいです！


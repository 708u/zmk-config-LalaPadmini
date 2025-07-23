# CLAUDE.md

このファイルは、このリポジトリのコードを扱う際のClaude Code (claude.ai/code) へのガイダンスを提供します。

## プロジェクト概要

これは**LalaPadmini**キーボード用のZMK（Zepherベースのメカニカルキーボード）ファームウェア設定リポジトリです。以下の特徴を持つ分割型エルゴノミックキーボードです：

- Seeeduino XIAO BLEボードを使用
- rgbled_adapterシールドによるRGB LEDサポート
- Cirqueトラックパッドサポート（Pinnacleチップ）
- GUIキーマップ編集用のZMK Studioサポート

## ビルドコマンド

### 初期セットアップ

```bash
# Westの初期化
west init -l config

# 依存関係の更新
west update
```

### ファームウェアのビルド

```bash
# 右半分のビルド
west build -b seeeduino_xiao_ble -d build/right -- -DSHIELD="lalapadmini_right rgbled_adapter" -DZMK_EXTRA_MODULES="/path/to/zmk-rgbled-widget;/path/to/cirque-input-module"

# 左半分のビルド
west build -b seeeduino_xiao_ble -d build/left -- -DSHIELD="lalapadmini_left rgbled_adapter" -DZMK_EXTRA_MODULES="/path/to/zmk-rgbled-widget;/path/to/cirque-input-module"

# 設定リセットファームウェアのビルド
west build -b seeeduino_xiao_ble -d build/reset -- -DSHIELD=settings_reset
```

### その他の便利なコマンド

```bash
# クリーンビルド
west build -t clean

# 設定調整用のメニューコンフィグ
west build -t menuconfig

# ファームウェアの書き込み（ブートローダーモード進入後）
cp build/*/zephyr/zmk.uf2 /Volumes/XIAO-SENSE/
```

## リポジトリ構造

### 主要な設定ファイル

- `config/lalapadmini.conf` - メインファームウェア設定（電源管理、Bluetooth、RGB設定）
- `config/lalapadmini.keymap` - キーマップ定義
- `config/lalapadmini.json` - ZMK Studio用キーボードメタデータ
- `config/west.yml` - 依存関係管理（zmk、cirque-input-module、zmk-rgbled-widget）
- `build.yaml` - GitHub Actions用ビルドマトリックス設定

### ハードウェア定義

- `config/boards/shields/lalapadmini/` - シールド定義ファイル
  - `lalapadmini.dtsi` - 共通ハードウェア設定
  - `lalapadmini_left.overlay` - 左半分専用設定
  - `lalapadmini_right.overlay` - 右半分専用設定（トラックパッド含む）
  - `Kconfig.*` - 設定オプション

## 主要機能の設定

### 電源管理

- アイドルタイムアウト：5分
- ディープスリープ：1時間
- `lalapadmini.conf`で設定

### RGB LED

- バッテリーレベルインジケーター
- レイヤー変更インジケーター
- `CONFIG_ZMK_RGB_*`オプションで設定

### トラックパッド

- 右半分のみに接続
- Cirque Pinnacleチップを使用
- `lalapadmini_right.overlay`でSPI設定

## 開発メモ

- このリポジトリはプッシュ/PR時の自動ビルドにGitHub Actionsを使用
- `zephyr/module.yml`により、このリポジトリをZMKモジュールとして使用可能
- キーマップを変更する場合は、`config/lalapadmini.keymap`を編集
- ハードウェア変更には適切な`.overlay`または`.dtsi`ファイルの編集が必要

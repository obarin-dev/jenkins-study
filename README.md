# Jenkins 学習環境

ローカルで Jenkins LTS を動かす最小構成です。学習用。

## 構成

- Jenkins LTS を Docker で起動
- `127.0.0.1:8080` にのみバインド（同一ネットワークの他端末からは見えない）
- データは名前付き volume `jenkins_home` に永続化
- `Jenkinsfile` にパイプライン定義

## 前提

Docker が必要です。Docker Desktop または Colima のどちらでも動きます。

```bash
# Colima を使う場合
brew install colima docker docker-compose
colima start
```

Colima は自動起動しないので、Mac を再起動したら `colima start` が必要です。

## 起動

```bash
docker compose up -d
```

初回はイメージの取得で数分かかります。

### 初期管理者パスワード

```bash
docker compose exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

初回セットアップ画面で入力する32文字の文字列です。起動直後はまだ生成されていないことがあるので、出なければ少し待って再実行してください。

セットアップ完了後は無効になります。

## 基本コマンド

```bash
docker compose ps                  # 状態確認
docker compose logs -f jenkins     # ログ追尾（Ctrl+C で抜ける。コンテナは止まらない）
docker compose stop                # 停止（データは残る）
docker compose start               # 再開
docker compose down                # コンテナ削除（volume は残る）
docker compose down -v             # volume ごと削除（全データ消去）
```

`down -v` はジョブ・プラグイン・ユーザーが全部消えます。やり直したいときだけ。

## 初回セットアップ

1. http://localhost:8080 を開く
2. Unlock Jenkins → 上記の初期パスワードを入力
3. **Install suggested plugins**（Git, Pipeline など定番一式。数分かかる）
4. 管理ユーザーを作成
5. Jenkins URL は `http://localhost:8080/` のまま Save and Finish

## Jenkinsfile

4ステージ構成のサンプルです。

```
準備 → テスト → ビルド → デプロイ
```

`post` ブロックで成功時・失敗時・常時の処理を分岐しています。

テストステージの `exit 1` を有効にすると、後続ステージがスキップされる挙動を確認できます。

## トラブルシュート

| 症状 | 対処 |
|---|---|
| `Cannot connect to the Docker daemon` | Colima なら `colima start`。Docker Desktop なら起動しているか確認 |
| ポート 8080 が使用中 | `lsof -nP -iTCP:8080 -sTCP:LISTEN` で確認。compose の `127.0.0.1:8080:8080` を `127.0.0.1:8081:8080` に変更 |
| 初期パスワードのファイルが無い | まだ起動中。`docker compose logs -f jenkins` で完了を待つ |
| ブラウザで 403 が出る | 正常。未認証だと `/` は 403 を返す。`/login` へ |

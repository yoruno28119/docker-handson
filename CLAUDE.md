# Docker ハンズオン — Claude への作業指示

## プロジェクト概要

.NET アプリを Docker でコンテナ化する学習プロジェクト。
Server（.NET Web API）と PostgreSQL をコンテナ化し、
Client（.NET Windows Forms）はローカルから API に接続する構成。

## アーキテクチャ

| レイヤー | 実装 | コンテナ化 |
|----------|------|-----------|
| Client | .NET Windows Forms（View / ViewModel） | しない |
| Server | .NET Web Application（API） | する |
| DB | PostgreSQL | する |

## ディレクトリ役割

| パス | 役割 |
|------|------|
| `Client/View/` | Windows Forms の画面クラス |
| `Client/ViewModel/` | バインディング・ロジック |
| `Server/` | .NET Web API プロジェクト |
| `compose.yaml` | Server + PostgreSQL の Compose 定義（ルートに配置） |
| `.env` | DB 接続情報などのシークレット（コミット禁止） |
| `.env.example` | 環境変数のサンプル（コミットOK） |
| `.claude/` | Claude Code 設定・カスタムコマンド |

## 作業ルール

- Dockerfile や compose.yaml には **必ずコメントを付ける**（学習目的のため）
- 説明・コメントは **日本語** で行う
- DB のパスワード等は必ず `.env` で管理し、ハードコードしない
- イメージは軽量ベース（`alpine` 系）を優先する
- compose.yaml の変更後は `docker compose config` で検証してから適用する
- Client は Docker 化しない（Windows Forms はコンテナ非対応のため）

## Docker ファイル規約

```
Server/Dockerfile       # .NET Web API のビルド定義
compose.yaml            # ルートに配置
.env.example            # サンプル環境変数（コミットOK）
.env                    # 実際の値（.gitignore に追加）
```

## PostgreSQL 接続情報（.env で管理）

```
POSTGRES_DB=handson
POSTGRES_USER=appuser
POSTGRES_PASSWORD=（各自設定）
```

Server 側の接続文字列は環境変数から読み込む形にすること。

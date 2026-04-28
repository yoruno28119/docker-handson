# Docker ハンズオン学習プロジェクト

.NET アプリケーションを Docker でコンテナ化する実践学習プロジェクトです。

## アーキテクチャ概要

```
┌─────────────────────────────────────────────────┐
│  ローカル環境（コンテナ化しない）                    │
│                                                  │
│  Client/  ← .NET Windows Forms アプリ            │
└───────────────────┬─────────────────────────────┘
                    │ HTTP
┌───────────────────▼─────────────────────────────┐
│  Docker 環境                                     │
│                                                  │
│  ┌──────────────────┐   ┌──────────────────┐    │
│  │  Server           │   │  PostgreSQL       │    │
│  │  .NET Web API     │──▶│  DB              │    │
│  └──────────────────┘   └──────────────────┘    │
└─────────────────────────────────────────────────┘
```

## 技術スタック

| レイヤー | 技術 | コンテナ化 |
|----------|------|-----------|
| クライアント | .NET Windows Forms | しない |
| サーバー | .NET Web Application (API) | する |
| データベース | PostgreSQL | する |

## プロジェクト構成

```
docker-handson/
├── Client/              # .NET Windows Forms
│   ├── View/            # 画面 (Form クラス)
│   └── ViewModel/       # ロジック・データバインディング
├── Server/              # .NET Web API
├── .claude/             # Claude Code 設定
├── compose.yaml         # Server + PostgreSQL の Compose 定義（予定）
├── CLAUDE.md
└── README.md
```

## コンテナ構成（予定）

### compose.yaml イメージ

```yaml
services:
  server:
    build: ./Server
    ports:
      - "8080:8080"
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 5s
      retries: 5

volumes:
  pgdata:
```

## よく使うコマンド

```bash
# Server と DB を起動
docker compose up -d

# ログ確認
docker compose logs -f server

# DB に接続して確認
docker compose exec db psql -U <ユーザー名> -d <DB名>

# 停止・削除（データは残る）
docker compose down

# データごと完全削除
docker compose down -v
```

## 環境変数

`.env.example` をコピーして `.env` を作成し、各値を設定してください。

```bash
cp .env.example .env
```

`.env` はリポジトリにコミットしないこと。

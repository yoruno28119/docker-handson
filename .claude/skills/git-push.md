---
description: 変更をすべてステージ・コミット・プッシュし、任意でプルリクエストを作成する。main/master へのプッシュは禁止。
---

以下の手順で Git 操作を行ってください。
環境は **Windows（PowerShell）** を前提とします。

## Step 1: ブランチ確認（main/master は即中断）

PowerShell ツールで実行する：

```powershell
git branch --show-current
```

現在のブランチが `main` または `master` の場合は**ここで必ず中断**し、
以下のメッセージをユーザーに伝えてください：

> main/master への直接プッシュは禁止されています。
> 新しいブランチを作成してから再実行してください。
> 例: `git checkout -b feature/your-branch-name`

## Step 2: 変更状況の確認

```powershell
git status
git diff --stat HEAD
```

## Step 3: 除外ファイルの確認

```powershell
git status --ignored --short
```

除外されているファイルを以下の形式で必ずユーザーに提示する：

```
### .gitignore により除外されたファイル
- path/to/file  ← 除外理由（例: .env はシークレット保護のため）
（なければ「除外ファイルなし」と明記）
```

手動でステージしないファイルがある場合も同様に明記する。

## Step 4: コミットメッセージの確認

`$ARGUMENTS` にメッセージが指定されていればそれを使用する。
指定がない場合はユーザーにコミットメッセージを確認してから進める。

## Step 5: ステージ・コミット・プッシュ

PowerShell ツールで実行する（ヒアストリングで改行を含むコミットメッセージを渡す）：

```powershell
git add -A
git status  # ステージ内容を再確認してからコミット
git commit -m @'
<確認済みのメッセージ>

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
'@
git push -u origin <現在のブランチ名>
```

## Step 6: プルリクエストの作成確認

プッシュ完了後、ユーザーに以下を確認する：

> プルリクエストも作成しますか？（ベースブランチ: `main`）

「はい」の場合は Step 7 へ進む。「いいえ」の場合は Step 8 へ進む。

## Step 7: プルリクエストの作成

### gh コマンドの呼び出し方（Windows 注意事項）

- `gh` が PATH に通っていない場合はフルパスで呼び出す
- PowerShell でスペースを含むパスの実行には `&` が必要

```powershell
# gh が PATH に通っている場合
gh pr create ...

# 通っていない場合（GitHub CLI のデフォルトインストール先）
& "C:\Program Files\GitHub CLI\gh.exe" pr create ...
```

まず `gh` で試し、`command not found` になったらフルパスに切り替える。

### 未認証の場合

```powershell
& "C:\Program Files\GitHub CLI\gh.exe" auth login
```

ブラウザ認証（Login with a web browser）を選択し、表示されるワンタイムコードを入力する。

### PR 作成コマンド（PowerShell ヒアストリング形式）

```powershell
& "C:\Program Files\GitHub CLI\gh.exe" pr create --title "<コミットメッセージと同じ>" --body @'
## Summary
- <変更内容を箇条書き>

## Test plan
- [ ] 動作確認済み

🤖 Generated with [Claude Code](https://claude.com/claude-code)
'@
```

作成後、PR の URL をユーザーに伝える。

## Step 8: 結果の報告

以下の形式でまとめて報告する：

```
### プッシュ完了レポート

**ブランチ:** feature/xxx
**コミット:** <hash> - <メッセージ>

**プッシュしたファイル:**
- 追加: xxx
- 変更: xxx
- 削除: xxx

**除外したファイル:**
- xxx （理由: .gitignore）
- （なければ「なし」）

**プルリクエスト:** <URL または「作成しない」>
```

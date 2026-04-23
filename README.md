# タスクボード

シンプルなタスク管理アプリです。

## デモ

https://shincom1974.github.io/task-board/

## 機能

- タスクの追加（テキスト入力 / Enter キー対応）
- チェックボックスで完了・未完了の切り替え
- タスクの削除
- 完了済みタスクのグレー表示・打ち消し線
- ページを閉じてもデータが残る（localStorage 保存）

## 技術スタック

| 項目 | 内容 |
|------|------|
| フロントエンド | HTML / CSS / React 18 |
| React の読み込み | CDN + Babel Standalone |
| データ保存 | localStorage |
| ビルドツール | なし（index.html を直接開くだけで動作） |

## ファイル構成

```
task-board/
├── index.html   # エントリーポイント（React・Babel を CDN から読み込み）
├── app.js       # React コンポーネント（JSX）
├── style.css    # スタイル
└── README.md
```

## ローカルで動かす

```bash
# Python が使える場合
python -m http.server 8080

# Node.js が使える場合
npx serve .
```

ブラウザで http://localhost:8080 を開いてください。

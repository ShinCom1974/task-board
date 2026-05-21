# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

**task-board** — タスク管理ボードアプリ

技術スタック: HTML / CSS / React 18（CDN + Babel Standalone、ビルドツールなし）

## 開発の進め方

ビルドツールは使用しないため、ブラウザで `index.html` を直接開くだけで動作確認できます。

ローカルサーバーを使う場合（CORS対策など）:

```bash
# Python が使える場合
python -m http.server 8080

# Node.js が使える場合
npx serve .
```

## アーキテクチャ方針

- サーバーサイド不要のピュアフロントエンド構成
- React は CDN + Babel Standalone で読み込み（npm・ビルド不要）
- タスクデータは `localStorage` で永続化する
- コンポーネント構成: `App` → `TaskInput` / `TaskList` → `TaskItem`

## 技術スタック

| 項目 | 内容 |
|------|------|
| マークアップ | HTML5 |
| スタイル | CSS3（外部フレームワークなし） |
| UIライブラリ | React 18 |
| JSX変換 | Babel Standalone（ブラウザ内でリアルタイム変換） |
| 状態管理 | React `useState` / `useEffect`（ライブラリなし） |
| データ永続化 | `localStorage` |
| ビルドツール | なし |
| パッケージ管理 | なし（CDN経由で読み込み） |

## コンポーネント命名規約

### コンポーネント名
- **パスカルケース**（先頭大文字）を使用する
- 例: `App`, `TaskInput`, `TaskList`, `TaskItem`

### props 名
- **キャメルケース**を使用する
- 例: `onAdd`, `onToggle`, `onDelete`

### イベントハンドラ名
- `handle` を接頭辞とするキャメルケースを使用する
- 例: `handleAdd`, `handleKeyDown`

### CSS クラス名
- **ケバブケース**（ハイフン区切り）を使用する
- 例: `task-input`, `task-item`, `delete-btn`

## GitHubリポジトリ

https://github.com/ShinCom1974/task-board

## デプロイ先

https://shincom1974.github.io/task-board/

## Git 運用ルール

**コードを変更するたびに、必ず GitHub にプッシュすること。**

```bash
git add .
git commit -m "変更内容を簡潔に記述"
git push origin main
```

### コミットメッセージの書き方

| 接頭辞 | 用途 |
|--------|------|
| `feat:` | 新機能追加 |
| `fix:` | バグ修正 |
| `style:` | UI/スタイル変更 |
| `refactor:` | リファクタリング |
| `docs:` | ドキュメント更新 |

例: `feat: タスクの追加・削除機能を実装`

### ブランチ戦略

- `main` ブランチを常に動作する状態に保つ
- 作業はすべて `main` に直接コミットして push する（シンプルな個人プロジェクトのため）

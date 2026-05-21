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

### コンポーネント詳細

#### `App`
- **役割**: アプリ全体の状態を管理するルートコンポーネント。タスクの追加・完了切り替え・削除の各処理を定義し、子コンポーネントに渡す。`useEffect` で tasks の変化を検知して `localStorage` に保存する。
- **props**: なし（ルートコンポーネントのため）
- **state**:
  - `tasks`: タスクの配列。各要素は `{ id: number, text: string, done: boolean }` の形式

#### `TaskInput`
- **役割**: テキスト入力欄と「追加」ボタンを表示する。入力値を state で管理し、追加操作時に親へ通知する。Enter キーでも追加できる。
- **props**:
  - `onAdd(text)`: タスクを追加するときに呼ぶ関数（親 `App` から渡される）
- **state**:
  - `text`: 入力欄の現在の文字列

#### `TaskList`
- **役割**: タスクの一覧を表示する。タスクが0件のときは「タスクがありません」と表示する。各タスクを `TaskItem` に渡してレンダリングする。
- **props**:
  - `tasks`: タスクの配列（親 `App` から渡される）
  - `onToggle(id)`: 完了・未完了を切り替えるときに呼ぶ関数
  - `onDelete(id)`: タスクを削除するときに呼ぶ関数
- **state**: なし

#### `TaskItem`
- **役割**: 1件のタスクを表示する。チェックボックス・タスク名・削除ボタンで構成される。完了済みの場合は CSS クラス `done` を付与してグレー表示にする。
- **props**:
  - `task`: 1件のタスクオブジェクト `{ id, text, done }`
  - `onToggle(id)`: チェックボックス変更時に呼ぶ関数
  - `onDelete(id)`: 削除ボタン押下時に呼ぶ関数
- **state**: なし

## コーディング規約

### 関数・変数の命名規則

| 種類 | 規則 | 例 |
|------|------|----|
| コンポーネント名 | パスカルケース | `App`, `TaskInput`, `TaskList`, `TaskItem` |
| 関数・変数 | キャメルケース | `addTask`, `toggleTask`, `remaining` |
| イベントハンドラ | `handle` + 動詞のキャメルケース | `handleAdd`, `handleKeyDown` |
| コールバック props | `on` + 動詞のキャメルケース | `onAdd`, `onToggle`, `onDelete` |
| 真偽値のプロパティ | 形容詞または過去分詞 | `done`（`isCompleted` より短く自然な形） |
| CSS クラス名 | ケバブケース | `task-item`, `delete-btn`, `task-input` |

### コメントの書き方方針

- **基本はコメントを書かない**。変数名・関数名で意図が伝わるように命名する
- CSS では視覚的なブロック区切りに `/* --- セクション名 --- */` 形式を使う
- 書く場合は「何をしているか」ではなく「なぜそうしているか」を1行で書く

```js
// OK: 理由を書く
// 空文字を弾いてから追加（スペースのみのタスクを防ぐ）
const trimmed = text.trim();

// NG: コードを日本語にしただけ
// テキストをトリムする
const trimmed = text.trim();
```

### React のベストプラクティス

**state はイミュータブルに更新する**
```js
// OK: 新しい配列を返す
setTasks(prev => [...prev, newTask]);
setTasks(prev => prev.filter(t => t.id !== id));
setTasks(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t));

// NG: 直接変更する
tasks.push(newTask);
tasks[0].done = true;
```

**リストには必ず `key` を付ける。インデックスは使わない**
```jsx
// OK: ユニークな id を使う
tasks.map(task => <TaskItem key={task.id} ... />)

// NG: インデックスを使う（並び替え・削除で不具合が起きる）
tasks.map((task, index) => <TaskItem key={index} ... />)
```

**副作用は `useEffect` で管理する**
```js
// OK: localStorage への書き込みは useEffect 内で行う
useEffect(() => {
  localStorage.setItem('tasks', JSON.stringify(tasks));
}, [tasks]);
```

**state は必要な最上位コンポーネントだけに持つ**
- `tasks` は `App` だけが管理し、子コンポーネントは props で受け取るだけにする
- 子コンポーネントが自分で state を持つのは、そのコンポーネント内で完結する値のみ（例: `TaskInput` の `text`）

### やってはいけないこと

- **DOM を直接操作しない** — `document.getElementById` や `innerHTML` は使わず、React の state と JSX で画面を制御する
- **state を直接変更しない** — `tasks.push()` や `task.done = true` のような破壊的操作は行わない
- **`key` にインデックスを使わない** — 削除・並び替え時に React の差分検知が壊れる
- **コンポーネント外で state を管理しない** — グローバル変数にタスクを持たせない
- **1つのコンポーネントに複数の責務を持たせない** — 表示・入力・リスト管理はそれぞれ別コンポーネントに分ける

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

## トラブルシューティング

### アプリが真っ白で何も表示されない（CORS エラー）

**原因**: `index.html` をブラウザで直接ダブルクリックして開いた場合、`file://` プロトコルになる。Babel が `app.js` を fetch しようとしても CORS ポリシーに引っかかり、JSX が読み込めない。

**解決策**: ローカルサーバー経由で開く。

```bash
python -m http.server 8080
# → http://localhost:8080 で開く
```

ブラウザの開発者ツール（F12）→ Console タブに以下のようなエラーが出ていたら CORS が原因。

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading...
```

---

### ページを再読み込みしたらタスクが消えた（localStorage）

**原因1**: シークレットモード（プライベートブラウジング）で開いている。シークレットモードでは localStorage が無効になるブラウザがある。

**解決策**: 通常モードのウィンドウで開く。

**原因2**: localStorage のデータが壊れて JSON.parse に失敗している。

**解決策**: 開発者ツール → Application タブ → Local Storage → `tasks` キーを削除してリロードする。このアプリは `try/catch` で壊れたデータを自動的に無視して空配列で起動するため、削除後は正常に動作する。

**注意**: localStorage はブラウザ・端末ごとに独立している。別のブラウザや別の PC ではデータは共有されない。

---

### CDN が読み込めず React/Babel が動かない

**原因**: オフライン環境、または企業・学校のネットワークが外部 CDN をブロックしている。

**確認方法**: 開発者ツール → Network タブ を開いてリロードし、`react.development.js` や `babel.min.js` の行が赤くなっていないか確認する。

**解決策**: オンラインに接続できる環境で開く。企業ネットワークの場合はネットワーク管理者に `unpkg.com` へのアクセス許可を依頼する。

---

### 初回読み込みが遅い

**原因**: Babel Standalone（約1MB）を CDN からダウンロードしたうえで、ブラウザ内で JSX をリアルタイム変換するため、通常の React アプリより初回表示が遅い。

**これは仕様**: このプロジェクトはビルドツールなしの学習用構成のため許容する。本番サービス向けには Vite 等でビルドする構成に切り替えることを検討する。

---

### ローカルサーバーが起動できない（ポート競合）

**原因**: ポート 8080 が別のプロセスで使用中。

**解決策**: 別のポート番号を指定する。

```bash
python -m http.server 3000
# → http://localhost:3000 で開く
```

---

### GitHub Pages にプッシュしたのに反映されない

**原因1**: GitHub Pages のビルドと公開に数分かかる。

**解決策**: リポジトリの Actions タブでデプロイの進行状況を確認する。緑のチェックマークが付いたら反映済み。

**原因2**: GitHub Pages の設定が正しくない。

**確認手順**:
1. リポジトリの Settings → Pages を開く
2. Branch が `main` / `/ (root)` になっているか確認する
3. 設定を変更した場合は Save を押して数分待つ

**原因3**: push した内容に構文エラーがある。

**解決策**: ローカルでサーバーを起動して動作確認してから push する。

---

### JSX の変更がブラウザに反映されない

**原因**: ブラウザがキャッシュした古いファイルを表示している。

**解決策**: ハードリロード（キャッシュ無視の再読み込み）を行う。

| OS | 操作 |
|----|------|
| Windows | `Ctrl + Shift + R` |
| Mac | `Cmd + Shift + R` |

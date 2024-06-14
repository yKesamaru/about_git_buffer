# 【今更】`Git`のバッファサイズを変更する

## はじめに
`2024-06-14 17:23:03.176 [info] error: RPC failed; HTTP 400 curl 22 The requested URL returned error: 400`
このエラーで`GitHub`に`push`出来ませんでした。
今更感がありますが、この問題に対処する過程で知った事柄やどのような手順で解決したかを残します。

## 環境
```bash
$ inxi -Sxxx --filter
System:
  Kernel: 6.5.0-35-generic x86_64 bits: 64 compiler: N/A Desktop: Unity
    wm: gnome-shell dm: GDM3 42.0 Distro: Ubuntu 22.04.4 LTS (Jammy Jellyfish)
```

## 前提
- ネット接続に問題はない（ファイアウォール等含める）
- [GitHub Status](https://www.githubstatus.com/)にも問題はない
  - ![](https://raw.githubusercontent.com/yKesamaru/about_git_buffer/master/assets/2024-06-14-17-56-42.png)


## `Git`のバッファサイズについて
`GitHub`に`push`するとき、1つのファイルサイズの上限は`100MB`です。またリポジトリ全体の制限は`1GB`です。
これだけに気を取られていましたが、1度に`push`するファイル数が多すぎる場合も問題が発生するようです。（この上限値はわかりません）
この時問題となるのが`http.postBuffer`のサイズだということです。
デフォルトでは`1MB`とのことです。この値を取得するには以下のようにします。
```bash
git config --get http.postBuffer
```
なにも出力されなければデフォルト値(`1MB`)です。

対策として、まずこの値を大きく設定します。
例えばバッファーサイズを`150MB`にする場合は以下のようになります。
`git config --global http.postBuffer 157286400`

### 補足
#### 150MBの根拠
- データ転送上、無理のない大きさ
- エラー発生時に困らない大きさ
#### `--global`オプション
`Git`の設定の適用範囲
-  `--global`
   - ユーザー全体の設定範囲
- `--local`
  - 現在のリポジトリにのみ適用
- `--system`
  - システム全体の設定範囲
#### `http.postBuffer`以外の設定項目
`Git`の一般的な設定項目

| 設定項目 | 説明 | コマンド例 |
| --- | --- | --- |
| `user.name` | コミットの作者名を設定 | `git config --global user.name "Your Name"` |
| `user.email` | コミットの作者メールアドレスを設定 | `git config --global user.email "your.email@example.com"` |
| `core.editor` | コミットメッセージの編集に使用するエディタを設定 | `git config --global core.editor "vim"` |
| `merge.tool` | マージ時に使用するツールを設定 | `git config --global merge.tool "meld"` |
| `diff.tool` | 差分表示時に使用するツールを設定 | `git config --global diff.tool "meld"` |
| `core.autocrlf` | 行末変換を自動的に行う設定 | `git config --global core.autocrlf true` (Windows), `git config --global core.autocrlf input` (Unix系) |
| `pull.rebase` | `git pull`の動作を設定し、プル時にリベースを行う | `git config --global pull.rebase true` |
| `color.ui` | Gitの出力にカラーを使用する設定 | `git config --global color.ui auto` |
| `credential.helper` | 認証情報をキャッシュする設定 | `git config --global credential.helper cache` |
| `merge.conflictStyle` | マージ時の競合表示スタイルを設定 | `git config --global merge.conflictStyle diff3` |
| `diff.ignoreSubmodules` | サブモジュールの差分を無視する設定 | `git config --global diff.ignoreSubmodules dirty` |


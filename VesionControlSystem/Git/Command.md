## よく使うGitコマンド

### カレントディレクトリにgitを導入する
```
git init
```

### 現在の作業ディレクトリの状態を取得する
```
git status
```

### gitに管理対象のファイルをステージングに追加する
```
git add [ファイル名]
```

#### カレントディレクトリ配下を全てをステージングに追加する場合は以下でOK
```
git add .
```

### ステージングの内容をまとめてローカルリポジトリに反映する
```
git commit -m "コミットコメント"
```

#### コミットする人の情報を確認する

コミット者の情報は３種類あり、以下のような優先順位となっている。

1. local（カレントディレクトリのリポジトリのみで使用される）
2. global（マシンにログインしているユーザーの情報）
3. system（マシンの全ユーザー共通で使用される）

全て設定されていない状態でコミットを行おうとするとエラーが発生するため
global、systemレベルでの設定は削除しておいて、面倒でも毎回localレベルの設定を付与すると安全。

##### 各レベルの情報を確認
```
git config --local --list
git config --global --list
git config --system --list
```

##### 各レベルの情報を削除
```
git config --local --unset [削除する情報]
git config --global --unset [削除する情報]
git config --system --unset [削除する情報]
```

##### 各レベルの情報を設定（主にuser.nameとuser.emailはセキュリティ上、気をつけたいところ）
```
git config --local user.email [設定する情報]
git config --global user.email [設定する情報]
git config --system user.email [設定する情報]
```

### コミットした変更を取り消す（コミット直前に戻す）
```
git reset --soft HEAD^
```

### コミット履歴を確認する
```
git log
```

#### 取得件数を指定してログを取得する
```
git log -[件数]
```
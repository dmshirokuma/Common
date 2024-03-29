## よく使うGitコマンド

### カレントディレクトリにgitを導入する
```
git init
```

### 現在の作業ディレクトリの状態を取得する
```
git status
```

### 作業ディレクトリで使用するコマンド

#### 作業ディレクトリ配下を全てをステージングに追加する
```
git add .
```

#### 作業ディレクトリ配下を全てをステージングに追加する（ファイル単位）
```
git add [ファイル名]
```

#### 作業ディレクトリ配下のファイルを削除する（ファイル単位）
```
git rm [ファイル名]
```

#### 作業ディレクトリの内容を取り消す（全体）
```
git checkout .
```

#### 作業ディレクトリの内容を取り消す（ファイル指定）
```
git restore --worktree [ファイル名]
```

#### 追跡中のファイルを消す
```
git clean -dn //これで消すファイルを一度確認する
git clean -df //問題なければこれで消す
```

### ステージングされたファイルで使用するコマンド

#### ステージングの内容をまとめてローカルリポジトリに反映する
```
git commit -m "コミットコメント"
```

#### ステージングされた内容を取り消す（ファイル指定）
```
git restore --staged [ファイル名]
```

#### 直前にコミットした変更を取り消す（コミット直前に戻す）
```
git reset --soft HEAD^
```

#### 直前にコミットした変更を取り消す（完全取り消し）
```
git reset --hard HEAD^
```
本コマンドを使用して予期しない戻り方をしてしまった場合、reflogから復元できる。

```
git reflog
```
にて対象のハッシュを指定し、reset hardすればOK
```
git reset --hard [reflogで確認したハッシュ]
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

### リモート

#### ローカルブランチのコミットをリモートブランチに反映する

```
git push origin main
```
「origin」によってリモートのブランチを指定することが可能。
一回指定すれば以降は
```
git push
```
だけでOK

ただ実際にはローカルブランチとリモートブランチの間に追跡ブランチというものがあり、
pushは追跡ブランチとリモートブランチの双方を更新する。

#### リモートブランチの内容をローカルブランチに反映する
```
git pull
```
pullは内部的にはfetch、mergeを同時に行っているため、細かいことをやろうとするとハマることがある。

そのため
```
git fetch
git merge
```
をそれぞれ使うという手もあり。

ちなみに
```
fetchはリモートブランチ→追跡ブランチへの反映
mergeは追跡ブランチ→ローカルブランチへの反映
```
のイメージ

### リモートブランチを削除する
```
git ls-remote
git push origin --delete [リモートブランチ名]
```
最初のコマンドでリモートブランチを確認後
リモートブランチ「refs/heads/feature」を削除したい場合は
```
git push origin --delete feature
```
とする

### ブランチ操作

#### ブランチ一覧を表示（ローカルブランチのみ）
```
git branch
```

#### ブランチ一覧を表示（全体）
```
git branch -a
```
全体と言いつつ、実のところ表示しているのはローカルブランチと追跡ブランチのみでリモートブランチは表示されていない。

#### ローカルブランチと追跡ブランチの詳細を表示
```
git branch -vv
```
全体と言いつつ、実のところ表示しているのはローカルブランチと追跡ブランチのみでリモートブランチは表示されていない。

#### ブランチを削除（ローカルブランチ）
```
git branch -D [ローカルブランチ名]
```

#### ブランチに移動
```
git checkout [ローカルブランチ名]
```

#### 現在のブランチを元にして新規ブランチを作成して移動
```
git checkout -b [ローカルブランチ名]
```

#### ブランチの内容をマージ
```
git checkout [マージ先ローカルブランチ名] //マージ先ブランチに移動
git merge [マージ元のローカルブランチ名] //マージ元の内容をマージ先に取り込み

ex. 「main」ブランチに「feature/xxx」の内容をマージしたい場合
git checkout main
git merge feature/xxx
```

#### リベース
```
git checkout [変更を取り込みたいブランチ]
git pull // 変更を取り込みたいブランチを最新化
git checkout [リベースしたいブランチ]
git pull // リベースしたいブランチを最新化
git rebase [変更を取り込みたいブランチ]

ex masterの内容をfeature/xxxブランチに取り込んでリベースしたい場合
git checkout master
git pull // 「master」を最新化
git checkout feature/xxx
git pull // 「feature/xxx」ブランチを最新化
git rebase master //「master」の内容を「feature/xxx」ブランチに取り込んでリベース
```

ちなみにリベースを行った後はリベースによって元ブランチ移行の変更には別のハッシュが与えられるため
そのままリモートへpushすることはできない。（pushはFast-Forwardが前提となっているため）
そのため、以下のコマンドを使用して強制的にリモートへ反映させることが必要。
```
git push --force-with-lease
```

###　履歴

#### 特定のコミットの状態を取得する
```
git log //対象のコミットのハッシュを確認
git checkout [コミットのハッシュ]
```

#### コミット履歴を確認する
```
git log
```

#### 取得件数を指定してログを取得する
```
git log -[件数]
```

### 待避

#### 現在の内容を待避する
```
git stash -u
```
作業が中途半端なのでコミットはしたくないが、一旦待避しておきたい場合などに使用する
「-u」オプションをつけることにより、追跡対象ファイル以外でも待避できる。

#### 待避した内容を確認する
```
git stash list
```

#### 待避した内容を復元する
```
git stash pop --index
```
「-index」オプションをつけることにより、ステージングされた内容もそのまま復元できる

#### 待避した内容を全て消す
```
git stash clear
```

### 現在のローカルブランチの内容をリモートブランチに反映
```
git push --force-with-lease
```

※「git push -f」を使う方法もあるが、リモートブランチを強制的に上書きしてしまうため、自分以外の誰かがpushしていた場合にその変更が消えてしまう。
そのため、基本的には使ってはいけない。

「git push --force-with-lease」を使用することによってローカルブランチの履歴がリモートブランチの履歴より
新しい場合のみ上書きするためこちらを使用する。


### 複数コミットをまとめる

ローカルで作業をしていると同じ作業に対して複数のコミットが発生し、そのままリモートブランチに反映してしまうと
リモートブランチのコミット履歴が複雑になってしまう。
そのため複数のコミットを１つにまとめたコミットを作成し、リモートブランチにpushする。
この操作をsquashという。

 ```
 git log //まとめたいコミットを確認
 git rebase -i HEAD^[まとめたいコミット数] //このコマンドの場合はHEADからまとめる
 ```

 エディタが起動するので対応する //squashしたいコミットを選択する

 ex.
```
pick 7c65355 xxx対応
pick 2639543 追加対応
```

のような感じで表示されるので「追加対応」を「xxx対応」としてまとめたい場合は↓のようにエディタで修正する

```
pick 7c65355 xxx対応
squash 2639543 追加対応
```

エディタを閉じる

再度エディタが起動するので次はどのコミットメッセージをまとめたコミットに採用するかを決める
```
# This is a combination of 2 commits.
# The first commit's message is:
xxx対応
# This is the 2nd commit message:
追加対応
```
「xxx対応」をコミットメッセージとして採用したい場合は↓のように不要なメッセージを削除して保存後、エディタを閉じる
```
# This is a combination of 2 commits.
# The first commit's message is:
xxx対応
```

まとめ終わったら「git log」で問題ないことを確認し、
「git push --force-with-lease」でリモートブランチに反映する。
（新しいコミットとなるがまとめられたコミットは無かったことになる（厳密には無くなっていないが）ため、強制的にリモートに反映させる必要がある）

### Globalに.gitignoreを設定する

以下にglobalにgitに含めたくないファイルを指定する（.DS_Storeとか）

```
/Users/hirokuma/.config/git/ignore
```
# exe_Github
## 夏休み演習

## コンフリクトの起こし方とその対処法
コンフリクトはコミット履歴を持つ2つ以上のブランチを統合する際に起こる競合のことである。

### コンフリクトを起こしてみる
現在のブランチ
```
╰─ git branch -v 
* git-conflict f6c2f4e Merge pull request #1 from Nekozawa0517/update_readme
  main         e6a4a30 [behind 3] test
```

git-conflictブランチから2つのブランチを生やしてみる
```
╰─ git branch feature/1

╰─ git branch feature/2

╰─ git branch -v
  feature/1    f6c2f4e Merge pull request #1 from Nekozawa0517/update_readme
  feature/2    f6c2f4e Merge pull request #1 from Nekozawa0517/update_readme
* git-conflict f6c2f4e Merge pull request #1 from Nekozawa0517/update_readme
  main         e6a4a30 [behind 3] test
```

feature/1ブランチに入ってindex.htmlファイルを編集してみる。
```
╰─ git checkout feature/1
M	README.md
Switched to branch 'feature/1'

╰─ cat index.html 
<h1>Hello</h1>
```

add & commitする
```
╰─ git add index.html 

╰─ git commit -m "helloを追加"
```

git-conflictブランチに戻って、feature/1ブランチをマージする
```
╰─ git checkout git-conflict
M	README.md
Switched to branch 'git-conflict'
Your branch is up to date with 'origin/main'.

╰─ git merge feature/1
Updating f6c2f4e..b97b569
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
```

ここではマージは成功した。
次に、feature/2でindex.htmlを編集してみる。
```
╰─ git checkout feature/2
M	README.md
Switched to branch 'feature/2'

╰─ cat index.html
<h1>good morning</h1>
```

add & commitする
```
╰─ git add index.html 

╰─ git commit -m "good morningを追加"
```

git-conflictブランチに戻って、feature/1ブランチをマージする
```
╰─ git checkout git-conflict
M	README.md
Switched to branch 'git-conflict'
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

╰─ git merge feature/2
Auto-merging index.html
CONFLICT (add/add): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

ここでfeature/2ブランチをマージしようとしたら、コンフリクトが起きた。

### 対処法
```
╰─ git merge feature/2
Auto-merging index.html
CONFLICT (add/add): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

このようにコンフリクトが起きた場合は、コンフリクトが発生しているファイルを修正する必要がある。

```
╰─ git status

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both added:      index.html
```

Unmerged pathsでまだマージができていないファイルを探す。

index.htmlを修正する。

修正前
```
╰─ cat index.html 
<<<<<<< HEAD
<h1>Hello</h1>
=======
<h1>good morning</h1>
>>>>>>> feature/2
```

"<<<<<<" HEAD の下の行が現在の状態

"=======" で現在の状態とマージしようとしたブランチの状態を分けている

">>>>>>" feature/2ブランチの状態

これをfeature/2ブランチの状態にしたいので、以下のように編集する。
```
╰─ cat index.html 
<h1>good morning</h1>
```

"<<<<<<"や">>>>>>"、"======="は削除して良い

add % commit % push する
```
╰─ git add index.html 

╰─ git commit -m "resolve conflict"

╰─ git push -u origin git-conflict
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (7/7), 646 bytes | 646.00 KiB/s, done.
Total 7 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), done.
remote: 
remote: Create a pull request for 'git-conflict' on GitHub by visiting:
remote:      https://github.com/Nekozawa0517/exe_Github/pull/new/git-conflict
remote: 
To https://github.com/Nekozawa0517/exe_Github.git
 * [new branch]      git-conflict -> git-conflict
branch 'git-conflict' set up to track 'origin/git-conflict'.
```

pushが完了した。


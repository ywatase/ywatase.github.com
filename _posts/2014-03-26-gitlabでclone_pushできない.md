---
layout: post
title:  "CentOS 6.5でgitlabサーバに/usr/local/binにgitを入れたらgitlabでclone/pushできない"
date:   2014-03-26 20:00
categories: gitlab
---

# 結論

* gitは/usr/local/bin/gitのみに存在
* sidekiqが動かしているjobの中に'git init --bare'してるところがある。
* sidekiqのプロセスには/usr/local/binへのパスが通っていないため、子プロセスもgitが見つからない

というわけで、対処法はパッと思いついたので2つ。

+ gitのインストール先を変える
+ /usr/local/bin へのパスが通るようにgitlabの起動スクリプトでPATHに追加する

sudoersのsecure_pathに書くのもありかと思ったけど、手元では解決しなかったので保留

# 再現環境

| 環境   | バージョン |
|:-------|:-----------|
| gitlab | 6.6.5      |
| CentOS | 6.5        |
| git    | 1.9.0      |

rpmのgitは入れずに、gitをソースから/usr/local/binにインストールしているため、/usr/local/binにパスが通っていないとgitが使えない

gitlab.ymlのbin_path

# 詳細

gitlabでリポジトリを作ってプッシュしようとしたらこんなメッセージが出ました。

```
$ git push origin master
fatal: '/home/git/repositories/share/test.git' does not appear to be a git repository
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

gitlab/lib/backup/repository.rb を見てみると

	if system(*%W(git clone --bare #{path_to_bundle(project)} #{path_to_repo(project)}), silent)

config の git.bin_path 見てない模様。まぁいじるなってコメントで書いてるところだし。
他にも多数あるので、/usr/local/bin/gitを使わせるには、PATHで優先させるしかない。

では、gitlabの起動スクリプトがPATHを設定しているところを見てみると

	sudo -u "$app_user" -H -i $0 "$@"; exit;

という感じでgitユーザーになって、再度 init scriptを実行している。
PATHを比較してみると。。。

	[root@hostname]$ sudo -u "git" -H -i bash -c 'echo $PATH'
	/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/home/git/bin
	[git@hostname]$ echo $PATH
	/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/git/bin

/usr/local/binがない。
でも、/usr/local/sbinはあるし順番も変だし謎

ということで最初の結論にでした。

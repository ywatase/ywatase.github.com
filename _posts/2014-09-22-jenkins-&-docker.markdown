---
layout: post
title: "jenkins & docker"
published: true
---

jenkins でdockerコマンドを実行しようとしたら、動かなかった。
jenkinsをdockerグループに入れたら動いた

```
usermod -aG docker jenkins
```

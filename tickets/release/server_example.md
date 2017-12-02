# リリース内容

* 店舗検索API でリクエストパラメータ に xxフラグ を追加する

# 前提条件

* 受入試験が完了し、Staging環境でのリハーサルも終わっていること
* リリース用Jenkinsへのアカウントを持っていること
* 納品用のgitリポジトリへのアクセス権があること

# 事前準備

* developブランチからreleaseブランチを作成し、リリースタグを付与
* 前回リリースタグとの差分を確認
* リリースタグを{顧客}のgitリポジトリにpush

コマンド

```
git checkout -b release/release-{yyyymmdd}-1
git tag -a release-{yyyymmdd}-1
git diff {前回リリースタグ} {今回リリースタグ}
git push {顧客リポジトリ} release/release-{yyyymmdd}-1
git push {顧客リポジトリ} tag {今回リリースタグ}
```

# リリース手順

* {顧客}にリリース開始の旨を連絡し、合意を得てから実施
* Jenkinsからビルドする
    * {JenkinsJobのURL} にアクセス
    * {今回リリースタグ} を指定し、ビルド・デプロイを実行


# 動作確認手順

* 以下のURLにリクエストし、〇〇店がレスポンスに含まれ、☓☓店が含まれないこと

```
curl -X GET 'http://{domain}/{path}?{xxフラグ}={value}
```

* ログサーバでログを確認し、CRITICALレベルのログが出ていないこと

もし所望の動作でなかったり、エラーが発生している場合は切り戻し手順を実行する

# 切り戻し手順

* {顧客}に問題が発覚し、切り戻しを行う旨を連絡する
* Jenkinsからビルド・デプロイする
    * {JenkinsJobのURL} にアクセス
    * {前回リリースタグ} を指定し、ビルド・デプロイを実行


# 事後作業手順

* 顧客にリリース完了を連絡する
* リリースブランチをmasterブランチにマージし、{顧客}のgitリポジトリにpush


```
git checkout master
git reset --hard {顧客リポジトリ}/master
git merge release/release-{yyyymmdd}-1
git merge {顧客リポジトリ} master
```
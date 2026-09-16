# go-only ブランチの変更記録

main(1011682c)をベースに、Go 実装だけで練習・ベンチ実行できる最小構成へ削ぎ落とした。

## 方針

- 残す: `webapp/go`・`webapp/sql`・`webapp/public`・`webapp/frontend`・webapp 直下の共通ファイル、`bench`、`docs`、`extra`、development の Go/MySQL/jiaapi-mock 構成、provisioning の Go 実行に必要な部分
- 削る: Go 以外の言語実装と、その言語のためだけに存在するセットアップ(ランタイムインストール、systemd unit、docker-compose サービス、CI)

## 削除したもの(コミット 638bf454, ba26a48e)

- `webapp/{nodejs,perl,php,python,ruby,rust}`
- `development/backend-{nodejs,perl,php,python,ruby,rust}`
- `provisioning/ansible/roles/langs.{nodejs,perl,php,python,ruby,rust}`
- `provisioning/ansible/roles/contestant/tasks/isucondition-{nodejs,perl,php,python,ruby,rust}.yml`
- `provisioning/ansible/roles/contestant/files/etc/systemd/system/isucondition.{nodejs,perl,php,python,ruby,rust}.service`
- `provisioning/ansible/roles/contestant/files/etc/nginx/sites-available/isucondition-php.conf` と `files/home/isucon/local/php`(php-fpm 設定)
- `.github_/workflows/{nodejs,perl,php,python,ruby,rust}.yml`

## 参照の後始末

- `development/Makefile`: 他言語の `up-*` / `test-*` ターゲットを削除
- `development/docker-compose-dev.yml`: backend-go / mysql-backend / jiaapi-mock のみに整理、rust-target volume を削除
- `provisioning/ansible/site.yml`: 各プレイから `langs.<他言語>` ロールを削除(`langs.go` と、Go のインストールに使う xbuild は維持)
- `provisioning/ansible/roles/contestant/tasks/isucondition.yml`: 他言語タスクの include と systemd unit 配布を削除
- `provisioning/ansible/roles/contestant/tasks/nginx.yml`: isucondition-php.conf の配布行を削除
- `.dockerignore`: rust/nodejs/php の ignore 行を削除
- `.github_/label-path-mapping.yml`: 他言語パスの transplant ラベルを削除

## 残置(意図的に触っていないもの)

- `docs/manual.md` 内の言語切替に関する記述(ドキュメントのため)
- `timezone.yml` 等のシェルワンライナーの `perl -pi -e`(システムの perl を使うだけで、言語セットアップとは無関係)
- 各種 `.pem` ファイル(ベンチ用テスト鍵)

## 動作確認

- `webapp/go` で `go build` が成功することを確認済み

# aws-penetration-test

AWS 上のマルチテナント Web アプリケーションを、攻撃者の視点で静的に調べる Cursor 用の手順です。アプリケーション本体は含みません。手順・エージェント・スキルだけを置いています。

想定する開発環境は次のとおりです。対象リポジトリの実装が違う場合は、リポジトリ側の名前と構成に合わせ、この想定を上書きします。

| 層 | 想定 |
| --- | --- |
| API | NestJS。実行は AWS Lambda |
| 入口 | Amazon API Gateway。JWT オーソライザーが外層 |
| 認証 | Amazon Cognito のユーザープール。検証はアプリのガードが内層 |
| データ | Prisma と Aurora PostgreSQL |
| ファイル | Amazon S3 の署名付き URL |
| 画面 | React |
| テナント | テナント所有の行はテナントキーを持つ。子スコープ（拠点など）がある場合がある |
| 認可 | ロール権限と、行の所有テナントは別々に判定する |
| ローカル | ローカル専用のトークン発行がある場合がある。ローカル以外では無効 |

## モード

指定は英語の `full` か `diff` です。1 回の起動は 1 モードです。結果を混ぜません。

| モード | 見ること | 起動 |
| --- | --- | --- |
| `full` | アプリ本体の攻撃面を静的に棚卸しする | `/cmd-penetration-test full` |
| `diff` | 統合ブランチとの差分だけ | `/cmd-penetration-test diff` |

指定が無いときは `diff` です。`diff` は差分の外を見ません。基準は `origin/develop`、無ければ `origin/main` です。

同じ依頼で `full` と `diff` の両方があるときは、`diff` の報告を終えてから `full` を別報告にします。

## 入れ方

対象のアプリケーションリポジトリに、次を同じパスで置きます。

- `.cursor/commands/cmd-penetration-test.md`
- `.cursor/agents/agt-penetration-test.md`
- `.cursor/skills/penetration-test/SKILL.md`

手順の正本はスキルです。コマンドは起動条件、エージェントは実行役です。

## 調べてよい環境

利用者がその依頼の中でベース URL を書いた開発環境だけ、最小のリクエストを送れます。ローカルも、URL を書いたときだけです。URL が無いときは、リポジトリ内の静的な確認に留めます。

本番へのリクエスト、秘密情報のコミット、データの破壊、大量リクエストはしません。

## ライセンス

[MIT](LICENSE)

# /cmd-penetration-test

AWS 上のマルチテナント Web アプリケーションを攻撃者目線でレビューする。手順の正本は Skill、実行は `agt-penetration-test`。

## 必読

`.cursor/skills/penetration-test/SKILL.md` を最初に Read する。この Command は起動条件と完了条件だけを書く。

## モード

起動時にどちらかへ固定する。報告の §0 にモード名を書く。片方の結果を、もう片方へ広げない。

| モード | 用途 | 言い方の例 |
| --- | --- | --- |
| 全体チェック | アプリ本体の攻撃面を静的に見る。リリース前や定期の棚卸し | `/cmd-penetration-test 全体`、「全体チェック」 |
| 個別確認 | 差分、未コミット、指定パスだけを見る。PR 前やその機能の確認 | `/cmd-penetration-test 個別`、パスや「この変更」 |

どちらも無いときは個別確認にする。差分は、`origin/develop` があれば `git diff origin/develop...HEAD`、無ければ `git diff origin/main...HEAD`。その差分にアプリの攻撃面が無くても、全体チェックへ切り替えない。全体が必要ならユーザーに確認する。

同じ依頼で両方を指定されたときは、個別確認を完了してから全体チェックを始める。報告は分け、発見を移さない。

この手順は URL スキャンの代わりにしない。

## 禁止

- Skill を読まず、静的 diff の指摘だけで完了とする
- ユーザーがその依頼でベース URL を書いていない環境へのリクエスト
- `.env` やトークンをリポジトリに書く
- ユーザー依頼のない修正実装

## 手順

1. モードを固定する。全体チェックか個別確認か。個別確認なら、続けて次のどれかを範囲にする。
   - `branch changes`（上記の `git diff`）
   - `uncommitted changes`
   - 特定パス（feature / Controller / エンドポイント）
2. Task でサブエージェント `agt-penetration-test` を起動する。親が Skill を読んで自分でレビューを完了させない。プロンプトに含める。
   - `Full Repository Path`: 調査するアプリケーションの絶対パス
   - `Diff`: `全体チェック`、または個別確認の範囲種別とパス
   - `Change Description`: 変更ファイルと要点（diff が取れない場合）
   - `Custom Instructions`: ユーザーが指定した攻撃者、環境、重点カテゴリ。ベース URL の指定が無ければリクエストしない。渡されたモードを変えない
3. サブエージェントは `.cursor/agents/agt-penetration-test.md` と Skill に従い、偵察、脅威モデリング、検証、報告を行う。
4. 親は報告をユーザーに提示する。修正は別途確認を取る。

## 完了条件

- Skill の出力 §0 から §5 が揃っている
- High 以上には攻撃シナリオと、再現手順または静的証明がある
- 倫理・スコープ違反の操作をしていない

## 関連

- 手順: `.cursor/skills/penetration-test/SKILL.md`
- サブエージェント: `.cursor/agents/agt-penetration-test.md`
- 差分だけの軽量レビュー: 組み込み `security-review`

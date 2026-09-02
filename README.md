# Instagram 運用ドキュメント

自社 Instagram アカウントの運用に関する手順書・テンプレートを管理するリポジトリです。

## 運用アカウント

作業手順・テンプレート（下記「ドキュメント」）は全アカウント共通。アカウントごとの前提・台本・分析は以下に分かれています。

| アカウント | 前提（CLAUDE.md） | 台本・分析 |
| --- | --- | --- |
| レシピ（冷凍ストック・時短） | [ルート CLAUDE.md](CLAUDE.md) | [docs/scripts](docs/scripts) / [docs/analysis](docs/analysis) |
| amber（タイ古式マッサージサロン） | [accounts/amber/CLAUDE.md](accounts/amber/CLAUDE.md) | [accounts/amber/docs/scripts](accounts/amber/docs/scripts) / [accounts/amber/docs/analysis](accounts/amber/docs/analysis) |

新しいアカウントを追加する場合は `accounts/<アカウント名>/` に同じ構成（`CLAUDE.md` + `docs/scripts` + `docs/analysis`）を作成してください。

## ドキュメント

| ドキュメント | 内容 |
| --- | --- |
| [リール作成 手順書](docs/reels-creation-procedure.md) | 企画から投稿・振り返りまでの標準手順 |
| [リール企画シート](docs/templates/reel-planning-sheet.md) | 企画・構成・投稿文・実績を記入するテンプレート |
| [投稿前チェックリスト](docs/templates/pre-post-checklist.md) | 承認者が投稿前に確認する項目 |

## 投稿の分析と台本

| ファイル | 内容 |
| --- | --- |
| [分析：ゴマたま鶏しゃぶ（8/27投稿）](docs/analysis/2026-08-27_goma-tama-torishabu.md) | インサイトとコマ解析による診断 |
| [参考リールの分解](docs/analysis/2026-08-27_reference-teardown.md) | @maho_protein のリールから、伸びている要素と真似していい範囲を整理 |
| [台本：梅ごま鶏しゃぶ](docs/scripts/2026-09_umegoma-torishabu.md) | **制作中。** 撮影済み素材から18秒に組む台本 |
| [台本：凍ったまま削るねぎ塩鶏](docs/scripts/2026-09_negishio-tori.md) | 保留。分析から起こした別案 |

## 使い方

1. 新しいリールを作るときは、[リール作成 手順書](docs/reels-creation-procedure.md) のフローに沿って進める。
2. [リール企画シート](docs/templates/reel-planning-sheet.md) を複製して案件フォルダに置き、ステップごとに記入する。
3. 投稿前に承認者が [投稿前チェックリスト](docs/templates/pre-post-checklist.md) で確認し、承認を得てから公開する。

## 更新について

Instagram の仕様は頻繁に変わります。仕様の変更や運用ルールの見直しがあった場合は、該当ドキュメントを修正し、手順書末尾の「改訂履歴」に追記してください。

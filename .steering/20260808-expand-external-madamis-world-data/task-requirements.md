# 外部マダミスワールド情報拡充 要求内容

## 目的

- `tmp/vrchat_madamis_worlds_2026-08-08.csv` を基に外部マダミスワールド情報を整理
- `docs/scenarios/external/datas/` にJSONデータを追加
- CSVの情報量と既存JSONの利用形態を比較し、拡張後のJSON形式を決定

## 背景

- 既存JSONは `gameId`、`title`、`summary`、`players`、`worldId`、`tags` を持つ最小構成
- CSVは40件の候補を持ち、直接プレイ候補以外に関連候補、条件付きホスト、旧版／アーカイブ、削除済みなどを含む
- CSVにはGM要否、プレイ時間、言語、対応プラットフォーム、作者、公開状態、情報源、確認日、注意事項など、既存JSONにない情報が含まれる
- 未確認値、名称変更疑い、外部データ購入が必要なワールドなどを、ポータル上で誤解なく扱う必要

## 今回の対象

- AGENTS.mdに定める承認手順に従ったステアリング文書の作成
- 既存JSONの項目・命名・配列管理方法の確認
- CSV項目とJSON項目の対応付け
- 直接プレイ候補と周辺候補を区別できるJSON形式の検討
- プレイ人数、GM、所要時間、言語、プラットフォーム、公開状態、出典、確認日、注意事項の表現方法の検討
- 既存データとの互換性、重複防止、`names.json` の管理方法の検討
- 承認された設計に基づくJSON追加・既存JSON形式の必要な見直し

## 受け入れ条件

- CSV全40件について、JSON登録対象・保留対象・対象外の判断基準が設計文書に記載される
- 既存形式を維持する項目と、CSV由来情報を表現するために追加する項目が設計文書に記載される
- 直接プレイ候補以外のワールドが、`activeIds` から除外され、直接プレイ可能な作品と誤認されない掲載制御になる
- 未確認値、旧版、削除済み、外部データ依存などの扱いが明確になる
- JSON追加後、各ファイルが構文上正しいJSONとして読み込める
- `worldId` の重複、`gameId` の重複、`names.json` の参照不整合がない
- 既存の外部シナリオデータの利用方法を壊さない
- CSVの確認日と情報源を追跡可能な形で保持する

## 制約事項

- 主な情報源は `tmp/vrchat_madamis_worlds_2026-08-08.csv`
- CSVの未確認値を推測で補完しない
- `tmp/vrchat_madamis_worlds_2026-08-08.csv` は変更しない
- JSON追加・形式変更は設計承認後に開始する
- ステアリング文書は1ファイルごとに確認・承認を取得する
- 基本設計への影響がない限り、`docs/` の永続化文書は変更しない
- JSONの分類を変更する場合、既存の参照側への影響を確認する

## 想定成果物

- `.steering/20260808-expand-external-madamis-world-data/task-requirements.md`
- `.steering/20260808-expand-external-madamis-world-data/design.md`
- `.steering/20260808-expand-external-madamis-world-data/task-list.md`
- `docs/scenarios/external/datas/` の追加・更新JSON
- 必要に応じた `docs/scenarios/external/names.json` の更新

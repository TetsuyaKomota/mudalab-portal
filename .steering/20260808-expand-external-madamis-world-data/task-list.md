# 外部マダミスワールド情報拡充 タスクリスト

## 進捗

- [x] AGENTS.md、CSV、既存JSONの確認
- [x] ステアリングディレクトリ作成
- [x] `task-requirements.md` 作成・承認
- [x] `design.md` 作成・修正・承認
- [x] `task-list.md` 承認
- [x] CSVと既存JSONの対応付け
- [x] JSON形式の変換・追加
- [x] `names.json` 更新
- [x] JSON・参照整合性の検証
- [x] 変更内容の最終確認

## 1. CSVと既存JSONの対応付け

### 作業

- `Import-Csv` でCSV40件を読み込み、引用符内の改行を含む情報を保持
- `roomId` と既存JSONの `worldId` を照合
- 次の既存データを更新対象として確定
  - `0.json`: `wrld_ced67f49-ac16-453b-a013-51ca68872529`
  - `1.json`: `wrld_57139eef-01d1-4716-928f-ef2f0f264b2e`
- 新規データのファイルIDをCSV順に `2.json` ～ `39.json` へ割り当て
- 新規 `gameId` を重複なく付与

### 完了条件

- CSV40件とJSON40件の対応表が確定
- 既存2件の `gameId` が維持される
- `worldId` の重複がない

## 2. JSONデータ作成

### 作業

- 既存 `0.json`、`1.json` を承認済み形式へ更新
- `2.json` ～ `39.json` を追加
- 共通トップレベル項目を設定
  - `gameId`
  - `title`
  - `summary`
  - `players.min`
  - `players.max`
  - `players.recommended`
  - `worldId`
  - `tags`
- 外部ワールド固有項目を `external` に設定
  - `gm.required`
  - `playTime.approximate`
  - `language`
  - `platforms`
  - `author`
  - `maxConnections`
  - `checkedAt`
  - `notes`
- 未確認値・空欄・利用不可を推測せず `null` として設定
- `作品タグ` をセミコロンで分割し、空白を整理した配列として設定
- `対応プラットフォーム` を `/` 区切りで配列化。未確認は `null`
- `GM要否` は `GM必須` を `true`、`GM不要` を `false`、その他を `null`
- `プレイ時間` は概算表現の有無だけを `playTime.approximate` に設定
- CSVの `VRChat URL`、`情報源URL`、公開状態はJSONへ追加しない

### 完了条件

- CSV40件が1件1JSONへ反映
- 承認済みの削除項目がJSON内に存在しない
- 既存JSONの読み込みに必要なトップレベル項目が維持される
- URL、スクリプト、実行可能な内容をJSONへ埋め込まない

## 3. `names.json` 更新

### 作業

- `docs/scenarios/external/names.json` の `activeIds` を更新
- CSV判定が「直接プレイ候補」または「条件付きホスト」のIDだけを登録
- 掲載対象IDを次の14件に設定
  - `[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14]`
- 関連候補、旧版／アーカイブ、非マダミス寄り、削除済みのIDを登録しない

### 完了条件

- `activeIds` の全IDに対応するJSONファイルが存在
- `activeIds` に非掲載対象が混入していない
- 既存の0、1が維持される

## 4. 構文・内容検証

### 作業

- `docs/scenarios/external/datas/*.json` 全ファイルをJSONパーサーで読み込み
- JSONファイル数を40件として確認
- `gameId` の重複を確認
- `worldId` の非NULL値の重複を確認
- CSV40件とJSON40件を `worldId` で照合
- 既存2件の `gameId`、`worldId`、プレイヤー人数を確認
- `activeIds` の参照先と掲載対象を確認
- 承認済みの不要項目がないことを確認
- `tmp/vrchat_madamis_worlds_2026-08-08.csv` に差分がないことを確認
- `docs/scenarios/internal/` と `docs/sample_json/` に差分がないことを確認

### 完了条件

- JSON構文エラーなし
- 重複・参照切れ・CSV未反映なし
- 不要なファイル変更なし

## 5. 最終確認

### 作業

- `git status` と差分を確認
- ステアリング文書の進捗を更新
- 追加JSON、`names.json`、既存JSON更新の一覧を報告

### 完了条件

- 要求・設計・実装の内容が一致
- 検証結果を最終報告へ反映

## 検証結果

- JSONファイル40件の構文解析成功
- CSV40件とJSON40件の `worldId` 照合成功
- `gameId` 重複なし
- `worldId` 重複なし
- `activeIds` 参照切れなし
- 承認済み不要項目の混入なし
- CSV、内部シナリオ、サンプルJSONの変更なし

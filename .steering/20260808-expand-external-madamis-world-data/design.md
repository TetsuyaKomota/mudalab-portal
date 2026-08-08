# 外部マダミスワールド情報拡充 設計

## 1. 設計方針

- 既存JSONのトップレベル項目を維持し、既存参照側との互換性を確保
- CSV固有の外部ワールド情報を `external` オブジェクトへ集約
- CSVの40件を1件1JSONとして記録し、調査結果を欠落させない
- `names.json` の `activeIds` をポータル掲載対象の制御に使用
- 直接プレイできない候補をJSONから除外せず、`activeIds` から除外して掲載対象を制御
- 不明値は推測せず `null` とし、CSVを正本としてJSONには必要な情報だけを保持

## 2. 登録方針

### 2.1 CSV行とJSONの対応

CSVの各行を1つのJSONデータへ変換する。既存JSONと同一 `worldId` の行は新規作成せず、既存ファイルを拡張する。

| CSV判定 | JSON登録 | `names.json.activeIds` | 扱い |
| --- | --- | --- | --- |
| 直接プレイ候補 | 対象 | 対象 | ポータル掲載対象 |
| 条件付きホスト | 対象 | 対象 | 条件付き掲載。外部データ・主催者などの条件を表示 |
| 関連候補 | 対象 | 対象外 | 将来候補・関連ジャンルとして保存。直接プレイ作品と混同させない |
| 旧版/アーカイブ | 対象 | 対象外 | 履歴・重複排除用。現行版への誘導情報を注記 |
| 非マダミス寄り | 対象 | 対象外 | 隣接ジャンルの調査記録 |
| 削除済み | 対象 | 対象外 | 履歴・重複排除用。ポータル掲載なし |

CSVの判定別件数は、直接プレイ候補13件、関連候補18件、条件付きホスト1件、旧版／アーカイブ3件、非マダミス寄り3件、削除済み2件。

### 2.2 ファイルIDと `gameId`

- 既存の `0.json` と `1.json` は `worldId` をキーに既存データと対応付けて更新
- 新規データは `2.json` から連番で追加
- `gameId` は既存値を変更しない
- `worldId` が存在する新規データは `external-world-` に短縮したワールドIDを付与
- ファイル名と `gameId` を別管理し、将来の並び替えで論理IDが変わらない構成

### 2.3 `activeIds`

`activeIds` には、CSV判定が「直接プレイ候補」または「条件付きホスト」のJSONファイルIDだけを登録する。既存の `0` と `1` は維持し、新規掲載対象を追加する。

関連候補・旧版・削除済みなどのJSONは調査記録として残すが、`activeIds` から除外する。これにより、データの完全性とポータルの掲載対象を分離する。

## 3. JSON形式

### 3.1 共通形式

```json
{
  "gameId": "external-sample-001",
  "title": "【2人用】 マーダーミステリー『Super ＆ Marketǃ』【Android対応】",
  "summary": "閉店間際のスーパーマーケット付近で死体が発見された――という導入の、2人用マーダーミステリー。",
  "players": {
    "min": 2,
    "max": 2,
    "recommended": 2
  },
  "worldId": "wrld_7b417d22-e016-45dc-b80d-c604200eb80c",
  "tags": ["2人用", "現代", "スーパーマーケット", "短編", "GMレス"],
  "external": {
    "gm": {
      "required": false
    },
    "playTime": {
      "approximate": true
    },
    "language": "日本語",
    "platforms": ["PC", "Android"],
    "author": "aaway",
    "maxConnections": 2,
    "checkedAt": "2026-08-08",
    "notes": "ワールド音量60%以上推奨。途中参加不可。"
  }
}
```

### 3.2 既存項目の扱い

| JSON項目 | 方針 |
| --- | --- |
| `gameId` | 必須。既存値を維持。新規値は一意に付与 |
| `title` | CSVの「ワールド名」を使用 |
| `summary` | CSVの `description` を使用。未入力時は空文字列ではなく `null` |
| `players.min` / `players.max` | CSVの最小人数・最大人数を数値化。未確認・利用不可は `null` |
| `players.recommended` | 固定人数が確認できる場合のみ設定。それ以外は `null` |
| `worldId` | CSVの `roomId` を使用。空欄は `null` |
| `tags` | CSVの「作品タグ」をセミコロンで分割した配列 |

`players` の既存3項目と `worldId`、`tags` は既存参照側が利用しているため、項目名と型を変更しない。

### 3.3 `external` 項目

| JSON項目 | CSV列 | 変換方針 |
| --- | --- | --- |
| `gm.required` | GM要否 | `GM必須` は `true`、`GM不要` は `false`。その他は `null` |
| `playTime.approximate` | プレイ時間 | 「約」や範囲など概算表現を含む場合 `true`。未確認は `null` |
| `language` | 言語 | 原文保持。未確認は `null` |
| `platforms` | 対応プラットフォーム | `/` 区切りで配列化。未確認は `null` |
| `author` | 作者 | 原文保持。未確認は `null` |
| `maxConnections` | 最大接続数 | 数値化。未確認は `null` |
| `checkedAt` | 確認日 | ISO形式の日付文字列 |
| `notes` | 注意事項 | 原文保持。未入力は `null` |

`external` は外部ワールド固有の任意オブジェクトとする。内部作品JSONに同じ形式を要求しない。

CSVの判定値はJSONへ保存せず、登録対象と `names.json.activeIds` の決定にだけ使用する。旧版・アーカイブ・削除済みなどのレコードは `activeIds` から除外する。

## 4. 既存データとの対応

既存データとの重複判定は `worldId` で実施する。

| 既存ファイル | CSV該当作品 | 対応方針 |
| --- | --- | --- |
| `0.json` | VRマダミス ぼくらのペリン事件 | `gameId` を維持し、CSV情報と `external` を追加 |
| `1.json` | SilverBlood MurderMystery | `gameId` を維持し、CSV情報と `external` を追加 |

既存の `summary` や `tags` にCSVにない情報がある場合は、既存利用者に必要な情報を削除せず、CSV由来情報と矛盾しない範囲で保持する。矛盾する場合はCSVの確認日・注意事項を優先し、必要な差異を `notes` に記録する。

## 5. 影響範囲

- `docs/scenarios/external/datas/0.json`、`1.json`: 外部メタデータ追加と必要な内容整理
- `docs/scenarios/external/datas/2.json` 以降: CSV各行の新規データ
- `docs/scenarios/external/names.json`: 掲載対象IDの追加
- `docs/scenarios/internal/`、`docs/sample_json/`: 変更なし
- `tmp/vrchat_madamis_worlds_2026-08-08.csv`: 変更なし
- `docs/` の永続化文書: 基本設計変更がないため変更なし

既存参照側はトップレベルの既存項目だけでも読み込める。`external` の利用側は、未実装環境でも追加項目を無視できる設計とする。

外部URLはJSONデータとして保存し、HTMLへ埋め込む場合は表示側でエスケープする。JSON内にスクリプトや実行可能な内容は保存しない。

## 6. 検証方針

- 全JSONをJSONパーサーで読み込み、構文エラーがないことを確認
- CSV40件とJSON40件を `worldId` で照合
- `worldId`、`gameId` の重複を確認
- `names.json.activeIds` の各値に対応するJSONファイルが存在することを確認
- `activeIds` に直接プレイ候補・条件付きホスト以外が含まれないことを確認
- 既存JSONの必須トップレベル項目が維持されていることを確認
- CSVの確認日と注意事項の欠落を確認

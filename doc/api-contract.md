# 最適化API契約

本リポジトリが`contracts/openapi/optimizer.yaml`を所有する。Spring Bootは版付きbundleからJava HTTPクライアントを生成する。FastAPIのPydantic入出力型とOpenAPI出力は、正の契約に対する試験で整合させる。

## エンドポイント

`POST http://optimizer:8000/v1/route-optimizations`

時間制限付き同期RESTとして候補を返す。基幹が画面に公開する非同期ジョブAPIとは別の契約であり、optimizerにジョブ照会・採用APIは設けない。

| 項目 | 内容 |
|---|---|
| 認証 | 環境別の`X-Optimizer-Key` |
| 識別 | requestId、snapshotId、scopeRevisions、inputHash |
| 訪問・車両 | 時間窓、作業時間、積載、稼働、成立済み担当条件 |
| 移動 | 地点順を固定した時間・距離行列、到達不能、matrixVersion |
| 制約 | constraintVersion、solverConfigVersion、timeLimitSeconds |
| 結果 | outcome、車両別routes、unassignedVisits、証跡 |

`outcome`はFEASIBLE / PARTIAL / INFEASIBLE / NO_SOLUTION_WITHIN_LIMIT。成功したHTTP応答は業務上の採用を意味しない。数学的に最適と証明できない実行可能解を「最適性保証」と表示しない。

| HTTP | 意味 |
|---|---|
| 200 | 計算結果。実行不能・時間内解未発見も含む |
| 401 / 403 | サービス認証・権限不正 |
| 422 | 入力・制約・行列の不整合 |
| 429 | 同時実行枠なし。Retry-Afterを返す |
| 503 | 一時的なサービス障害 |

同じrequestIdの要求は再計算され得る。同一入力に同一候補を返すことや、タスクをまたぐ重複排除を保証しない。結果はrequestIdとinputHashで照合し、基幹が有効な試行だけを保存する。

## 連携条件

solver上限20秒、要求deadline25秒、Service Connect timeout30秒、基幹timeout35秒とする。再試行・lease・入力版照合・採用条件の正は[基幹の連携定義](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/architecture/backend/optimizer-contract.md)。定義変更時は両リポジトリの契約試験を通す。

氏名・電話番号・決済情報を受け取らず、業務DBや基幹の業務APIから追加入力を取得しない。

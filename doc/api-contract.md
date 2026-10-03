# 最適化API契約の案

更新日：2026-10-03。仕様本体は未作成。スナップショット/結果採用の基幹側手順は[連携方針](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/main/doc/architecture/backend/optimizer-contract.md)を正とする。

## 所有と呼出し

本リポジトリが`contracts/openapi/optimizer.yaml`を所有する。Spring Bootがその版付き契約からHTTPクライアントを生成する。FastAPIの自動生成OpenAPIは実装検証に使い、正の契約と差分を確認する。

Spring Bootが整合した入力を送る方式を初期案とする。Pythonが予約・車両を複数APIで順次取得して入力を組み立てる構成は初期必須にしない。将来必要なら基幹internal APIを契約として追加する。

## 入出力

入力にはrequestId/snapshotId/planningRevision、訪問ID/座標/時間窓/作業時間/積載量、車両容量/稼働時間/開始終了地点、制約/移動時間データの版を持たせる。結果は入力識別、候補順序、距離/所要時間、未割当理由、solver情報を返す。

氏名/電話/決済情報は渡さない。予約変更によるstale判定と候補採用はSpring Bootが行う。成功レスポンスだけで採用済みにならない。

同期処理は上限時間と失敗応答を明示する。非同期化時の案はPOST `/v1/optimization-jobs`→202/jobId、GET `/v1/optimization-jobs/{id}`→状態/結果。非同期実装では状態永続化を必須とし、同じrequestIdの同じ入力は同じジョブへ、異なる入力は競合へ扱う。

## 試験

契約のbundle/生成/検証、認証失敗、入力異常、容量/時間窓制約、実行不能、時間切れ、重複要求、再起動、同一入力の追跡を試験する。基幹側では予約変更中の計算結果が採用されないことを結合試験する。

# 最適化サービスの構成案

更新日：2026-10-03。Python/FastAPIと計算候補の提供は合意済み。具体ライブラリ/配置は提案。

## 配置と依存

| パス | 責務 |
|---|---|
| `src/recycle_gang_optimizer/api/` | FastAPI、入出力検証、サービス認証 |
| `src/recycle_gang_optimizer/application/` | 計算ユースケース、タイムアウト、ジョブ操作 |
| `src/recycle_gang_optimizer/domain/` | 訪問/車両/制約/結果。HTTPに依存しない |
| `src/recycle_gang_optimizer/adapters/` | ソルバー、必要時のジョブ保存/移動時間取得 |
| `contracts/openapi/optimizer.yaml` | 提供側が所有する計算APIの正 |
| `tests/` | 単体、solver制約、API契約、ジョブ復旧 |
| `ci/` | 検証、コンテナbuild、成果物保存 |
| `pyproject.toml`、`uv.lock` | パッケージ/依存の固定。実装時に追加 |

Python 3.12系を互換性検証の起点に、FastAPI/Pydantic、uv/pytest、OR-Toolsを候補とする。版は対象ランタイム/ソルバーの組合せを試験後固定する。実際の走行時間や地図情報の提供元は未決。

## 実行

Spring Bootとは別のECSワークロード。小規模で上限時間がHTTP内に収まれば同期で始められる。長時間化時はAPI受付と計算ワーカーを分離し、永続ジョブ・再試行・キャンセル・再起動復旧を実装する。async宣言だけでCPU処理や障害復旧が解決するとは扱わない。

solver制限時間、メモリ、同時ジョブ数を設定し、基幹処理への影響を分離する。最適解が得られない場合は実行可能解・未割当・実行不能・時間切れを区別する。正しさは小さな既知ケースと制約検査で確認する。

## 運用

`optimizer-vX.Y.Z`で独立リリース。Git SHA、image digest、契約版、solver版/設定を記録する。スナップショットの保持期間と個人情報の最小化を定める。業務予約/料金/割当を独自更新しない。

共通のGitFlow・05:00 JST日次CI・手動CDを適用する。変更した契約を利用するSpringクライアントとの試験を必須にする。

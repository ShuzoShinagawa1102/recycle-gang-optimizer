# 最適化サービスの構成

FastAPIは入力検証・サービス認証・計算受付を担う。計算モデルをHTTPやソルバー固有型から分離し、ソルバーを外側のアダプターとして接続する。

## フォルダ構成

```text
recycle-gang-optimizer/
├── src/recycle_gang_optimizer/
│   ├── api/                  # FastAPI、Pydantic DTO、サービス認証
│   ├── application/          # 計算実行、deadline、並列数制御
│   ├── domain/               # 訪問・車両・制約・計算結果
│   └── adapters/solver/      # ソルバーへの変換・実行
├── contracts/openapi/optimizer.yaml
├── tests/
│   ├── unit/
│   ├── solver/
│   └── contract/
├── ci/
├── doc/
├── pyproject.toml
└── uv.lock
```

Python / FastAPI / Pydantic / uv / pytestを利用する。ソルバーはOR-Toolsを起点に制約と時間上限を検証して選定する。依存・生成器・コンテナの版を固定する。

## 実行

基幹とは別のECS Fargateサービスとし、同じ環境のService Connect namespaceで`optimizer:8000`を公開する。公開ALBへ登録せず、基幹SGからの8000のみ受け付ける。専用サービスキーをSecrets Managerから取得して検証する。

CPU計算は独立プロセスで行い、APIイベントループを塞がない。タスクあたり同時計算1件、solver上限20秒、要求deadline25秒とする。受付枠がなければ429とRetry-Afterを返し、タスク内に無制限の待ち行列を作らない。

## 状態と障害

ジョブ状態、入力、計算結果、採用履歴は基幹に保存する。optimizerにDBや永続キューを持たせない。ECS再起動による接続失敗は基幹ジョブの再試行で回復する。重複計算を許容し、採用の冪等性を基幹で保証する。

入力の氏名・電話・決済情報は受け取らない。ログにはrequestId、入力hash、solver版、時間、結果概要を記録し、サービスキーや座標付き入力全体を出力しない。

## リリースと試験

`optimizer-vX.Y.Z`で独立リリースし、image digest、Git SHA、契約版、solver版・設定を記録する。native ECS rollingを利用し、旧新タスクの契約互換性を守る。

小さな既知ケース、制約検査、時間上限、過負荷、認証、契約、切断・再送を試験する。実行可能解と最適性の証明、実行不能と時間内に解が見つからない結果を区別する。

[連携の正](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/architecture/backend/optimizer-contract.md)、[共通試験方針](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/process/testing-policy.md)

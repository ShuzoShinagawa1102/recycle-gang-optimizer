# Recycle Gang Optimizer

Pythonによる経路最適化とFastAPIの計算窓口を管理するリポジトリです。Spring Bootから版付き入力を受け取り、経路候補を返します。

更新日：2026-10-03。現在は設計文書のみ。計算コード、OpenAPI本体、ジョブ基盤、CI/CD、AWSは未実装です。

## 責務

指定された訪問先・車両・容量・時間制約から候補を計算します。予約・業者割当・採用済み計画の正は[recycle-gang](https://github.com/ShuzoShinagawa1102/recycle-gang)にあり、最終採用はSpring Bootが行います。基幹の業務DBへ接続しません。

## ドキュメント

- [構成・技術案](doc/architecture.md)
- [計算API契約案](doc/api-contract.md)
- [全体開発方針](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/main/doc/architecture/development-baseline.md)
- [基幹とのスナップショット連携](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/main/doc/architecture/backend/optimizer-contract.md)
- [共通CI/CD](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/main/doc/process/ci-cd-policy.md)

全体方針は基幹リポジトリを正とします。ソルバーや計算時間・制約の詳細はレビューで具体化します。

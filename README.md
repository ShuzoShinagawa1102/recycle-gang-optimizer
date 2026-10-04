# Recycle Gang Optimizer

Pythonによる経路計算とFastAPIの内部REST APIを管理する。基幹が渡す入力スナップショットから候補を計算し、結果を返す。開発の基準ブランチは`develop/v1`。

ジョブ、業務データ、候補採用はSpring Bootが所有する。optimizerはステートレスに動作し、業務DBへ接続しない。

- [構成とフォルダ](doc/architecture.md)
- [計算API契約](doc/api-contract.md)
- [全体アーキテクチャ](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/architecture/development-baseline.md)
- [基幹との連携](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/architecture/backend/optimizer-contract.md)
- [共通CI/CD](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/process/ci-cd-policy.md)
- [実装状況](https://github.com/ShuzoShinagawa1102/recycle-gang/blob/develop/v1/doc/process/implementation-status.md)

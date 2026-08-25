# IT サポート エージェント２ (Standard harness)

## 概要

ここでは、Microsoft Copilot Studio を使用して、架空の企業における社内 IT に関する問い合わせに対応するエージェントを作成します。

エージェントの作成を通じて、ナレッジを活用した回答の生成、会話フローの制御、およびユーザーのリクエストに応じたデータの作成・取得を実現する方法を学習します。

> [!IMPORTANT]
>
> 本手順は IT サポート エージェントの設計や実装に関するベストプラクティスを示すものではありません。
>
> 社内 IT サポートを題材として、Microsoft Copilot Studio を利用したエージェント作成を体験し、主要な機能や操作方法を学習することを目的としています。
> ここでは、もう一つの IT サポートエージェントとは別の方法で実装しています。

## ハンズオン

### 準備

1. [前提条件](docs/01-prerequisites.md)
2. [環境を準備する](docs/02-environment-setup.md)

### エージェント作成

1. [Support Ticket テーブルを作成する](docs/10-create-support-ticket-table.md)
2. [IT Support Agent を作成する](docs/11-create-it-support-agent.md)
3. [IT Support Agent にサポートチケット機能を実装する](docs/12-build-support-ticketing.md)
4. [IT Support Agent のインストラクションを設定する](docs/13-configure-agent-instructions.md)
5. [Device Assistant を作成してマルチエージェントを構成する](docs/14-build-multi-agent.md)
6. [Device Assistant にデバイス交換申請機能を実装する](docs/15-build-device-replacement-feature.md)
7. [エージェントを公開する](docs/20-publish-agent.md)

### 後作業

1. [作成したエージェントや環境を削除する](docs/90-cleanup.md)

## 注意事項

本資料は、Microsoft Copilot Studio の機能や操作方法について、実際に環境を操作しながら理解を深めることを目的として作成されたハンズオン資料です。

記載されている内容は本資料作成時点の情報に基づいており、機能変更等により、画面や操作手順が変更される場合があります。

また、本資料にはプレビュー機能を利用する手順が含まれています。プレビュー機能は変更または提供終了となる場合がありますので、利用にあたっては最新の製品ドキュメントを確認してください。

本資料に記載されている手順や構成は学習目的のサンプルであり、特定の環境や要件に対する設計・実装を保証するものではありません。

---
title: Azure Cosmos DB の Mongo DB 用 API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の MongoDB 用 API で発生する一般的な問題のトラブルシューティング方法について説明します。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: ece975fa37e500b1c160210684a0cb46e719c48b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754963"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング

Azure Cosmos DB は、MongoDB などの一般的な NoSQL データベースのワイヤ プロトコルを実装しています。 ワイヤ プロトコルの実装により、NoSQL データベースと連携する既存のクライアント SDK、ドライバー、およびツールを使用して Azure Cosmos DB と透過的に対話できます。 Azure Cosmos DB では、任意の NoSQL データベース向けにワイヤ互換性のある API を提供するためにデータベースのソース コードを使用することは一切ありません。 ワイヤ プロトコル バージョンを認識する MongoDB クライアント ドライバーはすべて Azure Cosmos DB に接続できます。

Azure Cosmos DB の MongoDB 用 API は、MongoDB のワイヤ プロトコルの 3.2 バージョンと互換性があります (バージョン 3.4 で追加されたクエリ演算子および機能は現在プレビューとして使用できます) が、Azure Cosmos DB 固有のエラーに対応するいくつかのカスタム エラー コードがあります。 この記事では、さまざまなエラー、エラー コード、およびそれらのエラーを解決する手順について説明します。

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

| Error               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済みの要求ユニット レートを超えたために調整されました。 | Azure portal からコンテナーまたはコンテナーのセットに割り当てられているスループットをスケーリングすることを検討してください。または、操作を再試行できます。 |
| ExceededMemoryLimit | 16501 | マルチテナント サービスとしての操作が、クライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 例: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 指定された order by 項目に対応するインデックスのパスが除外されます。order by クエリには提供元にすることができる対応する複合インデックスがありません。 | 2 | クエリは、インデックスが設定されていないフィールドに対して並べ替えを要求します。 | 実行しようとしている並べ替えクエリの一致するインデックス (または複合インデックス) を作成します。 |
| MongoDB ワイヤ バージョンの問題 | - | 古いバージョンの MongoDB ドライバーは、Azure Cosmos アカウントの名前を接続文字列から検出できません。 | Cosmos DB の MongoDB 用 API の接続文字列用の末尾に *appName=@**accountName**@* を追加します。***accountName*** は Cosmos DB アカウント名です。 |


## <a name="next-steps"></a>次の手順

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。


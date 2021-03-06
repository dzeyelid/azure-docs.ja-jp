---
title: ネットワークに関する Azure Security Center の推奨事項
description: この記事では、ネットワーク リソースを保護するのに役立つ Azure Security Center のセキュリティに関する推奨事項の一覧を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781843"
---
# <a name="network-recommendations---reference-guide"></a>ネットワークに関する推奨事項 - リファレンス ガイド

この記事では、ネットワークのトポロジとインターネットに接続するエンドポイントに関する、Azure Security Center に表示される推奨事項の完全な一覧を示します。

これらを見つけて解決する方法の詳細については、[こちら](security-center-network-recommendations.md)を参照してください。

## <a name="network-recommendations"></a>ネットワークの推奨事項

|推奨事項の名前|説明|重大度|セキュリティ スコア|リソースの種類|
|----|----|----|----|----|----|
|サブネット レベルでのネットワーク セキュリティ グループを有効にする必要がある|サブネットにデプロイされたリソースのネットワーク アクセスを制御するネットワーク セキュリティ グループを有効にします。|高/中|30|Subnet|
|仮想マシンはネットワーク セキュリティ グループに関連付ける必要がある|仮想マシンのネットワーク アクセスを制御するネットワーク セキュリティ グループを有効にします。|高/中|30|仮想マシン|
|インターネットに接続された VM を含む、制限のないネットワーク セキュリティ グループについてはアクセスを制限する必要がある|既存の許可ルールのアクセスを制限することで、インターネットに接続する VM のネットワーク セキュリティ グループを強化します。|高|20|仮想マシン|
|IaaS NSG 上の Web アプリケーションに対する規則を強化する必要がある|Web アプリケーションを実行していて、Web アプリケーション ポートに関する NSG 規則の制限が緩すぎる仮想マシンのネットワーク セキュリティ グループ (NSG) を強化します。|高|20|仮想マシン|
|App Services へのアクセスを制限する必要がある|ネットワーク構成を変更することによって App Services へのアクセスを制限し、広すぎる範囲からの受信トラフィックを拒否します。|高|10|App Service|
|仮想マシンの管理ポートを閉じておく必要がある|管理ポートへのアクセスを制限するために、仮想マシンのネットワーク セキュリティ グループを強化します。|高|10|仮想マシン|
DDoS Protection Standard を有効にする必要がある|DDoS Protection Standard を有効にすることで、パブリック IP を使用するアプリケーションが含まれる仮想ネットワークを保護します。 DDoS 保護は、ネットワークに対する帯域幅消費型攻撃およびプロトコル攻撃の軽減を有効にします。|高|10|仮想ネットワーク|
|仮想マシンでの IP 転送を無効にする必要がある|IP 転送を無効にします。 仮想マシンの NIC で IP 転送が有効になっていると、そのマシンはその他の宛先へのトラフィックを受信できます。 IP 転送が必要な状況は (VM をネットワーク仮想アプライアンスとして使用する場合などに) 限られているため、ネットワーク セキュリティ チームはこのことを確認する必要があります。|Medium|10|仮想マシン|
|Web アプリケーションには HTTPS を介してのみアクセスできるようにする|Web アプリケーションに対して "HTTPS のみ" のアクセスを有効にします。 HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。|Medium|20|Web アプリケーション|
|仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある|Just-In-Time (JIT) 仮想マシン (VM) アクセス制御を適用して、選択したポートへのアクセスを完全にロックダウンし、承認されたユーザーが JIT を使用して限られた時間だけそれらのポートを開けるようにします。|高|20|仮想マシン|
|Function App には HTTPS 経由でのみアクセスできるようにする|関数アプリに対して "HTTPS のみ" のアクセスを有効にします。 HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。|Medium|20|関数アプリ|
|ストレージ アカウントへの安全な転送を有効にする必要がある|ストレージ アカウントへの安全な転送を有効にします。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します。|高|20|ストレージ アカウント|


## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [ID とアクセスを監視する](security-center-identity-access.md)
* [マシンとアプリケーションを保護する](security-center-virtual-machine-protection.md)
* [Azure SQL サービスを保護する](security-center-sql-service-recommendations.md)


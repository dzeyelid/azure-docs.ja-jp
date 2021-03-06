---
title: ページ上のリンクがアプリケーション プロキシ アプリケーションに対して機能しない
description: Azure AD と統合したアプリケーション プロキシ アプリケーション上の壊れたリンクに関する問題をトラブルシューティングする方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 268775d125e783b0c640e565c5f63e416cb197fd
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996954"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>ページ上のリンクがアプリケーション プロキシ アプリケーションに対して機能しない

この記事では、Azure Active Directory アプリケーション プロキシ アプリケーション上のリンクが正しく機能しない理由をトラブルシューティングする方法について説明します。

## <a name="overview"></a>概要 
アプリケーション プロキシ アプリを発行した後、そのアプリケーション内では、既定では、発行されたルート URL 内に含まれている宛先へのリンクしか機能しません。 アプリケーション内のリンクは機能せず、アプリケーションの内部 URL には、アプリケーション内のリンクのすべての宛先が含まれていない可能性があります。

**この問題が発生する理由** アプリケーション内のリンクをクリックすると、アプリケーション プロキシはその URL を同じアプリケーション内の内部 URL か、または外部から使用可能な URL のどちらかとして解決しようとします。 リンクが同じアプリケーション内にない内部 URL を指している場合、そのリンクはこれらのバケットのどちらにも属せず、見つからないというエラーが発生します。

## <a name="ways-you-can-resolve-broken-links"></a>壊れたリンクを解決する方法

この問題を解決するには、次の 3 つの方法があります。 以下の選択肢は、複雑さが低い順に示されています。

1.  内部 URL が、アプリケーションの関連するすべてのリンクを含むルートであることを確認します。 これで、すべてのリンクを同じアプリケーション内で発行されたコンテンツとして解決できます。

    内部 URL は変更するが、ユーザーのランディング ページを変更したくない場合は、ホーム ページ URL を前に発行した内部 URL に変更します。 これを行うには、[Azure Active Directory] -&gt; [アプリの登録] に移動し、アプリケーションを選択し、[ブランド] を選択します。 [ブランド] セクションには、目的のランディング ページになるように調整できるフィールド [ホーム ページ URL] が表示されます。 従来のアプリの登録エクスペリエンスを引き続き使用している場合は、[プロパティ] タブに "ホームページ URL" の詳細が表示されます。 
    
    > [!IMPORTANT]
    > 上記の変更を行うには、Azure AD でアプリケーション オブジェクトを変更する権限が必要です。ユーザーには "[アプリケーション管理者](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/roles-delegate-app-roles#assign-built-in-application-admin-roles)" ロールが割り当てられている必要があります。このロールにより、ユーザーに Azure AD のアプリケーション修正権限が付与されます。 
    >

2.  アプリケーションで完全修飾ドメイン名 (FQDN) を使用している場合は、[カスタム ドメイン](application-proxy-configure-custom-domain.md)を使用してアプリケーションを発行します。 この機能を使用すると、同じ URL を内部と外部の両方に使用できます。

    このオプションでは、内部 URL へのアプリケーション内のリンクが外部からも認識されるため、アプリケーション内のリンクがアプリケーション プロキシ経由で外部からアクセス可能なことが保証されます。 すべてのリンクが引き続き、発行済みのアプリケーションに属している必要があります。 ただし、このオプションでは、各リンクが同じアプリケーションに属している必要はなく、複数のアプリケーションに属することができます。

3.  これらのオプションのいずれも実行できない場合は、インライン リンク変換を有効にするためのオプションが複数あります。 このようなオプションには、Intune Managed Browser の使用、My Apps 拡張機能、またはアプリケーションでのリンク変換設定の使用などがあります。 これらの各オプションの詳細と有効化の方法については、「[Azure Active Directory アプリケーション プロキシで発行されたアプリ用にハードコードされたリンクのリダイレクト](application-proxy-configure-hard-coded-link-translation.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)


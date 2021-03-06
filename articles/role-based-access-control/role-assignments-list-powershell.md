---
title: Azure RBAC と Azure PowerShell を使用してロールの割り当てを一覧表示する
description: Azure ロールベースのアクセス制御 (RBAC) と Azure PowerShell を使用して、ユーザー、グループ、サービス プリンシパル、およびマネージド ID のアクセスを決定する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c30f1246dccbe14445b0f7db8584e37c0f4be6ab
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709887"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Azure RBAC と Azure PowerShell を使用してロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] この記事では、Azure PowerShell を使用してロールの割り当てを一覧表示する方法を説明します。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- [Azure Cloud Shell の PowerShell](/azure/cloud-shell/overview) または [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>現在のサブスクリプションにおけるロールの割り当ての一覧表示

現在のサブスクリプション内の (ルートと管理グループから継承されたロールの割り当てを含む) すべてのロールの割り当ての一覧を取得するもっと簡単な方法は、パラメーターの指定なしで [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用することです。

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>サブスクリプションのロールの割り当ての一覧表示

サブスクリプション スコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで、または [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) を使用して、その ID を見つけることができます。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

指定したユーザーに割り当てられているすべてのロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

特定のユーザーに割り当てられたすべてのロールと、そのユーザーが所属するグループに割り当てられたロールを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

リソース グループのスコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>管理グループに対するロールの割り当ての一覧表示

管理グループのスコープですべてのロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup) を使用できます。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>従来のサービス管理者と共同管理者のロールの割り当てを一覧表示する

従来のサブスクリプション管理者と共同管理者のロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="next-steps"></a>次の手順

- [Azure RBAC と Azure PowerShell を使用してロールの割り当てを追加または削除する](role-assignments-powershell.md)

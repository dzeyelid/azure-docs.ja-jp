---
title: チュートリアル:組織の外部と共有する - Azure Data Share
description: チュートリアル - Azure Data Share を使用して顧客やパートナーとデータを共有する
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499325"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>チュートリアル:Azure Data Share を使用したデータの共有  

このチュートリアルでは、新しい Azure Data Share を設定して、Azure 組織の外部の顧客やパートナーとのデータの共有を開始する方法を学習します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * データ共有を作成する。
> * データ共有にデータセットを追加する。
> * データ共有の同期スケジュールを有効にする。 
> * データ共有に受信者を追加する。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション: Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* 受信者の Azure ログイン用メール アドレス (メール エイリアスは無効です)。

### <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有:

* Azure Storage アカウント: まだお持ちでない場合は、[Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)を作成できます。
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* 権限に含まれています。 この権限は、所有者ロール内に存在します。 

### <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有:

* 共有するテーブルとビューを含む Azure SQL Database または Azure SQL Data Warehouse。
* データ共有からデータ ウェアハウスにアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. 自分自身をサーバーの Azure Active Directory 管理者として設定します。
    1. Azure Active Directory を使用して Azure SQL Database/Data Warehouse に接続します。
    1. クエリ エディター (プレビュー) を使用して次のスクリプトを実行し、Data Share MSI を db_owner として追加します。 SQL Server 認証ではなく Active Directory を使用して接続する必要があります。 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
*<share_acc_name>* は、Data Share アカウントの名前であることに注意してください。 Data Share アカウントをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。  

* クライアント IP SQL Server のファイアウォール アクセス:この操作を行うには、以下の手順を実行します。1. *ファイアウォールと仮想ネットワーク*に移動します 1. Azure サービスへのアクセスを許可するには、**オン**トグルをクリックします。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

Azure リソース グループに Azure Data Share リソースを作成します。

1. ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。

1. *Data Share* を検索します。

1. Data Share を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本的な詳細に、次の情報を入力します。 

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | Name | *datashareacount* | Data Share アカウントの名前を指定します。 |
    | Subscription | 該当するサブスクリプション | Data Share アカウントに使う Azure サブスクリプションを選択します。|
    | Resource group | *test-resource-group* | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | Location | *米国東部 2* | Data Share アカウントのリージョンを選択します。
    | | |

1. **[作成]** を選択して、Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分以下です。 

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

## <a name="create-a-data-share"></a>データ共有を作成する

1. Data Share の [概要] ページに移動します。

    ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **作成** を選択します。   

1. ご自分のデータ共有の詳細を入力します。 名前、共有の内容の説明、使用条件 (省略可能) を指定します。 

    ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** を選択します

1. データ共有にデータセットを追加するには、 **[Add Datasets]\(データセットの追加\)** を選択します。 

    ![データセット](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 

    ![AddDatasets](./media/add-datasets.png "データセットを追加する")    

1. 共有するオブジェクトに移動し、[Add Datasets]\(データセットの追加\) を選択します。 

    ![SelectDatasets](./media/select-datasets.png "データセットを選択する")    

1. [Recipients]\(受信者\) タブで、[+ Add Recipient]\(+ 受信者の追加\) を選択して、データ コンシューマーのメール アドレスを入力します。 

    ![AddRecipients](./media/add-recipient.png "受信者の追加") 

1. **[続行]** を選択します

1. データ コンシューマーがデータの増分更新を取得できるようにする場合は、スナップショットのスケジュールを有効にします。 

    ![EnableSnapshots](./media/enable-snapshots.png "スナップショットを有効化する") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** を選択します

1. [Review + Create]\(レビュー + 作成\) タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **[作成]**

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure データ共有を作成し、受信者を招待する方法について説明しました。 データ コンシューマーがデータ共有を受け入れて受信できるようにする方法については、続けて[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルを行ってください。 

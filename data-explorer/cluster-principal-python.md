---
title: Python を使用して Azure Data Explorer 用のクラスター プリンシパルを追加する
description: この記事では、Python を使用して Azure Data Explorer 用にクラスター プリンシパルを追加する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 02/03/2020
ms.openlocfilehash: 7081f96bf948a7d3f0e99b6d9f7aaf6fa77b9c65
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872218"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Python を使用して Azure Data Explorer 用のクラスター プリンシパルを追加する

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager テンプレート](cluster-principal-resource-manager.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 この記事では、Python を使用して Azure Data Explorer 用にクラスター プリンシパルを追加します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [クラスターを作成](create-cluster-database-python.md)します。

## <a name="install-python-package"></a>Python パッケージのインストール

Azure Data Explorer (Kusto) 向けの Python パッケージをインストールするには、Python をパスに設定した状態でコマンド プロンプトを開きます。 次のコマンドを実行します。

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>クラスター プリンシパルの追加

次の例は、プログラムによってクラスター プリンシパルを追加する方法を示しています。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| client_secret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resource_group_name | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| cluster_name | *mykustocluster* | ご利用のクラスターの名前。|
| principal_assignment_name | *clusterPrincipalAssignment1* | クラスター プリンシパル リソースの名前。|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | プリンシパル ID。ユーザーの電子メール、アプリケーション ID、またはセキュリティ グループ名を指定できます。|
| ロール (role) | *AllDatabasesAdmin* | クラスター プリンシパルのロール。'AllDatabasesAdmin' または 'AllDatabasesViewer' を指定できます。|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | プリンシパルのテナント ID。|
| principal_type | *アプリ* | プリンシパルの種類。'User'、'App'、または 'Group' を指定できます。|

## <a name="next-steps"></a>次のステップ

* [データベース プリンシパルを追加する](database-principal-python.md)

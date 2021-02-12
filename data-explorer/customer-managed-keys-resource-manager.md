---
title: Azure Resource Manager テンプレートを使用して Azure Data Explorer でカスタマー マネージド キーを構成する
description: この記事では、Azure Resource Manager テンプレートを使用して、Azure Data Explorer でデータに対するカスタマー マネージド キーの暗号化を構成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: d67705722fb3f78cc4e69b2dcb38153ab3800b0b
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872031"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する

> [!div class="op_single_selector"]
> * [ポータル](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager テンプレート](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

このセクションでは、Azure Resource Manager テンプレートを使用して、カスタマー マネージド キーを構成します。 既定では、Azure Data Explorer の暗号化は Microsoft マネージド キーを使用します。 この手順では、カスタマー マネージド キーを使用するように Azure Data Explorer クラスターを構成し、そのクラスターに関連付けるキーを指定します。

Azure portal または PowerShell を使用して、Azure Resource Manager テンプレートをデプロイできます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、新しいバージョンを使用するようにクラスターを更新する必要があります。 まず `Get-AzKeyVaultKey` を呼び出し、キーの最新バージョンを取得します。 次に、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」に示されているように、新しいバージョンのキーを使用するようにクラスターのキー コンテナーのプロパティを更新します。

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* [Azure Data Explorer クラスターのマネージド ID を構成する](managed-identities.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のディスク暗号化を使用してクラスターをセキュリティで保護する - Azure portal](cluster-disk-encryption.md)。
* [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)


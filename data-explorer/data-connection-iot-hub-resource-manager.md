---
title: Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に IoT Hub データ接続を作成する
description: この記事では、Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に IoT Hub データ接続を作成する方法について学習します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: a9a8c0883563d1b05ddc0289bc8782647987a07d
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873323"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に IoT Hub データ接続を作成する

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager テンプレート](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
この記事では、Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に IoT Hub データ接続を作成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [クラスターとデータベース](create-cluster-database-portal.md)を作成します
* [テーブルと列のマッピング](ingest-data-iot-hub.md#create-a-target-table-in-azure-data-explorer)を作成します
* [共有アクセス ポリシーを構成して IoT ハブ](ingest-data-iot-hub.md#create-an-iot-hub)を作成します

## <a name="azure-resource-manager-template-for-adding-an-iot-hub-data-connection"></a>IoT Hub データ接続を追加するための Azure Resource Manager テンプレート

次の例は、IoT Hub データ接続を追加するための Azure Resource Manager テンプレートを示しています。  フォームを使用して、[Azure portal でテンプレートの編集とデプロイを実行](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template)できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "IotHubs_iothubdemo_name": {
            "type": "string",
            "defaultValue": "iothubdemo",
            "metadata": {
                "description": "Specifies the IoT Hub name."
            }
        },
        "iothubpolices_iothubowner_name": {
            "type": "string",
            "defaultValue": "iothubowner",
            "metadata": {
                "description": "Specifies the shared access policy name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the IoT Hub."
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the IoT Hub"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the IoT Hub"
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
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "IotHub",
            "properties": {
                "iotHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.Devices/IotHubs', parameters('IotHubs_iothubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "sharedAccessPolicyName": "[parameters('iothubpolices_iothubowner_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

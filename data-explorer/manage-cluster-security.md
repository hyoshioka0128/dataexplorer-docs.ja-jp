---
title: Azure Data Explorer でのクラスターのセキュリティ保護
description: この記事では、Azure portal 内の Azure Data Explorer でクラスターをセキュリティで保護する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 3daf9f5af46338a450a1f1a4e9c80a6c83841343
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374020"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Azure Data Explorer でのクラスターのセキュリティ保護 - Azure portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 クラスター仮想マシンの OS とデータ ディスクのボリューム暗号化が提供されます。 また、[Azure Key Vault](/azure/key-vault/) とも統合されます。これにより、ディスク暗号化キーとシークレットを制御および管理し、VM ディスク上の全データを確実に暗号化することができます。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Azure portal で保存時の暗号化を有効にする
  
クラスターのセキュリティ設定を使用して、クラスターに対してディスクの暗号化を有効にすることができます。 クラスターに対して[保存時の暗号化](/azure/security/fundamentals/encryption-atrest)を有効にすると、格納されているデータ (保存時) のデータ保護が提供されます。 

1. Azure portal で、Azure Data Explorer クラスター リソースに移動します。 **[設定]** という見出しの下にある **[セキュリティ]** を選択します。 

    ![保存時の暗号化を有効にする](media/manage-cluster-security/security-encryption-at-rest.png)

1. **[セキュリティ]** ウィンドウで、 **[ディスクの暗号化]** セキュリティ設定に対して **[オン]** を選択します。 

1. **[保存]** を選択します。
 
> [!NOTE]
> 有効にした後で暗号化を無効にするには、 **[オフ]** を選択します。

## <a name="next-steps"></a>次のステップ

[クラスターの正常性を確認する](check-cluster-health.md)

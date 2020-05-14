---
title: Azure Data Explorer REST API - Azure Data Explorer
description: この記事では、Azure Data Explorer の Azure Data Explorer REST API について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6abfe15490e5c633e1cb7912f4794ee90bec1947
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373599"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer REST API

この記事では、HTTPS 経由で Kusto を操作する方法について説明します。

## <a name="supported-actions"></a>サポートされているアクション

エンドポイントでサポートされるアクションの一覧は、エンドポイントがエンジン エンドポイントか、データ管理エンドポイントかによって変わります。

|アクション         |HTTP 動詞   |URI テンプレート           |エンジン|データ管理|認証 |
|---------------|------------|-----------------------|------|---------------|---------------|
|クエリ          |GET または POST |/v1/rest/query         |はい   |いいえ             |はい            |
|クエリ          |GET または POST |/v2/rest/query         |はい   |いいえ             |はい            |
|管理     |POST        |/v1/rest/mgmt          |はい   |はい            |はい            |
|UI             |GET         |/                      |はい   |いいえ             |いいえ             |
|UI             |GET         |/{dbname}              |はい   |いいえ             |いいえ             |

*アクション*は、関連するアクティビティのグループを表します

* クエリ アクションでは、サービスにクエリが送信され、クエリの結果が取得されます。
* 管理アクションでは、管理コマンドがサービスに送信され、管理コマンドの結果が取得されます。
* UI アクションを使用して、デスクトップ クライアントまたは Web クライアントを起動できます。 アクションは、サービスと対話するために、HTTP リダイレクト応答を介して実行されます。

## <a name="next-steps"></a>次のステップ

クエリおよび管理アクションの HTTP 要求と応答の詳細については、以下を参照してください。
 * [クエリ管理の HTTP 要求](./request.md)
 * [クエリ管理の HTTP 応答](./response.md)
 * [クエリ v2 の HTTP 応答](./response2.md)

UI アクションの詳細については、以下を参照してください。
 * [UI ディープ リンク](./deeplink.md)

---
title: getyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの getyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032cc319661218e77d5b23e6c649de7d5856d6c9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347648"
---
# <a name="getyear"></a>getyear()

引数の年の部分を返し `datetime` ます。

## <a name="example"></a>例

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```
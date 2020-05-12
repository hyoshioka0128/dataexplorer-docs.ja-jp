---
title: array_index_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_index_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 99044d8762a1c7c7e86fb2633a8226ef48d66b55
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225634"
---
# <a name="array_index_of"></a>array_index_of()

指定した項目を配列内で検索し、その位置を返します。

**構文**

`array_index_of(`*array*、*value*`)`

**引数**

* *array*: 検索する入力配列。
* *値*: 検索対象の値。 値の型は long、integer、double、datetime、timespan、decimal、string、または guid である必要があります。

**戻り値**

検索の0から始まるインデックス位置。
値が配列に見つからない場合は、-1 を返します。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|結果|
|---|
|3|

**参照**

配列に値が存在するかどうかを確認するだけで、その位置に関心がない場合は、 [set_has_element ( `arr` 、 `value` )](sethaselementfunction.md)を使用できます。 この関数を使用すると、クエリの読みやすさが向上します。 両方の関数のパフォーマンスは同じです。

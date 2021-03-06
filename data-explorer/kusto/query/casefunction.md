---
title: ケース ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのケース () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b493f74472454649a557b7e3677b26af169413de
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227555"
---
# <a name="case"></a>case()

述語の一覧を評価し、述語が満たされている最初の結果式を返します。

いずれの述語もを返さない場合 `true` 、最後の式 () の結果 `else` が返されます。
すべての奇数の引数 (カウントは1から開始) は、値に評価される式である必要があり `boolean` ます。
すべての偶数個の引数 (と) `then` と最後の引数 () は、 `else` 同じ型である必要があります。

**構文**

`case(`*predicate_1* `,`*then_1*、 *predicate_2* `,` *then_2*、 *predicate_3* `,` *then_3*、その*他*`)`

**引数**

* *predicate_i*: 値に評価される式 `boolean` 。
* *then_i*: *predicate_i*がに評価される最初の述語の場合、評価された式とその値が関数から返され `true` ます。
* *else*: 評価される式とその値が関数から返されます (どちらの*predicate_i*もに評価されない場合) `true` 。

**戻り値**

*Predicate_i*評価される最初の*then_i*の値、 `true` またはいずれの述語も満たされていない場合は*else*の値。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|サイズ|bucket|
|---|---|
|1|Small|
|3|Small|
|5|Medium|
|7|Medium|
|9|Medium|
|11|Large|
|13|Large|
|15|Large|

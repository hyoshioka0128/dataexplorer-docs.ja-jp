---
title: array_shift_right ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_shift_right () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 714c6c15443420abbc973593acb2f311a5507dc4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225668"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`配列内の値を右にシフトします。

**構文**

`array_shift_right(`*arr*、 *shift_count* [、 *fill_value* ]`)`

**引数**

* *arr*: 分割する入力配列。動的配列である必要があります。
* *shift_count*: 配列要素が右にシフトされる位置の数を指定する整数。 値が負の場合、要素は左にシフトされます。
* *fill_value*: 移動および削除された要素の代わりに要素を挿入するために使用されるスカラー値。 既定値: null 値または空の文字列 ( *arr*の種類によって異なります)。

**戻り値**

元の配列と同じ量の要素を格納している動的配列。各要素は*shift_count*に従ってシフトされます。 削除される要素の代わりに追加される新しい要素には、 *fill_value*の値が設定されます。

**参照**

* 配列を左に移動する方法については、「 [array_shift_left ()](array_shift_leftfunction.md)」を参照してください。
* 配列を右に回転する方法については、「 [array_rotate_right ()](array_rotate_rightfunction.md)」を参照してください。
* 配列を左に回転する方法については、「 [array_rotate_left ()](array_rotate_leftfunction.md)」を参照してください。

**使用例**

* 2つの位置で右に移動します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[null、null、1、2、3]|

* 2つの位置で右にシフトし、既定値を追加します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[-1、-1、1、2、3]|


* 負の shift_count 値を使用して、2つの位置で左に移動します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[3、4、5、-1、-1]|
    
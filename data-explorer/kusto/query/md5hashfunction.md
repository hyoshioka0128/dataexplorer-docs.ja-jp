---
title: hash_md5() - Azure Data Explorer
description: この記事では、Azure Data Explorer の hash_md5 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: 17b3e0d13f8048ccac90add4eee6f02f3d2e2959
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346798"
---
# <a name="hash_md5"></a>hash_md5()

入力値の MD5 ハッシュ値を返します。

## <a name="syntax"></a>構文

`hash_md5(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: ハッシュされる値。

## <a name="returns"></a>戻り値

16進数文字列としてエンコードされた、指定されたスカラーの MD5 ハッシュ値 (文字の文字列)。これらの2つは、それぞれ0と255の間の1つの16進数を表します。

> [!WARNING]
> この関数 (MD5) で使用されるアルゴリズムは、今後変更されることがないことが保証されていますが、計算が非常に複雑です。 1つのクエリの実行中に "ライトウェイト" ハッシュ関数を必要とするユーザーには、代わりに関数[hash ()](./hashfunction.md)を使用することをお勧めします。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1f8db31fee25ea8a9390b|


次の例では、関数を使用して、 `hash_md5()` 状態の MD5 ハッシュ値に基づいて StormEvents を集計します。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|State|StateHash|StormCount|
|---|---|---|
|テキサス州|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|カンザス|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|アイオワ州|6d4a7c02942f093576149db764d4e2d2|2337|
|イリノイ州|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|州|2d82f0c963c0763012b2539d469e5008|2016|

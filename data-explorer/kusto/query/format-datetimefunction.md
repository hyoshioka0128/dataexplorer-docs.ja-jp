---
title: format_datetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの format_datetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 073bf45977648bd654f72fff47b62f92ac1b3d27
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227385"
---
# <a name="format_datetime"></a>format_datetime()

指定された形式に従って datetime を書式設定します。

```kusto
format_datetime(datetime(2015-12-14 02:03:04.12345), 'y-M-d h:m:s.fffffff') == "15-12-14 2:3:4.1234500"
```

**構文**

`format_datetime(`*datetime* `,`*形式*`)`

**引数**

* `datetime`: 型の値 `datetime` 。
* `format`: 書式指定子文字列。1つ以上の[書式要素](#supported-formats)で構成されます。

**戻り値**

形式の結果を含む文字列。

## <a name="supported-formats"></a>サポートされるフォーマット

|書式指定子   |説明    |使用例
|---|---|---
|`d`    |月の日にち (1 ～ 31)。 | 2009-06-01T13:45:30-> 1、2009-06-15T13:45:30-> 15
|`dd`   |月の日にち (01 ～ 31)。| 2009-06-01T13:45:30-> 01、2009-06-15T13:45:30-> 15
|`f`    |日時値の秒部分の 1/10。 |2009-06-15T13:45: > 30.6170000 6, 2009-06-15T13:45: > 30.05 0
|`ff`   |日時値の秒部分の 1/100。 |2009-06-15T13:45: 30.6170000-> 61、2009-06-15T13:45: > 30.0050000 00
|`fff`  |日時値の秒部分の 1/1000。 |6/15/2009 13:45: 30.617-> 617、6/15/2009 13:45: > 30.0005 000
|`ffff` |日時値の秒部分の 1/10000。 |2009-06-15T13:45: 30.6175000-> 6175、2009-06-15T13:45: > 30.0000500 0000
|`fffff`    |日時値の秒部分の 1/100000。 |2009-06-15T13:45: 30.6175400-> 61754、2009-06-15T13:45: > 30.000005 00000
|`ffffff`   |日時値の秒部分の 1/1000000。 |2009-06-15T13:45: 30.6175420-> 617542、2009-06-15T13:45: > 30.0000005 000000
|`fffffff`  |日時値の秒部分の 1/10000000。 |2009-06-15T13:45: 30.6175425-> 6175425、2009-06-15T13:45: > 30.0001150 0001150
|`F`    |日時値の秒部分の 1/10 (0 以外の場合)。 |2009-06-15T13:45: > 30.6170000 6, 2009-06-15T13:45: > 30.0500000 (出力なし)
|`FF`   |日時値の秒部分の 1/100 (0 以外の場合)。 |2009-06-15T13:45: 30.6170000-> 61、2009-06-15T13:45: > 30.0050000 (出力なし)
|`FFF`  |日時値の秒部分の 1/1000 (0 以外の場合)。 |2009-06-15T13:45: 30.6170000-> 617、2009-06-15T13:45: > 30.0005000 (出力なし)
|`FFFF` |日時値の秒部分の 1/10000 (0 以外の場合)。 |2009-06-15T13:45: 30.5275000-> 5275、2009-06-15T13:45: > 30.0000500 (出力なし)
|`FFFFF`    |日時値の秒部分の 1/100000 (0 以外の場合)。 |2009-06-15T13:45: 30.6175400-> 61754、2009-06-15T13:45: > 30.0000050 (出力なし)
|`FFFFFF`   |日時値の秒部分の 1/1000000 (0 以外の場合)。 |2009-06-15T13:45: 30.6175420-> 617542、2009-06-15T13:45: > 30.0000005 (出力なし)
|`FFFFFFF`  |日時値の秒部分の 1/10000000 (0 以外の場合)。 |2009-06-15T13:45: 30.6175425-> 6175425、2009-06-15T13:45: > 30.0001150 000115
|`h`    |12 時間形式の時間 (1 ～ 12)。 |2009-06-15T01:45:30-> 1、2009-06-15T01:45:30-> 1
|`hh`   |12 時間形式の時間 (01 ～ 12)。 |2009-06-15T01:45:30-> 01、2009-06-15T01:45:30-> 01
|`H`    |24 時間形式の時間 (0 ～ 23)。 |2009-06-15T01:45:30-> 1、2009-06-15T01:45:30-> 13
|`HH`   |24 時間形式の時間 (00 ～ 23)。 |2009-06-15T01:45:30-> 01、2009-06-15T01:45:30-> 13
|`m`    |分 (0 ～ 59)。 |2009-06-15T01:09:30-> 9、2009-06-15T01:29:30-> 29
|`mm`   |分 (00 ～ 59)。 |2009-06-15T01:09:30-> 09, 2009-06-15T01:45:30-> 45
|`M`    |月 (1 ～ 12)。 |2009-06-15T13:45:30 -> 6
|`MM`   |月 (01 ～ 12)。|2009-06-15T13:45:30 -> 06
|`s`    |秒 (0 ～ 59)。 |2009-06-15T13:45:09 -> 9
|`ss`   |秒 (00 ～ 59)。 |2009-06-15T13:45:09 -> 09
|`y`    |年 (0 ～ 99)。 |0001-01-01T00:00:00-> 1、0900-01-01T00:00:00-> 0、1900-01-01T00:00:00-> 0、2009-06-15T13:45:30-> 9、2019-06-15T13:45:30-> 19
|`yy`   |年 (00 ～ 99)。 | 0001-01-01T00:00:00-> 01、0900-01-01T00:00:00-> 00、1900-01-01T00:00:00-> 00、2019-06-15T13:45:30-> 19
|`yyyy` |年 (4 桁の数値)。 | 0001-01-01T00:00:00-> 0001、0900-01-01T00:00:00-> 0900、1900-01-01T00:00:00-> 1900、2009-06-15T13:45:30-> 2009
|`tt`   |AM/PM 時間 |2009-06-15T13:45:09-> PM

**サポートされている区切り**

書式指定子には、次の区切り文字を含めることができます。

|区切り記号|解説|
|---------|-------|
|`' '`| Space|
|`'/'`||
|`'-'`|ダッシュ|
|`':'`||
|`','`||
|`'.'`||
|`'_'`||
|`'['`||
|`']'`||

**使用例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let dt = datetime(2017-01-29 09:00:05);
print 
v1=format_datetime(dt,'yy-MM-dd [HH:mm:ss]'), 
v2=format_datetime(dt, 'yyyy-M-dd [H:mm:ss]'),
v3=format_datetime(dt, 'yy-MM-dd [hh:mm:ss tt]')
```

|v1|v2|v3|
|---|---|---|
|17-01-29 [09:00:05]|2017-1-29 [9:00:05]|17-01-29 [09:00:05 AM]|
---
title: ストリーミングインジェストポリシー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのストリーミングインジェストポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: a0141482e3714ed710dbdc6fa00e3b8b396e77e6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373307"
---
# <a name="streaming-ingestion-policy"></a>ストリーミング インジェスト ポリシー

## <a name="streaming-ingestion-target-scenario"></a>ストリーミング取り込みターゲットシナリオ

ストリーミング インジェストは、多様なボリューム データでインジェスト時間が 10 秒未満の低待機時間を必要とするシナリオを対象とします。 1つまたは複数のデータベースで、複数のテーブルの操作処理を最適化するために使用されます。1つ以上のデータベースで、各テーブルへのデータストリームが比較的小さい (1 秒あたりのレコード数が少ない) が、全体的なデータインジェストボリュームは高くなります (1 秒あたり数千のレコード)。

データ量がテーブルごとに 1 MB/秒を超える場合は、ストリーミング インジェストではなく、従来の (一括) インジェストを使用します。 

* この機能を実装する方法については、[ストリーミングインジェスト](../../ingest-data-streaming.md)に関するページを参照してください。
* ストリーミングインジェスト制御コマンドの詳細については、「 [control コマンドを使用してストリーミングインジェストポリシーを管理する](../management/streamingingestion-policy.md)」を参照してください。

## <a name="streaming-ingestion-policy-definition"></a>ストリーミングインジェストポリシーの定義

ストリーミングインジェストポリシーは、テーブルまたはデータベースで定義できます。 このポリシーをデータベースレベルで定義すると、データベース内の既存および将来のすべてのテーブルに同じ設定が適用されます。 ストリーミングインジェストポリシーがテーブルレベルとデータベースレベルの両方で設定されている場合は、テーブルレベルの設定が優先されます。

> [!NOTE]
> テーブルでストリーミングインジェストが直接取得されず、更新ポリシーによってのみ使用される場合は、このテーブルにストリーミングインジェストポリシーを定義する必要はありません。 

ストリーミングインジェストポリシーは、テーブルのストリーミングデータが分散される行ストアの最大数を設定します。 ディストリビューションは、可用性とデータレートの両方のサポートに必要です。

## <a name="setting-the-number-of-row-stores"></a>行ストアの数の設定

ストリーミングインジェストポリシーで設定されている行ストアの数を定義する必要があります。 この数は、テーブルごとのストリーミングデータ速度に基づいている必要があります (大まかな見積もりで十分です)。
テーブルに対して推奨される行ストアの最小数は4です。 サポートされている行ストアの最大数は64です。
テーブルのストリーミングデータ速度が高いほど、関連するストリーミングインジェストポリシーで必要な行ストアの数が増加します。
推奨される設定については、次の表を使用してください (不明な場合は、より大きい値を使用してください)。

|ピーク時のピーク時ストリーミングデータ速度 (テーブルあたり)|行ストアの数|
|----------|------|
|< 1 Gb/時間 |4|
|1-2 GB/時間 |4-8|
|2-3 GB/時間 |8-12|
|3-4 GB/時間 |12-16|
| > 4 GB/時間 |

 この詳細については、[サポートチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開いてください。

クエリの待機時間を最適化するには、テーブルごとに定義されている行ストアの数が、上記の推奨値を大幅に超えることはできません。

> [!NOTE]
> データベースのストリーミングインジェストポリシーを設定するときは、最も高いデータレートを持つテーブルに必要な行ストアの数を割り当てます。 
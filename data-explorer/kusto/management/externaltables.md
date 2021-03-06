---
title: Kusto External table 全般制御コマンド-Azure データエクスプローラー
description: この記事では、一般的な外部テーブルコントロールコマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2020
ms.openlocfilehash: a08f1f154c0efa17164d15a075456e2b6fab3212
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867088"
---
# <a name="external-table-general-control-commands"></a>外部テーブル全般制御コマンド

外部テーブルの概要については、「[外部テーブル](../query/schema-entities/externaltables.md)」を参照してください。 次のコマンドは _、任意の外部テーブル_(任意の型) に関連しています。

## <a name="show-external-tables"></a>。外部テーブルを表示します。

* データベース内のすべての外部テーブル (または特定の外部テーブル) を返します。
* [データベースモニターのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**出力**

| 出力パラメーター | Type   | 説明                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | 外部テーブルの名前                                             |
| TableType        | string | 外部テーブルの種類                                              |
| フォルダー           | string | テーブルのフォルダー                                                     |
| DocString        | string | テーブルをドキュメント化する文字列                                       |
| プロパティ       | string | テーブルの JSON でシリアル化されたプロパティ (テーブルの型に固有) |


**例:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | フォルダー         | DocString | プロパティ |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | ドキュメント      | {}         |


## <a name="show-external-table-schema"></a>。外部テーブルスキーマを表示します

* JSON または CSL として、外部テーブルのスキーマを返します。 
* [データベースモニターのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external` `table` *TableName* `schema` `as` ( `json`  |  `csl` )

`.show` `external` `table` *TableName* `cslschema`

**出力**

| 出力パラメーター | Type   | 説明                        |
|------------------|--------|------------------------------------|
| TableName        | string | 外部テーブルの名前            |
| スキーマ           | string | JSON 形式のテーブルスキーマ |
| DatabaseName     | string | テーブルのデータベース名             |
| フォルダー           | string | テーブルのフォルダー                    |
| DocString        | string | テーブルをドキュメント化する文字列      |

**例:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**出力:**

*json*

| TableName | スキーマ    | DatabaseName | フォルダー         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "ExternalBlob",<br>"Folder": "ExternalTables",<br>"DocString": "Docs",<br>"OrderedColumns": [{"Name": "x", "Type": "system.string", "DocString", "Type": "system.string", "CslType": "string", "DocString": ""}]} を指定すると、"{" Name ":" x "," Type ":" system.object "," " | DB           | ExternalTables | ドキュメント      |


*csl*

| TableName | スキーマ          | DatabaseName | フォルダー         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long、-string | DB           | ExternalTables | ドキュメント      |

## <a name="drop-external-table"></a>。外部テーブルを削除します。

* 外部テーブルを削除します。 
* この操作後、外部テーブルの定義を復元できません
* [Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :**  

`.drop` `external` `table` *TableName*

**出力**

削除されたテーブルのプロパティを返します。 「[外部テーブルを表示する」を](#show-external-tables)参照してください。

**例:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | フォルダー         | DocString | スキーマ       | プロパティ |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | ドキュメント      | [{"Name": "x", "CslType": "long"},<br> {"Name": "s", "CslType": "string"}] | {}         |

## <a name="next-steps"></a>次のステップ

* [Azure Storage または Azure Data Lake で外部テーブルを作成および変更する](external-tables-azurestorage-azuredatalake.md)
* [外部 SQL テーブルを作成および変更する](external-sql-tables.md)

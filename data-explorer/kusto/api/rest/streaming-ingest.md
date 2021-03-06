---
title: ストリーミングインジェスト HTTP 要求-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのストリーミングインジェスト HTTP 要求について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 87b68e4a9de42a9a7085238db5919066d577ed1f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373536"
---
# <a name="streaming-ingestion-http-request"></a>ストリーミングインジェスト HTTP 要求

## <a name="request-verb-and-resource"></a>動詞とリソースの要求

|アクション    |HTTP 動詞|HTTP リソース                                               |
|----------|---------|------------------------------------------------------------|
|取り込み    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>要求パラメーター

| パラメーター    | 説明                                                                 | 必須/オプション |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   インジェスト要求の対象データベースの名前                     |  必須         |
| `{table}`    |   インジェスト要求の対象テーブルの名前                        |  必須         |

## <a name="additional-parameters"></a>追加パラメーター

追加のパラメーターは、URL クエリのペアとして書式設定され `{name}={value}` 、& 文字で区切られます。

| パラメーター    | 説明                                                                          | 必須/オプション   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| 要求本文内のデータの形式を指定します。 値は、、、、、、、、のいずれかである必要があります `CSV` `TSV` `SCsv` `SOHsv` `PSV` `JSON` `MultiJSON` `Avro` 。 詳細については、「[サポートされるデータ形式](../../../ingestion-supported-formats.md)」を参照してください。| 必須 |
|`mappingName` | テーブルで定義されている事前に作成されたインジェストマッピングの名前。 詳細については、「[データのマッピング](../../management/mappings.md)」を参照してください。 テーブルで事前に作成されたマッピングを管理する方法については、[こちら](../../management/create-ingestion-mapping-command.md)を参照してください。| 省略可能ですが `streamFormat` 、が、、 `JSON` `MultiJSON` またはのいずれかの場合に必要です。`Avro`|  |
              
たとえば、CSV 形式のデータをデータベース内のテーブルに取り込むには、 `Logs` `Test` 次のように使用します。

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

事前に作成されたマッピングで JSON 形式のデータを取り込むには `mylogmapping` 、次のように使用します。

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>要求ヘッダー

次の表に、クエリおよび管理操作の共通ヘッダーを示します。

|標準ヘッダー   | 説明                                                                               | 必須/オプション | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | この値をに設定 `application/json` します。                                                     | 省略可能          |
|`Accept-Encoding` | サポートされているエンコーディングは `gzip` と `deflate` です。                                             | 省略可能          | 
|`Authorization`   | 「[認証](./authentication.md)」を参照してください。                                                | 必須          |
|`Connection`      | `Keep-Alive`を有効にする:                                                                      | 省略可能          |
|`Content-Length`  | 要求本文の長さを指定します (既知の場合)。                                              | 省略可能          |
|`Content-Encoding`| をに設定します `gzip` が、本文は gzip で圧縮する必要があります                                        | 省略可能          |
|`Expect`          | `100-Continue` を設定します。                                                                    | 省略可能          |
|`Host`            | は、要求を送信したドメイン名 (など) に設定し `help.kusto.windows.net` ます。 | 必須          |

次の表に、クエリおよび管理操作の一般的なカスタムヘッダーを示します。 特に明記されていない限り、ヘッダーはテレメトリのみを目的としたものであり、機能には影響しません。

|カスタムヘッダー           |説明                                                                           | 必須/オプション |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |要求を行っているアプリケーションのフレンドリ名。                            | 省略可能          |
|`x-ms-user`             |要求を行っているユーザーのフレンドリ名。                                   | 省略可能          |
|`x-ms-user-id`          |`x-ms-user` と同じ。                                                                  | 省略可能          |
|`x-ms-client-request-id`|要求の一意の識別子。                                                  | 省略可能          |
|`x-ms-client-version`   |要求を行っているクライアントの (わかりやすい) バージョン識別子。 実行中のクエリの取り消しなど、要求を識別するために使用されるシナリオで必要です。                                                        | 省略可/必須  |

## <a name="body"></a>Body

本文は、取り込まれたする実際のデータです。 テキスト形式では、UTF-8 エンコードを使用する必要があります。

## <a name="examples"></a>例

次の例は、取り込み JSON コンテンツに対する HTTP POST 要求を示しています。

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求ヘッダー:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文:

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

次の例は、同じ圧縮データを取り込みするための HTTP POST 要求を示しています。

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求ヘッダー:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文:

```
... binary data ...
```

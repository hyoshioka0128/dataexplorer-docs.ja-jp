---
title: Kusto. インジェストコードの例-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto によるインジェストコードの例について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2019
ms.openlocfilehash: 73c2a01d3b71a541e8d3152f190732c394f5c435
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722118"
---
# <a name="kustoingest-ingestion-code-examples"></a>Kusto. インジェストの取り込みコードの例

この短いコードスニペットのコレクションは、Kusto テーブルにデータを取り込みするさまざまな手法を示しています。

> [!NOTE]
> これらの例は、インジェストの後すぐにインジェストクライアントが破棄されるかのように見えます。 これを文字どおりに実行しないでください。
> インジェストクライアントは再入可能でスレッドセーフであり、大量に作成することはできません。 インジェストクライアントインスタンスの推奨カーディナリティは、ホストプロセスごと、ターゲット Kusto クラスターごとに1つです。

## <a name="async-ingestion-from-a-single-azure-blob"></a>単一の Azure blob からの非同期インジェスト

1つの Azure blob からの非同期インジェストには、省略可能な RetryPolicy で KustoQueuedIngestClient を使用します。

```csharp
//Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create an ingest client
// Note, that creating a separate instance per ingestion operation is an anti-pattern.
// IngestClient classes are thread-safe and intended for reuse
IKustoIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from blobs according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

var sourceOptions = new StorageSourceOptions() { DeleteSourceOnSuccess = true };

//// Create your custom implementation of IRetryPolicy, which will affect how the ingest client handles retrying on transient failures
IRetryPolicy retryPolicy = new NoRetry();
//// This line sets the retry policy on the ingest client that will be enforced on every ingest call from here on
((IKustoQueuedIngestClient)client).QueueRetryPolicy = retryPolicy;

await client.IngestFromStorageAsync(uri: @"BLOB-URI-WITH-SAS-KEY", ingestionProperties: kustoIngestionProperties, sourceOptions);

client.Dispose();
```

## <a name="ingest-from-local-file"></a>ローカルファイルから取り込み 

KustoDirectIngestClient を使用して、ローカルファイルから取り込みます。


> [!NOTE]
> この方法は、制限されたボリュームと低頻度のインジェストにお勧めします。

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderEngine =
    new KustoConnectionStringBuilder(@"https://{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
using (IKustoIngestClient client = KustoIngestFactory.CreateDirectIngestClient(kustoConnectionStringBuilderEngine))
{
    //Ingest from blobs according to the required properties
    var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

    client.IngestFromStorageAsync(@"< Path to local file >", ingestionProperties: kustoIngestionProperties).GetAwaiter().GetResult();
}
```

## <a name="ingest-from-local-files-and-validate-ingestion"></a>ローカルファイルから取り込み、インジェストを検証する

KustoQueuedIngestClient を使用してローカルファイルから取り込み、インジェストを検証します。

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from files according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

client.IngestFromStorageAsync(@"ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync(@"InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailuresAsync().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailuresAsync().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-local-files-and-report-status-to-a-queue"></a>ローカルファイルから取り込み、キューに状態を報告する

KustoQueuedIngestClient を使用して、ローカルファイルから取り込み、状態をキューに報告します。

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myTable")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level - which is demonstrated in the
    // 'Ingest From Local File(s) using KustoQueuedIngestClient and Ingestion Validation' section)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice. 'Queue' is the default method.
    // For the sake of the example, we will choose it anyway. 
    ReportMethod = IngestionReportMethod.Queue
};

client.IngestFromStorageAsync("ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync("InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");

// Verify the success has also been reported to the queue
var ingestionSuccesses = client.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();
Ensure.ConditionIsMet((ingestionSuccesses.Count() > 0),
    "The successful ingestion should have been reported to the successful ingestions queue");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-local-files-and-report-status-to-a-table"></a>ローカルファイルから取り込み、テーブルに状態を報告する

KustoQueuedIngestClient を使用して、ローカルファイルから取り込み、テーブルに状態を報告します。

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myDB")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice
    ReportMethod = IngestionReportMethod.Table
};

var filePath = @"< Path to file >";
var fileIdentifier = Guid.NewGuid();
var fileDescription = new FileDescription() { FilePath = filePath, SourceId = fileIdentifier };
var sourceOptions = new StorageSourceOptions() { SourceId = fileDescription.SourceId.Value };

// Execute the ingest operation and save the result.
var clientResult = await client.IngestFromStorageAsync(fileDescription.FilePath,
    ingestionProperties: kustoIngestionProperties, sourceOptions);

// Use the fileIdentifier you supplied to get the status of your ingestion 
var ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
while (ingestionStatus.Status == Status.Pending)
{
    // Wait a minute...
    Thread.Sleep(TimeSpan.FromMinutes(1));
    // Try again
    ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
}

// Verify the results of the ingestion
Ensure.ConditionIsMet(ingestionStatus.Status == Status.Succeeded,
    "The file should have been ingested successfully");

// Dispose of the client
client.Dispose();
```

## <a name="next-steps"></a>次のステップ

* [Kusto. インジェストクライアントリファレンス](kusto-ingest-client-reference.md)
* [Kusto. 取り込み操作の状態](kusto-ingest-client-errors.md)
* [Kusto. インジェスト例外](kusto-ingest-client-errors.md)
* [Kusto の接続文字列](../connection-strings/kusto.md)
* [Kusto 承認モデル](../../management/security-roles.md)

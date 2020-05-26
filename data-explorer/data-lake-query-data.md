---
title: Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する
description: Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: b7ca4a41bb15e9fb8a4f55a9d674b1536dddd5ae
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226399"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する

Azure Data Lake Storage は、スケーラビリティが高く拡張性と費用対効果に優れた、ビッグ データ分析用のデータ レイク ソリューションです。 ハイパフォーマンス ファイル システムの能力に加えて、非常に高いスケーラビリティと効率性を兼ね備えており、お客様が分析情報を得るまでの時間を短縮するのに役立ちます。 Data Lake Storage Gen2 は Azure BLOB ストレージの機能を拡張するもので、分析ワークロード用に最適化されています。
 
Azure Data Explorer は、Azure Blob Storage および Azure Data Lake Storage (Gen1 および Gen2) と統合され、レイク内のデータへの高速でキャッシュされたインデックス付きのアクセスを提供します。 レイク内のデータは、Azure Data Explorer に事前に取り込まずに分析およびクエリを実行できます。 また、取り込んだデータと取り込んでいないネイティブ　レイク データに対して同時にクエリを実行することもできます。  

> [!TIP]
> 最良のクエリ パフォーマンスを得るには、Azure Data Explorer へのデータの取り込みが必要です。 事前に取り込まずに外部データのクエリを実行する機能は、履歴データか、ほとんどクエリが実行されないデータに対してのみ使用してください。 最高の結果を得るために、[レイク内でのクエリのパフォーマンスを最適化](#optimize-your-query-performance)してください。
 

## <a name="create-an-external-table"></a>外部テーブルを作成する

 > [!NOTE]
 > 現在サポートされているストレージ アカウントは、Azure Blob Storage または Azure Data Lake Storage (Gen1 および Gen2) です。

1. `.create external table` コマンドを使用して、Azure Data Explorer に外部テーブルを作成します。 `.show`、`.drop`、および `.alter` などの追加の外部テーブル コマンドについては、「[外部テーブル コマンド](kusto/management/externaltables.md)」に記載されています。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * 細かいパーティション分割を使用することでパフォーマンスの向上が期待されます。 たとえば、日単位のパーティションを使用する外部テーブルでのクエリは、月単位のパーティション テーブルを使用したクエリよりもパフォーマンスが良くなります。
    > * パーティションを使用する外部テーブルを定義するときは、ストレージ構造が同一であると想定します。
たとえば、テーブルが yyyy/MM/dd 形式 (既定) の DateTime パーティションで定義されている場合、URI ストレージ ファイルのパスは *container1/yyyy/MM/dd/all_exported_blobs* である必要があります。 
    > * 外部テーブルが datetime 列によってパーティション分割されている場合は、閉じた範囲の時間フィルターをクエリに含めるようにしてください (たとえば、範囲が閉じていないこちらの `ArchivedProducts | where Timestamp > ago(1h)` よりも、クエリ `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` の方がパフォーマンスが良くなります)。 
    > * 外部テーブルを使用して、すべての[サポートされているインジェスト形式](ingestion-supported-formats.md)のクエリを実行できます。

1. 外部テーブルは Web UI の左側のウィンドウに表示されます

    ![Web UI の外部テーブル](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>json 形式で外部テーブルを作成する

json 形式で外部テーブルを作成することができます。 詳細については、[外部テーブルのコマンド](kusto/management/externaltables.md)に関する記事を参照してください。

1. `.create external table` コマンドを使用して、*ExternalTableJson* という名前のテーブルを作成します。

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. json 形式では、次に示すように列へのマッピングを作成する 2 番目の手順が必要です。 次のクエリでは、*mappingName* という名前の特定の json マッピングを作成します。

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部テーブルのアクセス許可
 
* データベース ユーザーは外部テーブルを作成できます。 テーブルの作成者は、自動的にそのテーブルの管理者になります。
* クラスター、データベース、またはテーブルの管理者は、既存のテーブルを編集できます。
* すべてのデータベースのユーザーまたは閲覧者は、外部テーブルのクエリを実行できます。
 
## <a name="query-an-external-table"></a>外部テーブルのクエリを実行する
 
外部テーブルのクエリを実行するには、`external_table()` 関数を使用し、関数の引数としてテーブル名を指定します。 クエリの残りの部分は、標準的な Kusto クエリ言語です。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense は、現在、外部テーブルのクエリではサポートされていません。

### <a name="query-an-external-table-with-json-format"></a>json 形式で外部テーブルのクエリを実行する

json 形式で外部テーブルのクエリを実行するには、`external_table()` 関数を使用し、関数の引数としてテーブル名とマッピング名の両方を指定します。 以下のクエリでは、*mappingName* が指定されていない場合は、以前に作成したマッピングが使用されます。

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>外部データと取り込んだデータのクエリをまとめて実行する

同じクエリ内で、外部テーブルと取り込んだデータ テーブルの両方のクエリを実行できます。 外部テーブルを、Azure Data Explorer、SQL サーバー、またはその他のソースからの追加データと [`join`](kusto/query/joinoperator.md) または [`union`](kusto/query/unionoperator.md) します。 [`let( ) statement`](kusto/query/letstatement.md) を使用して、外部テーブル参照に短縮名を割り当てます。

以下の例で、*Products* は取り込んだデータ テーブルで、*ArchivedProducts* は、Azure Data Lake Storage Gen2 のデータを含む外部テーブルです。

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>ヘルプ クラスターで *TaxiRides* 外部テーブルのクエリを実行する

*TaxiRides* サンプル データ セットには、[ニューヨーク市タクシーおよびリムジン委員会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)からのニューヨーク市のタクシーのデータが含まれています。

### <a name="create-external-table-taxirides"></a>外部テーブル *TaxiRides* を作成する 

> [!NOTE]
> このセクションでは、*ヘルプ* クラスターに *TaxiRides* 外部テーブルを作成するために使用されるクエリを示します。 このテーブルは既に作成済みのため、このセクションをスキップして、[*TaxiRides* 外部テーブル データのクエリを実行](#query-taxirides-external-table-data)できます。 

1. ヘルプ クラスターに外部テーブル *TaxiRides* を作成するために、次のクエリが使用されました。 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 結果のテーブルが *ヘルプ* クラスターに作成されました。

    ![TaxiRides 外部テーブル](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>*TaxiRides* 外部テーブル データのクエリを実行する 

[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) にサインインして、*TaxiRides* 外部テーブルのクエリを実行します。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>パーティション分割を行わずに *TaxiRides* 外部テーブルのクエリを実行する

外部テーブル *TaxiRides* で[このクエリを実行](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)して、データ セット全体で曜日別の乗車状況を示します。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

このクエリにより、一週間で最も忙しい曜日が示されます。 データがパーティション分割されていないため、このクエリでは、結果が返されるまでに長い時間がかかる可能性があります (最大 7 分)。

![パーティション分割されていないクエリのレンダリング](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>パーティション分割を行って TaxiRides 外部テーブルのクエリを実行する 

外部テーブル *TaxiRides* に対して[このクエリを実行](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)すると、2017 年 1 月に使用されたタクシーの種類 (イエローまたはグリーン) が示されます。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

このクエリではパーティション分割を使用しています。それにより、クエリの時間とパフォーマンスが最適化されます。 クエリではパーティション分割された列 (pickup_datetime) でフィルター処理が実行されて、数秒で結果が返されます。

![パーティション分割されたクエリのレンダリング](media/data-lake-query-data/taxirides-with-partition.png)
  
外部テーブル *TaxiRides* で実行する追加のクエリを記述することで、このデータについてさらに詳しく知ることができます。 

## <a name="optimize-your-query-performance"></a>クエリ パフォーマンスを最適化する

外部データに対するクエリの実行に関する次のベスト プラクティスに従って、レイクでのクエリのパフォーマンスを最適化します。 
 
### <a name="data-format"></a>データ形式
 
分析クエリには列形式を使用します。なぜなら、
* クエリに関連する列のみを読み取ることができるためです。 
* 列エンコード手法を使用すると、データのサイズを大幅に削減できるためです。  
Azure Data Explorer は、Parquet および ORC の列形式をサポートしています。 最適化された実装である Parquet 形式が推奨されます。 
 
### <a name="azure-region"></a>Azure リージョン 
 
外部データが Azure Data Explorer クラスターと同じ Azure リージョンに存在することを確認します。 これにより、コストとデータのフェッチ時間が削減されます。
 
### <a name="file-size"></a>ファイル サイズ
 
最適なファイルサイズは、ファイルあたり数百 MB (最大 1 GB) です。 低速のファイル列挙プロセスや列形式の使用の制限などの、不要なオーバーヘッドを要求する大量の小さなファイルは避けてください。 ファイルの数は、Azure Data Explorer クラスター内の CPU コアの数より多くする必要があることに注意してください。 
 
### <a name="compression"></a>圧縮
 
圧縮を使用して、リモート ストレージからフェッチされるデータの量を減らします。 Parquet 形式の場合は、内部 Parquet 圧縮メカニズムを使用して列グループを個別に圧縮するため、個別に読み取ることができます。 圧縮メカニズムの使用を検証するには、ファイル名が "<filename>.parquet.gz" ではなく、"<filename>.gz.parquet" または "<filename>.snappy.parquet" であることを確認します。 
 
### <a name="partitioning"></a>パーティション分割
 
"フォルダー" パーティションを使用してデータを整理し、クエリで無関係なパスをスキップできるようにします。 パーティション分割を計画するときは、クエリ内でタイムスタンプやテナント ID などの、ファイル サイズおよび一般的なフィルターを検討してください。
 
### <a name="vm-size"></a>VM サイズ
 
よりコア数が多く、ネットワーク スループットが高い VM SKU を選択します (メモリはあまり重要ではありません)。 詳細については、[Azure Data Explorer クラスターに適した VM SKU を選択する](manage-cluster-choose-sku.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行します。 [クエリを記述](write-queries.md)して、データから追加の分析情報を得る方法について学習します。

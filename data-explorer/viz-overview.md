---
title: Azure Data Explorer でのデータの視覚化
description: Azure Data Explorer のデータを視覚化するさまざまな方法について説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b1351ceb9fe4b81a818ca41728a588dddfb4c5a2
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294680"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure Data Explorer でのデータの視覚化 

Azure Data Explorer は、膨大な量のデータの複雑な分析ソリューションを構築するために使用される、ログおよびテレメトリ データ用の高速でスケーラブルなデータ探索サービスです。 Azure Data Explorer にはさまざまな視覚化ツールが統合されているため、データを視覚化して、組織全体で結果を共有することができます。 このデータを、ビジネスに影響を与える実用的な分析情報に変換できます。

データの視覚化とレポートは、データ分析プロセスの重要な手順です。 Azure Data Explorer では多数の BI サービスがサポートされているため、シナリオや予算に最適なものを使用することができます。

## <a name="azure-data-explorer-dashboards"></a>Azure Data Explorer のダッシュボード

Azure Data Explorer のダッシュボードは、スタンドアロンの Web アプリケーションである [Web UI](web-query-data.md) でクエリを実行し、ダッシュボードを構築できるようにする Web アプリケーションです。 Azure Data Explorer のダッシュボードには、次の 3 つの主な利点があります。

* Web UI から Azure Data Explorer ダッシュボードにクエリをネイティブでエクスポートできます。 
* Web UI でデータを探索できます。
* ダッシュボードのレンダリング パフォーマンスが最適化されています。

詳細については、「[Azure Data Explorer ダッシュボードを使用してデータを視覚化する](azure-data-explorer-dashboards.md)」を参照してください。

## <a name="kusto-query-language-visualizations"></a>Kusto クエリ言語の視覚化

Kusto クエリ言語 [`render operator`](kusto/query/renderoperator.md) には、テーブル、円グラフ、棒グラフなど、クエリ結果を描写するさまざまな視覚化が用意されています。 クエリの視覚化は、異常検出、予測、機械学習などで役立ちます。

## <a name="power-bi"></a>Power BI

Azure Data Explorer には、さまざまな方法を使用して [Power BI](https://powerbi.microsoft.com) に接続する機能があります。 

  * [組み込みのネイティブ Power BI コネクタ](power-bi-connector.md)

  * [Azure Data Explorer から Power BI へのクエリのインポート](power-bi-imported-query.md)
 
  * [SQL query](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer には、[組み込みのネイティブ Excel コネクタ](excel-connector.md)を使用して [Microsoft Excel](https://products.office.com/excel) に接続したり、Azure Data Explorer から Excel に[クエリをインポート](excel-blank-query.md)したりする機能があります。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) には、Azure Data Explorer のデータを視覚化できる Azure Data Explorer プラグインがあります。 [Grafana のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](grafana.md)。 

## <a name="kibana"></a>Kibana

Azure Data Explorer は、オープン ソース コネクタである K2Bridge を使用して [Kibana](https://www.elastic.co/guide/en/kibana/6.8/discover.html) に接続する機能を提供します。 [Kibana のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](k2bridge.md)。

## <a name="odbc-connector"></a>ODBC コネクタ

Azure Data Explorer には [Open Database Connectivity (ODBC) コネクタ](connect-odbc.md)が用意されているため、ODBC をサポートするすべてのアプリケーションが Azure Data Explorer に接続できます。

## <a name="tableau"></a>Tableau

Azure Data Explorer には、[ODBC コネクタ](connect-odbc.md)を使用して [Tableau](https://www.tableau.com) に接続し、[Tableau でデータを視覚化する](tableau.md)機能があります。

## <a name="qlik"></a>Qlik

Azure Data Explorer には、[ODBC コネクタ](connect-odbc.md)を使用して [Qlik](https://www.qlik.com) に接続し、Qlik Sense ダッシュボードを作成して、データを視覚化する機能があります。 次の動画を使用すると、Azure Data Explorer のデータを Qlik で視覚化する方法を学習できます。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>SiSense

Azure Data Explorer には、JDBC コネクタを使用して [Sisense](https://www.sisense.com) に接続する機能があります。 [Sisense のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](sisense.md)。

## <a name="redash"></a>Redash

[Redash](https://redash.io/) を使用して、ダッシュボードを作成し、データを視覚化できます。 [Redash のデータ ソースとして Azure Data Explorer を設定し、データを視覚化します](redash.md)。

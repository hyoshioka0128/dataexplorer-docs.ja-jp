---
title: Kusto でのロール ベースの承認 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの Kusto でのロール ベースの承認について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: fe7013e3ee4b842dc2dcb2e251c342897fa4e489
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522629"
---
# <a name="role-based-authorization-in-kusto"></a>Kusto におけるロールベースの承認



**承認**とは、セキュリティ プリンシパルのアクセス許可によるアクションの実行を許可または拒否するプロセスです。
Kusto は **、認証**されたプリンシパルがロール にマップされ、割り当てられたロールに従ってアクセスを取得する**ロール**ベースのアクセス制御モデルを使用します。

**Kusto エンジン**サービスには、次の役割があります。

|Role                       |アクセス許可                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|すべてのデータベース管理者        |任意のデータベースのスコープで「何でも」できます。特定のクラスターレベルのポリシーを表示および変更できます。                                                           |
|データベース管理者             |特定のデータベースのスコープで「何でも」できます。                                                                                                     |
|データベース ユーザー              |データベースのすべてのデータとメタデータを読み取ることができます。さらに、テーブルを作成し (したがって、そのテーブルのテーブル管理になる)、データベース内の関数を作成できます。|
|すべてのデータベース ビューアー       |任意のデータベースのすべてのデータとメタデータを読み取ることができます。                                                                                                              |
|データベース表示者            |特定のデータベースのすべてのデータとメタデータを読み取ることができます。                                                                                                     |
|データベース取り込み者          |データベース内の既存のすべてのテーブルにデータを取り込むことはできますが、データを照会することはできません。                                                                              |
|データベース非制限表示者|[制限されたビューアクセス ポリシー](../restrictedviewaccess-policy.md)が有効になっているデータベース内のすべてのテーブルを照会できます。                                |
|データベース監視者           |データベースとその`.show`子エンティティのコンテキストでコマンドを実行できます。                                                                          |
|機能管理者             |関数の変更、関数の削除、または別のプリンシパルへの管理者権限の付与が可能。                                                                         |
|テーブル管理者                |特定のテーブルのスコープ内で何でも実行することができます。                                                                                                          |
|テーブル取り込み者             |特定のテーブルのスコープ内でデータを取り込むことはできますが、データを照会することはできません。                                                                                  |

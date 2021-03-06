---
title: Kusto Explorer のオプション-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto Explorer オプションについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 5b1bb73778858998f835f1e8718afbfbd6b69443
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108390"
---
# <a name="kusto-explorer-options"></a>Kusto Explorer のオプション

次の表では、[**ツール** > ] [**オプション**] ダイアログボックスで kusto エクスプローラーの動作をカスタマイズするためのオプションについて説明します。

## <a name="general-settings"></a>全般設定

| オプション | 説明 |
|---------|--------------|
| ツールモード | ベータ、アルファ、および試験的なアプリケーションの機能を有効にします。 既定値は none です。|
| 拡張アクセシビリティ | 有効にすると、アプリケーションはユーザー補助ツールによって使用されるユーザー補助イベントを送信します。 有効にすると、パフォーマンスに影響する可能性があります。 既定では、無効になっています。 |
| テレメトリデータの送信を許可する | 有効にすると、アプリケーションは、エラーが発生したとき、またはアプリケーションがクラッシュしたときに、テレメトリデータを送信します。 |
| ウェルカム メッセージ | 有効にすると、アプリケーションによって開始時にウェルカムメッセージが表示されます。 既定では有効になっています。|
| テーマの表示 | アプリケーション UI の配色: 明るいまたはダーク。|
  
## <a name="query-editor"></a>クエリ エディター

| オプション | 説明 |
|---------|--------------|
| クエリの自動保存 | 有効にすると、開いているドキュメントがアプリケーションによって自動的に保存されます。 この設定を変更するには、アプリケーションを再起動して有効にする必要があります。 既定で有効です。|
| 変更を追跡する | 有効にすると、アプリケーションは、他のアプリケーションによってディスク上のクエリスクリプトに加えられた変更を追跡します。 クエリスクリプトが外部で変更された場合は、通知が表示され、再読み込みを求めるメッセージが表示されます。 この設定を変更するには、アプリケーションを再起動する必要があります。 既定で有効です。|
| 編集セッションを使用する | 有効にすると、各アプリケーションプロセスが独自のドキュメントセットを所有します。 既定で無効になっています。|
| フォント サイズ | クエリエディターで使用されるフォントサイズ。|
| コマンドの背景を選択 | 現在選択されているコマンドを強調表示するために使用する背景色。|
| タブをスペースに置き換える | 有効にすると、タブは自動的にスペースに置き換えられます。|
| 関数パラメーターの挿入を無効にする | 有効にすると、クリップボードからの関数パラメーターの挿入が無効になります。|
| クエリパラメーターの挿入を無効にする | 有効にすると、クエリパラメーターの挿入は無効になります。|
| F5 を除くクエリ実行トリガーを無効にする | 有効にすると、F5 キーだけでクエリが実行されます。|
| アプリケーション内にヘルプを表示する | 有効にすると、アプリケーション内にヘルプトピックが表示されます。 無効にすると、ヘルプトピックがブラウザー内で開かれます。|
| クエリパラメーターの長さの制限 | クエリパラメーターとして使用できる文字列の最大長。 この値を128を超える値に設定すると、パフォーマンスの問題が発生する可能性があります。 既定値は64K です。|

## <a name="intellisense"></a>IntelliSense

| オプション | 説明 |
|---------|--------------|
| IntelliSense のバージョン | 使用された IntelliSense エンジンのバージョン。 *V1*は従来のエンジンです。 *V2*は最新のエンジンです。 既定値は*V2*です。 |
| IntelliSense: コントロールコマンド | コントロールコマンド用の IntelliSense のバージョン。 *V1*は従来のエンジンです。 *V2*は最新のエンジンです。 既定値は*V2*です。 | 
| IntelliSense | IntelliSense を有効または無効にします。 既定では有効になっています。|
| 構文の強調表示 | 構文の強調表示を有効または無効にします。 既定では有効になっています。|
| ツールヒント | 選択したクエリの領域上にマウスポインターを置いたときに表示されるツールヒントを有効または無効にします。 既定では有効になっています。|

## <a name="formatter"></a>フォーマッタ

| オプション | 説明 |
|---------|--------------|
| Version | Debug.log Query ツールが現在のクエリに適用されるときに使用されるフォーマッタのバージョンです。 *V1*は、クラシックフォーマッタです。 *V2*は最新のフォーマッタです。 既定値は*V2*です。|
| [インデント幅] | インデントされた項目のスペースの数。|
| 関数中かっこ | 関数の中かっこの配置。 [*縦*] は、新しい行に左中かっこを配置します。 [*横*] は、始めかっこを元の行に残し、新しい行に終わりかっこを配置します。 *None*すべての中かっこをそのままにします。|
| パイプ演算子 | 表形式クエリ演算子間に存在するパイプ (バー) 文字の配置。 *新しい行*では、すべてのパイプ文字が新しい行に配置されます。 クエリが既に複数の行にわたっている場合、*スマート*はすべてのパイプ文字を新しい行に配置します。 *None*すべてのパイプ文字をそのままにします。|
| セミコロン | クエリステートメントを終了するセミコロンの配置。 *新しい行*では、インデントされた新しい行にセミコロンが挿入されます。 ステートメント自体が複数の行にまたがっている場合は、新しい行にセミコロン*が挿入さ*れます。  *None*は、セミコロンをそのままにします。
| 欠落している構文の挿入 | エラーメッセージを取得しているなど、不足している句読点 (コンマ、セミコロンなど) を追加します。|

## <a name="results-viewer"></a>結果ビューアー

| オプション | 説明 |
|---------|--------------|
| フォント サイズ | データテーブルグリッドで使用されるフォントサイズ。クエリの結果が表示されます。|
| 詳細の配色 | 自動検出された詳細レベルに基づいて、行の書式設定の配色を選択します。|
| 空の列を非表示にする | 有効にすると、ビュー内の空の列にデータが表示されなくなります。  既定では、無効になっています。|
| 1つの値の列を折りたたむ| 有効にすると、データを表示するビューの単一値の列が自動的に折りたたまれます。 空でない単一の値を持つ列は、グループとして表示されます。 既定では、無効になっています。|
| Datetime 列による結果の自動並べ替え | 有効にすると、datetime 列によって行が自動並べ替えられます。 既定では有効になっています。|
| 列に表示される範囲 | 列に表示される最大文字数。 既定値は2048です。|
| JSON をテキストとして視覚化 | 有効にすると、JSON 値はテキストとして視覚化されます。 既定では、無効になっています。|
| テキストの詳細の最大サイズ | セルがダブルクリックされたときに詳細情報パネルに表示される最大文字数。 既定値は 32 KB です。|

## <a name="connections"></a>接続

| オプション | 説明 |
|---------|--------------|
| テーブル列をアルファベット順に並べ替え | 有効にすると、[接続] パネルの [テーブル] ノードの下に表示される列がアルファベット順に並べ替えられます。|
| クエリサーバーのタイムアウト | クエリ実行のサーバータイムアウト。|
| 接続ロック時に警告する | 有効にすると、接続スイッチが試行されるたびに、アプリケーションによって接続ロックに関する警告が表示されます。 既定では有効になっています。|
| レイジースキーマの探索 | 有効にすると、データベースノードが展開されている場合にのみ、[接続] パネルにデータベーススキーマのフェッチと表示が行われます。|
| 非表示システムオブジェクトの表示 | 有効にすると、ユーザーが適切なアクセス許可を持っている場合、非表示のシステムオブジェクトが表示されます。|
| クエリの弱い整合性 | 有効にすると、クエリに弱い一貫性が使用されます。|
| Kusto パーサーのバージョン | クエリの実行に使用されるパーサーのバージョン。 *V1*は、クラシックパーサーです。 *V2*は最新のパーサーです。 既定値は*V1*です。|

## <a name="diagnostic-tracing"></a>診断トレース

| オプション | 説明 |
|---------|--------------|
| トレースの有効化 | トレースを有効にします。|
| トレースの詳細度 | トレースの詳細度を設定します。|
| トレースの場所 | トレースがログに記録される場所。|
| PlatformTraceVerbosity | プラットフォームのトレースの詳細度を設定します。| 
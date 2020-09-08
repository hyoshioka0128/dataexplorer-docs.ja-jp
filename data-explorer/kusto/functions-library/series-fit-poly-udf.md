---
title: series_fit_poly_udf ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_poly_udf () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 8855b711ad2243a7483e94406ca84ec09a18e5e3
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557960"
---
# <a name="series_fit_poly_udf"></a>series_fit_poly_udf ()


関数は、 `series_fit_poly_udf()` 系列に多項式回帰を適用します。 複数の系列 (動的な数値配列) を含むテーブルを取得し、各系列に対して、 [多項式回帰](https://en.wikipedia.org/wiki/Polynomial_regression)を使用して最も適した上位の多項式を生成します。 この関数は、系列の範囲に対する多項式係数と補間多項式の両方を返します。

> [!NOTE]
>* この関数にはインライン Python が含まれており、クラスターで [python () プラグインを有効にする](../query/pythonplugin.md#enable-the-plugin) 必要があります。
>* この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。
>* 系列 [演算子](../query/make-seriesoperator.md)によって作成された等間隔の系列の線形回帰の場合は、ネイティブ関数 [series_fit_line ()](../query/series-fit-linefunction.md)を使用します。

## <a name="syntax"></a>構文

`T | invoke series_fit_poly_udf(`*y_series* `,`*y_fit_series* `,`*fit_coeff* `,`*度* `, [`*x_series* `,`*x_istime*]`)`
  
## <a name="arguments"></a>引数

* *y_series*: [依存変数](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)を含む入力テーブル列の名前。 これは、適合する系列です。
* *y_fit_series*: 最適な系列を格納する列の名前。
* *fit_coeff*: 最適な多項式係数を格納する列の名前。
* *次数*: 必要な多項式の順序。 たとえば、線形回帰の場合は1、2次回帰の場合は2になります。
* *x_series*: [独立変数](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)、つまり x または時間軸を含む列の名前。 このパラメーターは省略可能であり、均等の [時系列](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)に対してのみ必要です。 既定値は空の文字列です。 x は、均等に並んだ系列の回帰に対して冗長です。
* *x_istime*: このブール型パラメーターは省略可能です。 このパラメーターは、 *x_series* が指定されていて、それが datetime のベクターである場合にのみ必要です。

## <a name="usage"></a>使用法

* `series_fit_poly_udf()` はユーザー定義関数です。 クエリにコードを埋め込むか、データベースにインストールすることができます。
    * アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。
    * 繰り返し使用する場合は、 [. create function](../management/create-function.md)を使用して永続化します。 <br>
        関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
* `series_fit_poly_udf()` は [表形式関数](../query/functions/user-defined-functions.md#tabular-function)で、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。

# <a name="ad-hoc-usage"></a>[アドホック使用](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_udf=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_udf('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent-usage"></a>[永続的な使用](#tab/persistent)

* **1回限りのインストール**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_udf(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

* **使用方法**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_udf('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-udf/usage-example.png" alt-text="標準の時系列に5番目の注文多項式が表示されるグラフ" border="false":::

## <a name="additional-examples"></a>追加の例

次の例は、関数が既にインストールされていることを前提としています。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Test irregular (unevenly spaced) time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| where TimeStamp between ((max_t-2d)..max_t)
| summarize num=count() by bin(TimeStamp, 5m), OsVer
| order by TimeStamp asc
| where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
| summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null)
| invoke series_fit_poly_udf('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
| render timechart with(ycolumns=num, fnum)
```

:::image type="content" source="images/series-fit-poly-udf/irregular-time-series.png" alt-text="8番目の注文多項式が不規則な時系列に適合することを示すグラフ" border="false":::

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// 5th order polynomial with noise on x & y axes
//
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| order by x asc 
| summarize x=make_list(x), y=make_list(y)
| extend y_fit = dynamic(null), coeff=dynamic(null)
| invoke series_fit_poly_udf('y', 'y_fit', 'coeff', 5, 'x')
|fork (project-away coeff) (project coeff | mv-expand coeff)
| render linechart
```

:::image type="content" source="images/series-fit-poly-udf/fifth-order-noise.png" alt-text="X & y 軸でのノイズによる5番目の注文多項式のフィットグラフ" border="false":::
:::image type="content" source="images/series-fit-poly-udf/fifth-order-noise-table.png" alt-text="ノイズによる5番目の注文多項式の適合性の係数" border="false":::

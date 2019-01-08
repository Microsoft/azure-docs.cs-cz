---
title: Analýza dat pomocí poznámkového bloku Jupyter a Kqlmagic
description: V tomto tématu ukazují, jak analyzovat data pomocí poznámkového bloku Jupyter a KQLmagic
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 179cc133e755a317c70b84acc95aafc61f4e0e68
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078163"
---
# <a name="analyze-data-using-jupyter-notebook-and-kqlmagic"></a>Analýza dat pomocí poznámkového bloku Jupyter a Kqlmagic

Poznámkový blok Jupyter je open source webová aplikace, která umožňuje vytvářet a sdílet dokumenty, které obsahují živého kódu, rovnice, vizualizace a vyprávěného textu. Použití zahrnuje čištění dat a transformace, číselné simulace, statistické modelování, vizualizace dat a strojové učení.
[Poznámkový blok Jupyter](https://jupyter.org/) podporuje magic funkce, které rozšiřují možnosti jádra díky podpoře dalších příkazů. Kqlmagic je příkaz, který rozšiřuje možnosti jádra Python do poznámkového bloku Jupyter, abyste mohli spustit dotazy v jazyce Kusto nativně. Můžete snadno kombinovat Python a Kusto dotazovací jazyk pro dotazování a vizualizace dat s využitím bohaté integrovaná knihovna Plot.ly `render` příkazy. Zdroje dat pro spouštění dotazů nejsou podporovány. Tyto zdroje dat patří Průzkumník dat Azure, zkoumání rychlá a vysoce škálovatelnými datové služby pro data protokolů a telemetrická data, i pro Log Analytics a Application Insights. Kqlmagic funguje taky s příponou poznámkových bloků Azure Jupyter Lab a Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Požadavky

- Organizační e-mailový účet, který je členem z Azure Active Directory (AAD).
- Poznámkový blok Jupyter nainstalované na místním počítači nebo použití poznámkových bloků Azure a vytvoření klonu ukázky [poznámkového bloku v Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kqlmagic-library"></a>Nainstalujte knihovnu Kqlmagic

1. Nainstalujte Kqlmagic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Při použití poznámkových bloků Azure, tento krok není povinný.

1. Načte Kqlmagic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Připojte se ke clusteru pomáhají Průzkumník dat Azure

Použijte následující příkaz pro připojení k *ukázky* hostitelem databáze *pomáhají* clusteru. Microsoft AAD uživatele, nahraďte název tenanta `Microsoft.com` s vašeho Tenanta AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Dotazování a vizualizace

Dotazování dat pomocí [vykreslení operátor](/azure/kusto/query/renderoperator) a vizualizace dat pomocí knihovny ploy.ly. Tento dotaz a vizualizací poskytuje integrované prostředí, který používá nativní KQL. Kqlmagic podporuje většinu grafy s výjimkou `timepivot`, `pivotchart`, a `ladderchart`. Podporuje vykreslení všech atributů s výjimkou `kind`, `ysplit`, a `accumulate`. 

### <a name="query-and-render-piechart"></a>Dotaz a vykreslování piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Dotaz a vykreslování promítnu

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Tyto grafy jsou interaktivní. Vyberte časový rozsah pro zvětšení určitý čas.

### <a name="customize-the-chart-colors"></a>Přizpůsobení barev grafu

Pokud se vám výchozí palety barev, upravit grafy, pomocí možnosti palety. K dispozici palety najdete tady: [Zvolte paletu barev pro výsledek Kqlmagic dotazu grafu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Seznam palety:

    ```python
    %kql --palettes -popup_window
    ```

1. Vyberte `cool` paleta barev a vykreslit dotaz znovu:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrizovat dotazování pomocí Pythonu

Kqlmagic umožňuje jednoduchou výměny mezi Kusto dotazovací jazyk a Python. Další informace: [Parametrizace dotazu Kqlmagic pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Použití Pythonu proměnné v dotazu KQL

Hodnota proměnné Pythonu můžete použít v dotazu k filtrování dat:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Převést na Pandas DataFrame výsledků dotazu

Výsledky dotazu KQL v Pandas DataFrame můžete přistupovat. Přístup k poslední výsledky dotazu provedeného proměnnou `_kql_raw_result_` a snadno převést výsledky Pandas DataFrame následujícím způsobem:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Příklad:

V mnoha scénářích analytics můžete vytvářet opakovaně použitelné poznámkových bloků, které obsahují mnoho dotazů a vrátit výsledky z jednoho dotazu do dalších dotazů. Následující příklad používá proměnnou Python `statefilter` filtrovat data.

1. Spusťte dotaz, chcete-li zobrazit prvních 10 států s maximální `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Spuštění dotazu extrahovat nejvyšší stav a nastavte ji do proměnné Pythonu:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Spuštění dotazů `let` příkazu a proměnná Pythonu:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Spusťte příkaz help:

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Další postup

Spusťte příkaz help prozkoumávat následující ukázkové poznámkové bloky, které obsahují všechny podporované funkce:
- [Začínáme s Kqlmagic pro Průzkumník dat Azure](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Začínáme s Kqlmagic pro Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Začínáme s Kqlmagic pro Log Analytics](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize Kqlmagic dotazu pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Zvolte paletu barev pro výsledek Kqlmagic dotazu grafu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
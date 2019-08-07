---
title: Použití Jupyter Notebook k analýze dat v Azure Průzkumník dat
description: V tomto tématu se dozvíte, jak analyzovat data v Azure Průzkumník dat pomocí Jupyter Notebook a rozšíření Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 312e39ff1b699bb3c7f2baea3c66cbf8999ee44b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814514"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Použití rozšíření Jupyter Notebook a Kqlmagic k analýze dat v Azure Průzkumník dat

Jupyter Notebook je open-source webová aplikace, která umožňuje vytvářet a sdílet dokumenty obsahující živý kód, rovnice, vizualizace a mluvený text. Použití zahrnuje čištění a transformaci dat, číselnou simulaci, statistické modelování, vizualizaci dat a strojové učení.
[Jupyter notebook](https://jupyter.org/) podporuje funkci Magic, která rozšiřuje možnosti jádra tím, že podporuje další příkazy. KQL Magic je příkaz, který rozšiřuje možnosti jádra Pythonu v Jupyter Notebook, takže můžete spouštět dotazy jazyka Kusto nativně. Můžete snadno kombinovat Python a Kusto dotazovací jazyk a dotazovat se na data a vizualizovat je pomocí bohatých knihoven `render` plot.ly integrovaných s příkazy. Zdroje dat pro běžící dotazy jsou podporovány. Tyto zdroje dat zahrnují Azure Průzkumník dat, rychlou a vysoce škálovatelnou službu průzkumu dat pro data protokolů a telemetrie a také protokoly Azure Monitor a Application Insights. KQL Magic také spolupracuje s rozšířeními Azure Notebooks, Jupyter Lab a Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Požadavky

- E-mailový účet organizace, který je členem Azure Active Directory (AAD).
- Jupyter Notebook nainstalované na místním počítači, nebo použijte Azure Notebooks a naklonujte ukázkový [notebook Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb) .

## <a name="install-kql-magic-library"></a>Nainstalovat KQL Magic Library

1. Nainstalovat KQL Magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Při použití Azure Notebooks se tento krok nevyžaduje.

1. Load KQL Magic:

    ```python
    %reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Připojení ke clusteru Azure Průzkumník dat Help

Pomocí následujícího příkazu se připojte k databázi *ukázek* , která je hostovaná v clusteru *help* . Pro uživatele AAD, kteří nejsou od Microsoftu, nahraďte `Microsoft.com` název tenanta vaším klientem AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Dotazování a vizualizace

Dotazování dat pomocí [operátoru vykreslení](/azure/kusto/query/renderoperator) a vizualizace dat pomocí knihovny ploy.ly Tento dotaz a vizualizace poskytuje integrované prostředí, které používá nativní KQL. Kqlmagic podporuje většinu grafů s `timepivot`výjimkou, `ladderchart` `pivotchart`a. Vykreslování je podporováno u všech atributů s `kind`výjimkou, `accumulate` `ysplit`a. 

### <a name="query-and-render-piechart"></a>Dotazování a vykreslování piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Dotazování a vykreslování timechart

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Tyto grafy jsou interaktivní. Vyberte časový rozsah, který se má zvětšit na určitý čas.

### <a name="customize-the-chart-colors"></a>Přizpůsobení barev grafu

Pokud se vám nelíbí výchozí barevná paleta, upravte grafy pomocí možností palety. Dostupné palety najdete tady: [Zvolit paletu barev pro výsledek grafu dotazu KQL Magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Seznam palet:

    ```python
    %kql --palettes -popup_window
    ```

1. Vyberte paletu `cool` barev a znovu vykreslete dotaz:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrizovat dotaz pomocí Pythonu

KQL Magic umožňuje jednoduché výměny mezi dotazovacím jazykem Kusto a Pythonem. Další informace: [Parametrizovat dotaz KQL Magic pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Použití proměnné Pythonu v dotazu KQL

Pomocí hodnoty proměnné jazyka Python v dotazu můžete filtrovat data:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Převod výsledků dotazu na PANDAS dataframe

K výsledkům dotazu KQL můžete získat přístup v dataframe PANDAS. Získejte přístup k posledním spuštěným výsledkům `_kql_raw_result_` dotazu podle proměnné a jednoduše převeďte výsledky na PANDAS dataframe následujícím způsobem:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Příklad

V mnoha scénářích analýz můžete chtít vytvořit opakovaně použitelné poznámkové bloky, které obsahují mnoho dotazů, a podávat výsledky z jednoho dotazu do dalších dotazů. Následující příklad používá proměnnou `statefilter` jazyka Python k filtrování dat.

1. Spusťte dotaz pro zobrazení prvních 10 stavů s maximálním `DamageProperty`limitem:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Spusťte dotaz pro extrakci nejvyššího stavu a nastavte jej na proměnnou Pythonu:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Spusťte dotaz pomocí `let` příkazu a proměnné jazyka Python:

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

> [!TIP]
> Pro získání informací o všech dostupných konfiguracích `%config KQLmagic`. K řešení potíží a zaznamenání chyb Kusto, jako jsou problémy s připojením a nesprávné dotazy, použijte`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Další kroky

Spuštěním příkazu help můžete prozkoumat následující ukázkové poznámkové bloky, které obsahují všechny podporované funkce:
- [Začínáme s KQL Magic pro Azure Průzkumník dat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Začínáme s KQL Magic pro Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Začínáme s KQL Magic pro protokoly Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize dotazu KQL Magic pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Zvolit paletu barev pro výsledek grafu dotazu KQL Magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

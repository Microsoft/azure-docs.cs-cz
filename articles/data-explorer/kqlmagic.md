---
title: Analýza dat v Azure Data Exploreru pomocí poznámkového bloku Jupyter
description: Toto téma ukazuje, jak analyzovat data v Průzkumníku dat Azure pomocí poznámkového bloku Jupyter a rozšíření Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064865"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Použití poznámkového bloku Jupyter a rozšíření Kqlmagic k analýze dat v Průzkumníku dat Azure

Jupyter Notebook je open-source webová aplikace, která vám umožní vytvářet a sdílet dokumenty obsahující živý kód, rovnice, vizualizace a narativní text. Použití zahrnuje čištění a transformaci dat, numerickou simulaci, statistické modelování, vizualizaci dat a strojové učení.
[Jupyter Notebook](https://jupyter.org/) podporuje magické funkce, které rozšiřují možnosti jádra podporou dalších příkazů. KQL magic je příkaz, který rozšiřuje možnosti jádra Pythonu v Jupyter Notebooku, takže můžete nativně spouštět dotazy jazyka Kusto. Můžete snadno kombinovat dotazovací jazyk Pythonu a Kusto pro dotazování `render` a vizualizaci dat pomocí bohaté Plot.ly knihovny integrované s příkazy. Zdroje dat pro spouštění dotazů jsou podporovány. Tyto zdroje dat zahrnují Azure Data Explorer, rychlé a vysoce škálovatelné služby pro zkoumání dat pro log a telemetrická data, stejně jako protokoly Azure Monitor a Application Insights. KQL magie funguje také s Azure Notebooky, Jupyter Lab a Visual Studio Code Jupyter rozšíření.

## <a name="prerequisites"></a>Požadavky

- Organizační e-mailový účet, který je členem Služby Azure Active Directory (AAD).
- Jupyter notebook nainstalovaný ve vašem místním počítači nebo použijte poznámkové bloky Azure a naklonujte ukázkový [poznámkový blok Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Instalace magické knihovny KQL

1. Nainstalujte KQL magie:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Při použití poznámkových bloků Azure tento krok není povinný.

1. Zatížení KQL magie:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Změňte verzi jádra na Python 3.6 kliknutím na Kernel > Change Kernel > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Připojení ke clusteru nápovědy Průzkumníka dat Azure

Pomocí následujícího příkazu se připojte k databázi *ukázky* hostované v clusteru *nápovědy.* Pro uživatele add jinénež Microsoft `Microsoft.com` nahraďte název klienta klienta klienta AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Dotaz a vizualizace

Dotazujte se na data pomocí [operátoru vykreslení](/azure/kusto/query/renderoperator) a vizualizovat data pomocí knihovny ploy.ly. Tento dotaz a vizualizace poskytuje integrované prostředí, které používá nativní KQL. Kqlmagic podporuje většinu `timepivot` `pivotchart`grafů `ladderchart`s výjimkou , a . Vykreslení je podporováno `kind`všemi atributy s výjimkou , `ysplit`a `accumulate`. 

### <a name="query-and-render-piechart"></a>Dotaz a vykreslení se koláčem

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Dotaz a vykreslení časového diagramu

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Tyto grafy jsou interaktivní. Vyberte časový rozsah, který chcete přiblížit na určitý čas.

### <a name="customize-the-chart-colors"></a>Přizpůsobení barev grafu

Pokud se vám nelíbí výchozí paleta barev, přizpůsobte grafy pomocí možností palety. Dostupné palety naleznete zde: [Zvolte paletu barev pro výsledek grafu magických dotazů KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Seznam palet:

    ```python
    %kql --palettes -popup_window
    ```

1. Vyberte `cool` paletu barev a vykreslete dotaz znovu:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrize dotazu pomocí Pythonu

KQL magie umožňuje jednoduchou výměnu mezi dotazovacím jazykem Kusto a Pythonem. Další informace: [Parametrizujte magický dotaz KQL pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Použití proměnné Pythonu v dotazu KQL

Hodnotu proměnné Pythonu v dotazu můžete použít k filtrování dat:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Převést výsledky dotazu na datový rámec pand

K výsledkům dotazu KQL můžete přistupovat v datovém rámečku Pandy. Získejte přístup k výsledkům `_kql_raw_result_` posledního provedeného dotazu podle proměnné a snadno převeďte výsledky na pandas DataFrame následujícím způsobem:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Příklad

V mnoha scénářích analýzy můžete chtít vytvořit opakovaně použitelné poznámkové bloky, které obsahují mnoho dotazů a zdroje výsledků z jednoho dotazu do následujících dotazů. Následující příklad používá k `statefilter` filtrování dat proměnnou Pythonu.

1. Spuštěnídotazu zobrazíprvních 10 stavů `DamageProperty`s maximem :

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Spuštění dotazu pro extrahování nejvyššího stavu a jeho nastavení do proměnné Pythonu:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Spusťte dotaz `let` pomocí příkazu a proměnné Pythonu:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Spusťte příkaz nápovědy:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Chcete-li získat informace o `%config Kqlmagic`všech dostupných konfiguracích, použijte . Chcete-li odstranit a zachytit chyby kusto, jako jsou problémy s připojením a nesprávné dotazy, použijte`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Další kroky

Spuštěním příkazu nápovědy prozkoumejte následující ukázkové poznámkové bloky, které obsahují všechny podporované funkce:
- [Začínáme s kql magií pro Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Začínáme s kql magií pro application insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Začínáme s protokoly KQL magic pro Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize magického dotazu KQL pomocí Pythonu](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Výběr palety barev pro výsledek grafu magických dotazů KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

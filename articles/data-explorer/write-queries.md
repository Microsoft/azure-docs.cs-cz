---
title: Zápis dotazů pro Azure Průzkumník dat
description: V tomto postupu se naučíte, jak provést základní a pokročilejší dotazy pro Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881181"
---
# <a name="write-queries-for-azure-data-explorer"></a>Zápis dotazů pro Azure Průzkumník dat

V tomto článku se dozvíte, jak používat dotazovací jazyk v Azure Průzkumník dat k provádění základních dotazů s nejběžnějšími operátory. Také se dostanete k některým pokročilejším funkcím tohoto jazyka.

## <a name="prerequisites"></a>Požadavky

Dotazy v tomto článku můžete spustit jedním ze dvou způsobů:

- V clusteru Azure Průzkumník dat *pomoci* , který jsme nastavili na podporu učení.
    Přihlaste se [ke clusteru](https://dataexplorer.azure.com/clusters/help/databases/samples) pomocí e-mailového účtu organizace, který je členem Azure Active Directory.

- Ve vlastním clusteru, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [rychlý Start: Vytvořte cluster Azure Průzkumník dat a databázi](create-cluster-database-portal.md) a ingestujte ukázková [data do Azure Průzkumník dat](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Přehled dotazovacího jazyka

Dotazovací jazyk v Azure Průzkumník dat je žádost jen pro čtení ke zpracování dat a vrácení výsledků. Požadavek je uveden v prostém textu pomocí modelu toku dat, který je navržen pro usnadnění čtení, vytváření a automatizaci syntaxe. Dotaz používá entity schématu, které jsou uspořádány v hierarchii podobně jako SQL: databáze, tabulky a sloupce.

Dotaz se skládá z sekvence příkazů dotazu, oddělený středníkem (`;`), s alespoň jedním příkazem, který je tabulkovým výrazem, který je příkaz, který vytváří data uspořádaná do mřížky jako tabulka sloupců a řádků. Příkazy tabulkového výrazu dotazu vydávají výsledky dotazu.

Syntaxe příkazu tabulkového výrazu má tabulkový tok dat z jednoho operátoru dotazu na jiný, počínaje zdrojem dat (například tabulka v databázi nebo operátor, který vytváří data) a následně přetéká pomocí sady transformace dat. operátory, které jsou svázané prostřednictvím použití oddělovače kanálu (`|`).

Například následující dotaz má jediný příkaz, který je tabulkový výraz příkazu. Příkaz začíná odkazem na tabulku s názvem `StormEvents` (databáze, kterou hostuje Tato tabulka, je tady implicitní a část informací o připojení). Data (řádky) pro tuto tabulku se pak filtrují podle hodnoty `StartTime` sloupce a pak se filtrují podle hodnoty `State` sloupce. Dotaz pak vrátí počet "zbývajících" řádků.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

V tomto případě je výsledkem:

|Count|
|-----|
|   23|
| |

Další informace najdete v referenčních informacích k [dotazovacímu jazyku](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Nejběžnější operátory

Operátory uvedené v této části jsou stavebními bloky pro porozumění dotazům v Azure Průzkumník dat. Většina dotazů, které zapisujete, bude obsahovat několik z těchto operátorů.

Spuštění dotazů v clusteru Help: vyberte **Kliknutím spustíte dotaz** nad každým dotazem.

Spuštění dotazů na vlastním clusteru:

1. Zkopírujte každý dotaz do webové aplikace dotazů a pak buď vyberte dotaz, nebo umístěte kurzor do dotazu.

1. V horní části aplikace vyberte **Spustit**.

### <a name="count"></a>count

[**počet**](https://docs.microsoft.com/azure/kusto/query/countoperator): Vrátí počet řádků v tabulce.

Následující dotaz vrátí počet řádků v tabulce StormEvents.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>nezbytná

[**Vezměte v úvahu**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Vrátí až zadaný počet řádků dat.

Následující dotaz vrátí pět řádků z tabulky StormEvents. *Omezení* klíčového slova je alias pro *přijetí.*

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Není nijak zaručeno, které záznamy se vrátí, pokud zdrojová data nejsou seřazená.

### <a name="project"></a>project

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Vybere podmnožinu sloupců.

Následující dotaz vrátí konkrétní sadu sloupců.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>kde

[**kde**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtruje tabulku na podmnožinu řádků, které odpovídají predikátu.

Následující dotaz filtruje data podle `EventType` a. `State`

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>druhu

[**Seřadit**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Seřaďte řádky vstupní tabulky podle jednoho nebo více sloupců podle pořadí.

Následující dotaz seřadí data v sestupném pořadí `DamageProperty`podle.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Pořadí operací je důležité. Zkuste vložit `take 5` před `sort by`. Získáte různé výsledky?

### <a name="top"></a>vrchol

[**horní**](https://docs.microsoft.com/azure/kusto/query/topoperator): Vrátí prvních *N* záznamů seřazených podle zadaných sloupců.

Následující dotaz vrátí stejné výsledky jako u jednoho operátoru s menším množstvím.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>zvětšení

[**rozšiřuje**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Vypočítá odvozené sloupce.

Následující dotaz vytvoří nový sloupec pomocí výpočtu hodnoty v každém řádku.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Výrazy mohou zahrnovat všechny obvyklé operátory (+,-, *,/,%) a existuje rozsah užitečných funkcí, které můžete volat.

### <a name="summarize"></a>Souhrn

[**Souhrn**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agreguje skupiny řádků.

Následující dotaz vrátí počet událostí podle `State`.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Operátor **sumarizace** seskupí řádky, které mají stejné hodnoty v klauzuli **podle** , a pak pomocí agregační funkce (například **Count**) kombinuje každou skupinu do jednoho řádku. V tomto případě je v tomto případě řádek pro každý stav a sloupec pro počet řádků v tomto stavu.

Existuje řada agregačních funkcí a můžete použít několik z nich v jednom operátoru **Shrnutí** k vytvoření několika počítaných sloupců. Můžete například získat počet nekonzistencí v jednotlivých stavech a jedinečné množství nekonzistencí pro každý stav a potom použít **horní** seznam a získat tak nejvíc ovlivněných stavů.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Výsledkem operace sumarizace je:

- Každý sloupec s názvem v

- Sloupec pro každý počítaný výraz

- Řádek pro každou kombinaci podle hodnot

### <a name="render"></a>činit

[**vykreslení**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Vykresluje výsledky jako grafický výstup.

Následující dotaz zobrazí sloupcový graf.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Následující dotaz zobrazí jednoduchý časový graf.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Následující dotaz počítá události podle času modulo za jeden den, rozdělený v hodinách a zobrazuje graf času.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Následující dotaz porovnává více denních řad v časovém grafu.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Operátor **vykreslování** je funkce na straně klienta, nikoli součást modulu. Je integrována do jazyka pro snadné použití. Webová aplikace podporuje následující možnosti: BarChart, columnchart, piechart, timechart a linechart. 

## <a name="scalar-operators"></a>Skalární operátory

Tato část se zabývá některými z nejdůležitějších skalárních operátorů.

### <a name="bin"></a>bin ()

[**bin ()** ](https://docs.microsoft.com/azure/kusto/query/binfunction): Zaokrouhlí hodnoty dolů na celočíselnou hodnotu násobku dané velikosti přihrádky.

Následující dotaz vypočítá počet s velikostí intervalu jednoho dne.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>případ ()

[**case()** ](https://docs.microsoft.com/azure/kusto/query/casefunction): Vyhodnotí seznam predikátů a vrátí první výsledek výrazu, jehož predikát je splněn, nebo konečný výraz **Else** . Tento operátor můžete použít ke kategorizaci nebo seskupení dat:

Následující dotaz vrátí nový sloupec `deaths_bucket` a seskupí zaniklelné číslo.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()** ](https://docs.microsoft.com/azure/kusto/query/extractfunction): Načte shodu regulárního výrazu z textového řetězce.

Následující dotaz extrahuje konkrétní hodnoty atributů z trasování.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Tento dotaz používá příkaz **let** , který váže název (v tomto případě `MyData`) k výrazu. Pro zbytek oboru, ve kterém se zobrazuje příkaz **let** (globální rozsah nebo v rozsahu těla funkce), lze název použít k odkazování na jeho vázanou hodnotu.

### <a name="parse_json"></a>parse_json()

[**parse_json()** ](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpretuje řetězec jako hodnotu JSON a vrátí hodnotu jako dynamickou. Použití funkce **extractjson ()** je nadřazené, pokud potřebujete extrahovat více než jeden prvek složeného objektu JSON.

Následující dotaz extrahuje prvky JSON z pole.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Následující dotaz extrahuje prvky JSON.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Následující dotaz extrahuje prvky JSON s dynamickým datovým typem.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()** ](https://docs.microsoft.com/azure/kusto/query/agofunction): Odečte zadané časové rozmezí od aktuálních časových hodin UTC.

Následující dotaz vrátí data za posledních 12 hodin.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Vrátí začátek týdne obsahujícího datum posunutý posunem, pokud je k dispozici.

Následující dotaz vrátí začátek týdne s různými posuny.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Tento dotaz používá operátor **rozsahu** , který generuje tabulku hodnot s jedním sloupcem. Viz také: [**startofday ()** ](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek ()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**STARTOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction), [**STARTOFMONTH ()** ](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday ()** ](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek (** ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction)), [**ENDOFMONTH ()** ](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)a [**ENDOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between ()

[**Between ()** ](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Odpovídá zadanému vstupu uvnitř rozsahu.

Následující dotaz filtruje data podle zadaného rozsahu kalendářních dat.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Následující dotaz filtruje data podle zadaného rozsahu kalendářních dat s mírnou variací tři dny (`3d`) od počátečního data.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabulkové operátory

Kusto má mnoho tabelárních operátorů, z nichž některé jsou pokryté v dalších částech tohoto článku. Tady se zaměříme na **analýzu**. 

### <a name="parse"></a>Analýza

[**analyzovat**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Vyhodnotí řetězcový výraz a analyzuje jeho hodnotu do jednoho nebo více počítaných sloupců. Existují tři způsoby, jak je analyzovat: jednoduché (výchozí), Regex a odlehčené.

Následující dotaz analyzuje trasování a extrahuje příslušné hodnoty pomocí výchozí jednoduché analýzy. Výraz (označovaný jako StringConstant) je regulární hodnota řetězce a shoda je striktní: rozšířené sloupce musí odpovídat požadovaným typům.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Následující dotaz analyzuje trasování a extrahuje příslušné hodnoty pomocí `kind = regex`. StringConstant může být regulární výraz.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Následující dotaz analyzuje trasování a extrahuje příslušné hodnoty pomocí `kind = relaxed`. StringConstant je běžná řetězcová hodnota a shoda je odlehčená: rozšířené sloupce mohou částečně odpovídat požadovaným typům.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Analýza časových řad

### <a name="make-series"></a>vytvořit řadu

[**make-Series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): agreguje dohromady skupiny řádků, jako je [](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)například sumarizace, ale generuje vektor řady (Time) pro každou kombinaci hodnot.

Následující dotaz vrátí sadu časových řad pro počet událostí zaplavení za den. Dotaz pokrývá tříměsíční období pro každý stav a vyplní chybějící přihrádky konstantou 0:

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Jakmile vytvoříte sadu (časovou) řadu, můžete použít funkce řady a detekovat tak neobvyklé tvary, sezónní vzory a spoustu dalšího.

Následující dotaz extrahuje horní tři stavy, které měly nejvíc událostí v určitém dni:

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Další informace najdete v úplném seznamu [funkcí řady](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Pokročilé agregace

Dříve v tomto článku jsme pokryli základní agregace, jako je **počet** a **sumarizace**. V této části se seznámíte s pokročilejšími možnostmi.

### <a name="top-nested"></a>horní – vnořený

[**horní – vnořený**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Vytvoří hierarchické výsledky, kde jsou jednotlivé úrovně procházením na základě hodnot předchozí úrovně.

Tento operátor je vhodný pro scénáře vizualizace řídicího panelu nebo v případě, že je potřeba odpovědět na otázku, jako je následující: "Najděte horní-N hodnoty K1 (s použitím některé agregace); pro každý z nich Najděte, jaké jsou hodnoty nejvyššího M K2 (pomocí jiné agregace); ..."

Následující dotaz vrátí hierarchickou tabulku s `State` na nejvyšší úrovni, `Sources`za kterou následuje.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Pivot () – modul plug-in

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Otočí tabulku tak, že z jednoho sloupce ve vstupní tabulce přepíná jedinečné hodnoty na více sloupců ve výstupní tabulce. Operátor provádí agregace, kde jsou požadovány pro všechny zbývající hodnoty sloupce v konečném výstupu.

Následující dotaz aplikuje filtr a Překlopí řádky na sloupce.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()** ](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Vrátí odhad počtu jedinečných hodnot výrazu ve skupině. K počítání všech hodnot použijte [**Count ()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) .

Následující dotaz počítá jednotlivé `Source` `State`položky.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()** ](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Vrátí odhad počtu jedinečných hodnot výrazu pro řádky, pro které je predikát vyhodnocen jako true.

Následující dotaz počítá různé hodnoty `Source` , kde. `DamageProperty < 5000`

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()** ](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Vypočítá hodnotu **DCount** z výsledků HyperLogLog (generovaných [**HLL**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) nebo [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Následující dotaz vygeneruje počet pomocí algoritmu HLL.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()** ](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Najde řádek ve skupině, který maximalizuje výraz, a vrátí hodnotu jiného výrazu (nebo * pro vrácení celého řádku).

Následující dotaz vrátí čas poslední zprávy o zaplavení v každém stavu.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()** ](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Vrátí dynamické pole (JSON) sady různých hodnot, které výraz převezme ve skupině.

Následující dotaz vrátí všechny časy, kdy byl v jednotlivých stavech nahlášen čas, a vytvoří pole ze sady různých hodnot.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>MV – rozbalit

[**MV – rozbalit**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Rozbalí kolekce s více hodnotami z dynamického typu sloupce tak, aby každá hodnota v kolekci měla samostatný řádek. Všechny ostatní sloupce v rozbaleném řádku jsou duplicitní. Je opakem z MakeList.

Následující dotaz generuje ukázková data vytvořením sady a jejím použitím k předvedení možností **rozšíření MV** .

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentily ()

[**percentily ()** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Vrátí odhad pro zadaný [**percentil nejbližšího pořadí**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) populace, který je definován výrazem. Přesnost závisí na hustotě populace v oblasti percentilu. Dá se použít jenom v kontextu agregace v rámci [**sumarizace**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Následující dotaz vypočítá percentily pro dobu trvání.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Následující dotaz vypočítá percentily pro dobu trvání hodnoty násobené stavem a normalizuje data o pět minut přihrádek (`5m`).

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Vzájemná datová sada

Tato část se zabývá prvky, které vám umožní vytvářet složitější dotazy, spojovat data napříč tabulkami a dotazovat se napříč databázemi a clustery.

### <a name="let"></a>aplikaci

[**Povolit**](https://docs.microsoft.com/azure/kusto/query/letstatement): Vylepšuje modularitu a opakované použití. Příkaz **let** umožňuje rozdělit potenciálně složitý výraz na více částí, z nichž každá je svázána s názvem, a vytvořit tyto části dohromady. Příkaz **let** lze použít také k vytvoření uživatelem definovaných funkcí a zobrazení (výrazy nad tabulkami, jejichž výsledky vypadají jako nová tabulka). Výrazy vázané pomocí příkazu **let** mohou být skalárního typu, tabulkového typu nebo uživatelsky definované funkce (výrazy lambda).

Následující příklad vytvoří proměnnou tabelárního typu a použije ji v následném výrazu.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**připojit**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Sloučí řádky dvou tabulek, aby bylo možné vytvořit novou tabulku odpovídajícími hodnotami zadaného sloupce z každé tabulky. Kusto podporuje celou škálu typů spojení: **FullOuter**, **Inner**, **innerunique**, **leftanti**, **leftantisemi**, **LeftOuter**, **leftsemi**, **rightanti**, **rightantisemi**, **RightOuter** , **rightsemi**.

Následující příklad spojí dvě tabulky s vnitřním spojením.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Pomocí operátorů **WHERE** a **projektu** můžete snížit počet řádků a sloupců ve vstupních tabulkách před připojením. Je-li jedna tabulka vždy menší než druhá, použijte ji jako levou (v kanálu) na straně spojení. Sloupce pro shodu spojení musí mít stejný název. Použijte operátor **projektu** , pokud je to nutné k přejmenování sloupce v jedné z tabulek.

### <a name="serialize"></a>serializovat

[**serializace**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serializace sady řádků, takže můžete používat funkce, které vyžadují Serializovaná data, například **ROW_NUMBER ()** .

Následující dotaz je úspěšný, protože data jsou serializována.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Sada řádků je také považována za serializovanou, pokud je výsledkem operátorů **řazení**, **horních**nebo **rozsahů** , volitelně následovaných **projektem**, **projektem-zaskladněním**, **rozšířeným**, **WHERE**, **analyze**, **MV-expanding.** nebo **přebírat** operátory.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Dotazy mezi databázemi a mezi clustery

[Dotazy mezi databázemi a mezi clustery](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Můžete zadat dotaz na databázi ve stejném clusteru tak, že na ni `database("MyDatabase").MyTable`odkazujete jako na. Dotaz na databázi na vzdáleném clusteru můžete vytvořit tak, že na ni `cluster("MyCluster").database("MyDatabase").MyTable`odkazujete jako na.

Následující dotaz se volá z jednoho clusteru a dotazuje se na `MyCluster` data z clusteru. Chcete-li spustit tento dotaz, použijte vlastní název clusteru a název databáze.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analýza uživatelů

Tato část obsahuje prvky a dotazy, které ukazují, jak snadné je provádět analýzu chování uživatele v Kusto.

### <a name="activity_counts_metrics-plugin"></a>modul plug-in activity_counts_metrics

[**modul plug-in activity_counts_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Vypočítá užitečnou metriku aktivity (celkový počet hodnot, jedinečných hodnot počtu, jedinečný počet nových hodnot a agregovaný jedinečný počet). Metriky se vypočtou pro každé časové okno, pak se porovnávají a agreguje do a s využitím všech předchozích časových oken.

Následující dotaz analyzuje přijetí uživatelem pomocí výpočtu denních počtů aktivit.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>modul plug-in activity_engagement

[**modul plug-in activity_engagement**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Vypočítá poměr zapojení aktivity na základě sloupce ID přes posuvnou časovou osu okna. **modul plug-in activity_engagement** se dá použít k výpočtu Dau, WAU a MAU (denně, týdně a měsíčně aktivních uživatelů).

Následující dotaz vrátí poměr celkových jedinečných uživatelů, kteří používají aplikaci denně v porovnání s celkovým počtem jedinečných uživatelů, kteří používají aplikaci týdně, na přesunu sedmi dnů.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Při výpočtu DAU/MAU změňte koncová data a období přesunutí (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>modul plug-in activity_metrics

[**modul plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Vypočítá užitečnou metriku aktivity (jedinečné hodnoty počtu, jedinečný počet nových hodnot, míru uchování a míru četnosti změn) na základě okna aktuální období vs. předchozí období.

Následující dotaz vypočítá četnost a míru uchování pro danou datovou sadu.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>modul plug-in new_activity_metrics

[**modul plug-in new_activity_metrics**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Vypočítá užitečnou metriku aktivity (jedinečné hodnoty počtu, jedinečný počet nových hodnot, míru uchování a míru četnosti změn) pro kohorta nových uživatelů. Koncept tohoto modulu plug-in je podobný [**modulu plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), ale zaměřuje se na nové uživatele.

Následující dotaz vypočítá míru uchování a četnosti změn pomocí okna týden po týdnu pro nové uživatele kohorta (uživatelé, kteří dorazili první týden).

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>modul plug-in session_count

[**modul plug-in session_count**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Vypočítá počet relací na základě sloupce ID na časové ose.

Následující dotaz vrátí počet relací. Relace se považuje za aktivní, pokud se ID uživatele zobrazí alespoň jednou v časovém intervalu 100. v časovém intervalu po dobu až je to 41 časů.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>modul plug-in funnel_sequence

[**modul plug-in funnel_sequence**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Vypočítá jedinečný počet uživatelů, kteří provedli sekvenci stavů. ukazuje rozdělení předchozích a dalších stavů, které vedla nebo byly následovány sekvencí.

Následující dotaz ukazuje, co událost nastane před a po všech Tornado událostech v 2007.

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>modul plug-in funnel_sequence_completion

[**modul plug-in funnel_sequence_completion**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Vypočítá trychtýři dokončených kroků sekvence v různých časových obdobích.

Následující dotaz zkontroluje trychtýři dokončení sekvence: `Hail -> Tornado -> Thunderstorm -> Wind` v celkovém počtu hodin jedné hodiny, čtyři hodiny a jeden den (`[1h, 4h, 1d]`).

**\[** [**Kliknutím spustíte dotaz.** ](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funkce

Tato část se zabývá [**funkcemi**](https://docs.microsoft.com/azure/kusto/query/functions): opakovaně použitelnými dotazy uloženými na serveru. Funkce mohou být vyvolány dotazy a jinými funkcemi (rekurzivní funkce nejsou podporovány).

> [!NOTE]
> Nemůžete vytvořit funkce v clusteru Help, který je jen pro čtení. Pro tuto část použijte vlastní testovací cluster.

Následující příklad vytvoří funkci, která jako argument přijímá název stavu (`MyState`).

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Následující příklad volá funkci, která získává data pro stav Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Následující příklad odstraní funkci, která byla vytvořena v prvním kroku.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Další kroky

[Reference k jazyku dotazů Kusto](https://aka.ms/kustolangref)

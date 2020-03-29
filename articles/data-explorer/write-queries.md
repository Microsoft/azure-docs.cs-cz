---
title: Psaní dotazů pro Azure Data Explorer
description: V tomto návodu se dozvíte, jak provádět základní a pokročilejší dotazy pro Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881181"
---
# <a name="write-queries-for-azure-data-explorer"></a>Psaní dotazů pro Azure Data Explorer

V tomto článku se dozvíte, jak pomocí dotazovacího jazyka v Aplikaci Azure Data Explorer provádět základní dotazy s nejběžnějšími operátory. Můžete také získat expozici některé z pokročilejších funkcí jazyka.

## <a name="prerequisites"></a>Požadavky

Dotazy v tomto článku můžete spustit jedním ze dvou způsobů:

- V Azure Data Explorer *pomoci clusteru,* který jsme nastavili pro pomoc učení.
    [Přihlaste se ke clusteru](https://dataexplorer.azure.com/clusters/help/databases/samples) pomocí e-mailového účtu organizace, který je členem adresáře Azure Active.

- Ve vlastním clusteru, který obsahuje ukázková data StormEvents. Další informace najdete [v tématu Úvodní příručka: Vytvoření clusteru A databáze Azure A databáze](create-cluster-database-portal.md) a [ukázková data ingestování do Průzkumníka dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Přehled dotazovacího jazyka

Dotazovací jazyk v Průzkumníku dat Azure je požadavek jen pro čtení pro zpracování dat a vrácení výsledků. Požadavek je uveden ve formátu prostého textu pomocí modelu toku dat, který usnadňuje čtení, vytváření a automatizaci syntaxe. Dotaz používá entity schématu, které jsou uspořádány v hierarchii podobné SQL: databáze, tabulky a sloupce.

Dotaz se skládá z posloupnosti příkazů dotazu,`;`oddělených středníkem ( ), přičemž alespoň jeden příkaz je příkaz tabulkového výrazu, což je příkaz, který vytváří data uspořádaná v síti sloupců a řádků podobné tabulce. Příkazy tabulkového výrazu dotazu vytvářejí výsledky dotazu.

Syntaxe příkazu tabulkového výrazu má tabulkový datový tok z jednoho operátoru tabulkového dotazu do jiného, počínaje zdrojem dat (například tabulka v databázi`|`nebo operátor, který vytváří data) a poté prochází sadou operátorů transformace dat, které jsou spojeny pomocí oddělovače kanálu ( ).

Například následující dotaz má jeden příkaz, což je příkaz tabulkového výrazu. Příkaz začíná odkazem na tabulku `StormEvents` s názvem (databáze, která je hostitelem této tabulky, je zde implicitní a část informací o připojení). Data (řádky) pro tuto tabulku jsou pak filtrována podle hodnoty `StartTime` sloupce a `State` poté filtrována podle hodnoty sloupce. Dotaz pak vrátí počet řádků "přežívání".

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

V tomto případě je výsledkem:

|Počet|
|-----|
|   23|
| |

Další informace naleznete v [odkazu na dotazovací jazyk](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Nejběžnější operátoři

Operátory uvedené v této části jsou stavební bloky pro pochopení dotazů v Průzkumníku dat Azure. Většina dotazů, které napíšete, bude obsahovat několik těchto operátorů.

Spuštění dotazů v clusteru nápovědy: vyberte **možnost Kliknutím spusťte dotaz** nad jednotlivými dotazy.

Spuštění dotazů ve vlastním clusteru:

1. Zkopírujte každý dotaz do webové aplikace dotazu a pak vyberte dotaz nebo umístěte kurzor do dotazu.

1. V horní části aplikace vyberte **Spustit**.

### <a name="count"></a>count

[**Count**](https://docs.microsoft.com/azure/kusto/query/countoperator): Vrátí počet řádků v tabulce.

Následující dotaz vrátí počet řádků v tabulce StormEvents.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>vzít

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Vrátí až na zadaný počet řádků dat.

Následující dotaz vrátí pět řádků z tabulky StormEvents. *Limit* klíčových slov je alias pro *přijmout.*

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Neexistuje žádná záruka, které záznamy jsou vráceny, pokud nejsou seřazena zdrojová data.

### <a name="project"></a>projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Vybere podmnožinu sloupců.

Následující dotaz vrátí určitou sadu sloupců.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>where

[**kde**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtruje tabulku do podmnožiny řádků, které splňují predikát.

Následující dotaz filtruje `EventType` data `State`podle a .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**Řazení**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Seřaďte řádky vstupní tabulky do pořadí podle jednoho nebo více sloupců.

Následující dotaz seřadí data v `DamageProperty`sestupném pořadí podle .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Pořadí operací je důležité. Zkuste `take 5` uvedení `sort by`před . Máte jiné výsledky?

### <a name="top"></a>top

[**nahoře**](https://docs.microsoft.com/azure/kusto/query/topoperator): Vrátí první *N* záznamy seřazené podle zadaných sloupců.

Následující dotaz vrátí stejné výsledky jako výše s jedním operátorem méně.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Rozšířit

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Vypočítá odvozené sloupce.

Následující dotaz vytvoří nový sloupec výpočtem hodnoty v každém řádku.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Výrazy mohou obsahovat všechny obvyklé operátory (+, -, *, /, %), a existuje řada užitečných funkcí, které můžete volat.

### <a name="summarize"></a>Sumarizovat

[**shrneme-li**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agreguje skupiny řádků.

Následující dotaz vrátí počet událostí `State`podle .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**Shrneme-li** operátor skupiny dohromady řádky, které mají stejné hodnoty v **by** klauzule a potom používá agregace funkce (například **počet)** kombinovat každou skupinu do jednoho řádku. Takže v tomto případě je řádek pro každý stav a sloupec pro počet řádků v tomto stavu.

Existuje řada funkcí agregace a můžete použít několik z nich v jednom **operátoru souhrnu** k vytvoření několika vypočítaných sloupců. Můžete například získat počet bouří v každém státě a jedinečný počet bouří na stát a pak použít **top** pro získání nejvíce bouře postižených stavů.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Výsledek operace **shrnutí** má:

- Každý sloupec pojmenovaný **v**

- Sloupec pro každý vypočítaný výraz

- Řádek pro každou kombinaci podle hodnot

### <a name="render"></a>Vykreslení

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Vykresluje výsledky jako grafický výstup.

Následující dotaz zobrazuje sloupcový graf.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Následující dotaz zobrazuje jednoduchý časový graf.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Následující dotaz počítá události podle modulu času jeden den, binned do hodin a zobrazí časový graf.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Následující dotaz porovnává více denních řad v časovém grafu.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Operátor **vykreslení** je funkce na straně klienta, nikoli část motoru. Je integrován do jazyka pro snadné použití. Webová aplikace podporuje následující možnosti: barchart, sloupcový graf, piechart, časový graf a linechart. 

## <a name="scalar-operators"></a>Skalární operátory

Tato část obsahuje některé z nejdůležitějších skalární operátory.

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Zaokrouhlí hodnoty dolů na celé číslo daného rozměru přihrádky.

Následující dotaz vypočítá počet s velikostí bloku jeden den.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>případ()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Vyhodnotí seznam predikátů a vrátí první výsledný výraz, jehož predikát je splněn, nebo konečný **výraz else.** Tento operátor můžete použít ke kategorizaci nebo seskupit data:

Následující dotaz vrátí nový `deaths_bucket` sloupec a seskupí úmrtí podle čísla.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

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

### <a name="extract"></a>extrakt()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Získá shodu pro regulární výraz z textového řetězce.

Následující dotaz extrahuje hodnoty určitých atributů ze trace.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Tento dotaz používá **příkaz let,** který váže název `MyData`(v tomto případě) na výraz. Pro zbytek oboru, ve kterém let **prohlášení** se zobrazí (globální obor nebo v oboru tělo funkce), název lze použít k odkazování na jeho vázanou hodnotu.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpretuje řetězec jako hodnotu JSON a vrátí hodnotu jako dynamickou. Je lepší než použití **extractjson()** funkce, když potřebujete extrahovat více než jeden prvek složeného objektu JSON.

Následující dotaz extrahuje prvky JSON z pole.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Následující dotaz extrahuje prvky JSON.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Následující dotaz extrahuje prvky JSON s dynamickým datovým typem.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Odečte daný časový rozsah od aktuálního času hodin UTC.

Následující dotaz vrátí data za posledních 12 hodin.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>začátektýdne()

[**začátek týdne()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Vrátí začátek týdne obsahujícího datum posunuté o posun, pokud je k dispozici.

Následující dotaz vrátí začátek týdne s různými posuny.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Tento dotaz používá operátor **range,** který generuje tabulku hodnot s jedním sloupcem. Viz také: [**začátekdne()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**začátektýdne()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**začátekroku()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction), [**začátekměsíce()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), a [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>mezi()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Odpovídá vstupu, který je uvnitř včetně rozsahu.

Následující dotaz filtruje data podle daného rozsahu dat.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Následující dotaz filtruje data podle daného období s mírnou`3d`odchylkou tří dnů ( ) od počátečního data.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabulkové operátory

Kusto má mnoho tabulkových operátorů, z nichž některé jsou zahrnuty v jiných částech tohoto článku. Zde se zaměříme na **analýzu**. 

### <a name="parse"></a>parse

[**analýza**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Vyhodnotí řetězec výraz a analyzuje jeho hodnotu do jednoho nebo více počítaných sloupců. Existují tři způsoby, jak analyzovat: jednoduché (výchozí), regulární výraz a uvolněné.

Následující dotaz analyzuje trasování a extrahuje příslušné hodnoty pomocí výchozí jednoduché analýzy. Výraz (označovaný jako StringConstant) je normální řetězcová hodnota a shoda je striktní: rozšířené sloupce musí odpovídat požadovaným typům.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

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

Následující dotaz analyzuje trasování a extrahuje příslušné `kind = regex`hodnoty pomocí . StringConstant může být regulární výraz.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

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

Následující dotaz analyzuje trasování a extrahuje příslušné `kind = relaxed`hodnoty pomocí . StringConstant je normální hodnota řetězce a shoda je uvolněná: rozšířené sloupce mohou částečně odpovídat požadované typy.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

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

### <a name="make-series"></a>řada make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): agreguje skupiny řádků, jako [je shrnutí](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), ale generuje vektor (čas) řady pro každou kombinaci podle hodnot.

Následující dotaz vrátí sadu časových řad pro počet událostí bouře za den. Dotaz pokrývá tříměsíční období pro každý stát, vyplnění chybějící přihrádky s konstantou 0:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Jakmile vytvoříte sadu (časových) řad, můžete použít funkce řad k detekci anomálních tvarů, sezónních vzorů a mnoho dalšího.

Následující dotaz extrahuje první tři stavy, které měly nejvíce událostí v určitém dni:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Další informace naleznete v úplném seznamu [funkcí řady](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Pokročilé agregace

Probrali jsme základní agregace, jako **je počet** a **shrnutí**, dříve v tomto článku. Tato část představuje pokročilejší možnosti.

### <a name="top-nested"></a>vnořené nahoře

[**vnořené nahoře**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Vytváří hierarchické nejlepší výsledky, kde každá úroveň je přechod k podrobnostem na základě předchozích hodnot úrovně.

Tento operátor je užitečný pro scénáře vizualizace řídicího panelu nebo v případě, že je nutné odpovědět na následující otázku: "Najděte nejvyšší hodnoty N K1 (pomocí některých agregací); pro každou z nich, najít to, co jsou top-M hodnoty K2 (pomocí jiné agregace); ..."

Následující dotaz vrátí hierarchickou `State` tabulku na nejvyšší `Sources`úrovni následovanou .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() plugin

[**plugin pivot()**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Otočí tabulku otočením jedinečných hodnot z jednoho sloupce ve vstupní tabulce do více sloupců ve výstupní tabulce. Operátor provádí agregace, kde jsou požadovány na všechny zbývající hodnoty sloupců v konečném výstupu.

Následující dotaz použije filtr a převede řádky do sloupců.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Vrátí odhad počtu odlišných hodnot výrazu ve skupině. Použijte [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) počítat všechny hodnoty.

Následující dotaz se `Source` `State`liší podle .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Vrátí odhad počtu odlišných hodnot výrazu pro řádky, pro které predikát vyhodnotí hodnotu true.

Následující dotaz počítá odlišné `Source` hodnoty `DamageProperty < 5000`where .

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Vypočítá **dcount** z výsledků HyperLogLog (generované [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) nebo [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Následující dotaz používá algoritmus HLL ke generování počtu.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Vyhledá řádek ve skupině, který maximalizuje výraz a vrátí hodnotu jiného výrazu (nebo * vrátí celý řádek).

Následující dotaz vrátí čas poslední zprávy o zaplavení v každém stavu.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Vrátí dynamické pole (JSON) sady odlišných hodnot, které výraz převezme ve skupině.

Následující dotaz vrátí všechny časy, kdy zaplavování bylo hlášeno každý stav a vytvoří pole ze sady různých hodnot.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Rozbalí kolekci více hodnot ze sloupce s dynamickým typem tak, aby každá hodnota v kolekci získá samostatný řádek. Všechny ostatní sloupce v rozbaleném řádku jsou duplikovány. Je to opak makelistu.

Následující dotaz generuje ukázková data vytvořením sady a jejím následným použitím k předvedení možností **rozšíření mv.**

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentily()

[**percentily()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Vrátí odhad pro [**zadaný nejbližší percentil**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) základního souboru definovaný výrazem. Přesnost závisí na hustotě populace v oblasti percentilu. Lze použít pouze v kontextu agregace uvnitř [**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Následující dotaz vypočítá percentily pro trvání bouře.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Následující dotaz vypočítá percentily pro dobu trvání bouře podle stavu a normalizuje data pětiminutovými přihrádky (`5m`).

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Křížová datová sada

Tato část popisuje prvky, které umožňují vytvářet složitější dotazy, spojit data mezi tabulkami a dotazovat se napříč databázemi a clustery.

### <a name="let"></a>Nechat

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Zlepšuje modularitu a opětovné použití. Příkaz **let** umožňuje rozdělit potenciálně složitý výraz na více částí, z nichž každá je vázána na název, a sestavit tyto části dohromady. Příkaz **let** lze také použít k vytvoření uživatelem definovaných funkcí a zobrazení (výrazy nad tabulkami, jejichž výsledky vypadají jako nová tabulka). Výrazy vázané příkazem **let** mohou být skalárního typu, tabulkového typu nebo uživatelem definované funkce (lambdas).

Následující příklad vytvoří proměnnou tabulkového typu a použije ji v následujícím výrazu.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

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

[**Spojení**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Sloučit řádky dvou tabulek a vytvořit novou tabulku porovnáním hodnot zadaného sloupce (sloupců) z každé tabulky. Kusto podporuje celou řadu typů spojení: **fullouter**, **vnitřní**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightouter**, rightsemi .

Následující příklad spojuje dvě tabulky s vnitřním spojením.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

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
> Pomocí **operátorů where** a **project** můžete snížit počet řádků a sloupců ve vstupních tabulkách před spojením. Pokud je jedna tabulka vždy menší než druhá, použijte ji jako levou (piped) stranu spojení. Sloupce pro shodu spojení musí mít stejný název. V případě potřeby použijte operátor **projektu** k přejmenování sloupce v jedné z tabulek.

### <a name="serialize"></a>Serializaci

[**serializace**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serializuje sadu řádků, takže můžete používat funkce, které vyžadují serializovaná data, například **row_number()**.

Následující dotaz je úspěšný, protože data jsou serializována.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Sada řádků je také považována za serializovanou, pokud je výsledkem: **operátory řazení**, **horní**nebo **rozsah,** případně následované **projektem**, **project-away**, **extend**, **where** **,,,** **mv-expand**nebo **take** operátory.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Dotazy mezi databázemi a mezi clustery

[Dotazy mezi databázemi a mezi clustery](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Databázi ve stejném clusteru můžete zadat odkazem na `database("MyDatabase").MyTable`. Databázi ve vzdáleném clusteru můžete dotazovat `cluster("MyCluster").database("MyDatabase").MyTable`tak, že na ní odkazujete jako na .

Následující dotaz je volán z jednoho `MyCluster` clusteru a dotazuje data z clusteru. Chcete-li tento dotaz spustit, použijte vlastní název clusteru a název databáze.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analýza uživatelů

Tato část obsahuje prvky a dotazy, které ukazují, jak snadné je provádět analýzu chování uživatelů v Kusto.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

[**activity_counts_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Vypočítá metriky užitečné aktivity (hodnoty celkového počtu, odlišné hodnoty počtu, odlišný počet nových hodnot a agregovaný odlišný počet). Metriky se počítají pro každé časové okno, pak jsou porovnány a agregovány do a se všemi předchozími časovými okny.

Následující dotaz analyzuje přijetí uživatele výpočtem denní aktivity počítá.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

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

### <a name="activity_engagement-plugin"></a>activity_engagement plugin

[**activity_engagement plugin**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Vypočítá poměr zapojení aktivity na základě sloupce ID v posuvném okně časové osy. **activity_engagement plugin** lze použít pro výpočet DAU, WAU a MAU (denní, týdenní a měsíční aktivní uživatelé).

Následující dotaz vrátí poměr celkových odlišných uživatelů, kteří používají aplikaci denně, ve srovnání s celkovými odlišnými uživateli, kteří používají aplikaci týdně, v pohyblivém sedmidenním okně.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

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
> Při výpočtu DAU/MAU změňte koncová data a období přesunutí okna (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics plugin

[**activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Vypočítá metriky užitečné aktivity (odlišné hodnoty počtu, odlišný počet nových hodnot, míra uchování a rychlost změn) na základě aktuálního okna období vs. okno předchozího období.

Následující dotaz vypočítá míru změny a uchování pro danou datovou sadu.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

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

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

[**new_activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Vypočítá metriky užitečné aktivity (odlišné hodnoty počtu, odlišný počet nových hodnot, míra uchování a rychlost změn) pro kohortu nových uživatelů. Koncept tohoto pluginu je podobný [**activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), ale zaměřuje se na nové uživatele.

Následující dotaz vypočítá retenční a konve sazby s týden-přes-týdenní okno pro kohorty nových uživatelů (uživatelé, kteří přišli na první týden).

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

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

### <a name="session_count-plugin"></a>session_count plugin

[**session_count plugin**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Vypočítá počet návštěv na základě sloupce ID na časové ose.

Následující dotaz vrátí počet relací. Relace je považována za aktivní, pokud se ID uživatele zobrazí alespoň jednou v časovém rámci 100 časových slotů, zatímco okno zpětného vyhledávání relace je 41 časových slotů.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

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

### <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

[**funnel_sequence plugin**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Vypočítá odlišný počet uživatelů, kteří vzali posloupnost stavů; zobrazuje rozdělení předchozích a následujících stavů, které vedly k sekvenci nebo byly následovány.

Následující dotaz ukazuje, jaká událost se stane před a po všech událostech Tornado v roce 2007.

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

[**funnel_sequence_completion plugin**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Vypočítá trychtýř dokončených sekvenčních kroků v různých časových obdobích.

Následující dotaz kontroluje dokončení trychtýře sekvence: `Hail -> Tornado -> Thunderstorm -> Wind` v "celkově" časy jedna`[1h, 4h, 1d]`hodina, čtyři hodiny a jeden den ( ).

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

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

Tato část popisuje [**funkce**](https://docs.microsoft.com/azure/kusto/query/functions): opakovaně použitelné dotazy, které jsou uloženy na serveru. Funkce mohou být vyvolány dotazy a dalšími funkcemi (rekurzivní funkce nejsou podporovány).

> [!NOTE]
> V clusteru nápovědy, který je jen pro čtení, nelze vytvořit funkce. Pro tuto část použijte vlastní testovací cluster.

Následující příklad vytvoří funkci, která má`MyState`název stavu ( ) jako argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Následující příklad volá funkci, která získává data pro stát Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Následující příklad odstraní funkci, která byla vytvořena v prvním kroku.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Další kroky

[Odkaz na dotaz kusto](https://aka.ms/kustolangref)

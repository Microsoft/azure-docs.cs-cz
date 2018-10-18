---
title: Psát dotazy pro Průzkumník dat Azure
description: V tomto návodu se dozvíte, jak provádět základní a rozšířené dotazy pro Průzkumník dat Azure.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 952d8801c189322161bbf8b795676af48b92c29f
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394325"
---
# <a name="write-queries-for-azure-data-explorer"></a>Psát dotazy pro Průzkumník dat Azure

V tomto článku se dozvíte, jak provádět základní dotazy s nejběžnější operátory pomocí dotazovací jazyk v Průzkumníku dat Azure. Získáte také vystavení některé pokročilejší funkce jazyka.

## <a name="prerequisites"></a>Požadavky

Dotazy můžete spustit v tomto článku v jednom ze dvou způsobů:

- V Průzkumníku dat Azure *clusteru help* , že jsme nastavili na podporu učení.
    [Přihlaste se ke clusteru](https://dataexplorer.azure.com/clusters/help/databases/samples) pomocí účtu organizace e-mailu, který je členem skupiny Azure Active directory.

- Na vlastní cluster, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [rychlý start: vytvoření clusteru Průzkumník dat Azure a databáze](create-cluster-database-portal.md) a [Ingestování ukázková data do Průzkumníku dat Azure](ingest-sample-data.md).

Obsahuje data týkající se počasí s z StormEvents ukázkovou datovou sadu [National Center pro informace o okolnostech](https://www.ncdc.noaa.gov/stormevents/).

## <a name="overview-of-the-query-language"></a>Přehled dotazovacího jazyka

Dotazovací jazyk, v Průzkumníku dat Azure je jen pro čtení požadavek na zpracování dat a vrátí výsledky. Žádost je uvedena jako prostý text, použití toku dat modelu navržené tak, aby syntaxe usnadňuje čtení, vytváření a automatizace. Dotaz používá schématu entity, které jsou uspořádány do hierarchie podobný SQL: databáze, tabulky a sloupce.

Dotaz se skládá ze sekvence příkazů dotazů, oddělených středníkem (`;`), alespoň jeden příkaz je příkaz tabulkového výrazu, což je příkaz, který vytvoří data uspořádaná v tabulku jako mřížku sloupců a řádků. Příkazy tabulkového výrazu dotazu výsledky dotazu.

Syntaxe tabulkového výrazu má tabulkový datový tok z jedné operátor tabulkový dotaz do druhého, počínaje zdroje dat (třeba tabulky v databázi nebo operátor, který data produkuje) a potom předávaných mezi sadu transformace dat operátory, které jsou technologicky propojené, prostřednictvím kanálu (`|`) oddělovač.

Například následující dotaz vrátí jediném příkazu, který je příkaz tabulkového výrazu. Příkaz se spustí s odkazem na tabulky nazvané `StormEvents` (databázi hostovat v této tabulce je implicitní tady a část informací o připojení). Data (řádky) tabulky pak se filtrují podle hodnoty `StartTime` sloupce a potom filtrovaná podle hodnoty `State` sloupce. Dotaz vrátí počet řádků "přežití".

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

V takovém případě je výsledek:

|Počet|
|-----|
|   23|
| |

Další informace najdete v článku [dotazování referenční informace k jazyku](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Nejběžnější operátory

Operátory popsané v této části jsou stavební bloky pro porozumění dotazy v Průzkumníku dat Azure. Většina dotazů, který napíšete bude obsahovat několik těchto operátorů.

Ke spouštění dotazů v clusteru help: vyberte **klikněte na tlačítko Spustit dotaz** každý dotaz výše.

Ke spouštění dotazů na vlastní cluster:

1. Kopírování jednotlivých dotazů do dotazů založených na webové aplikace a pak vyberte dotaz nebo umístěte ukazatel myši v dotazu.

1. V horní části aplikace, vyberte **spustit**.

### <a name="count"></a>count

[**počet**](https://docs.microsoft.com/azure/kusto/query/countoperator): Vrátí počet řádků v tabulce.

Následující dotaz vrátí počet řádků v tabulce StormEvents.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>Take

[**Využijte**](https://docs.microsoft.com/azure/kusto/query/takeoperator): vrátí až po zadaný počet řádků dat.

Následující dotaz vrátí pět řádků z tabulky StormEvents. Klíčové slovo *limit* je alias pro *trvat.*

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Neexistuje žádná záruka záznamy, které jsou vráceny, pokud je řazen zdrojová data.

### <a name="project"></a>projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): vybere podmnožinu sloupců.

Následující dotaz vrátí konkrétní sadu sloupců.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>kde

[**kde**](https://docs.microsoft.com/azure/kusto/query/whereoperator): filtruje tabulku na podmnožinu řádků, které splňují predikát.

Následující dotaz vyfiltruje data podle `EventType` a `State`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Řazení

[**řazení**](https://docs.microsoft.com/azure/kusto/query/sortoperator): řazení řádků ve vstupní tabulce v pořadí podle jednoho nebo více sloupců.

Následující dotaz seřadí v sestupném pořadí podle data `DamageProperty`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Je důležité pořadí operací. Zkuste umístit `take 5` před `sort by`. Získat jiné výsledky?

### <a name="top"></a>nahoru

[**horní**](https://docs.microsoft.com/azure/kusto/query/topoperator): vrátí první *N* záznamy seřazené podle zadaných sloupců.

Následující dotaz vrátí stejné výsledky jako výše jeden menší operátorem.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Rozšíření

[**rozšíření**](https://docs.microsoft.com/azure/kusto/query/extendoperator): výpočetní prostředí odvozené sloupce.

Následující dotaz vytvoří nový sloupec výpočtem hodnoty v každém řádku.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Výrazy můžou obsahovat běžných operátorů (+, -, *, /, %), a celou řadu užitečných funkcí, které můžete volat.

### <a name="summarize"></a>shrnutí

[**Shrnutí**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): agreguje skupiny řádků.

Následující dotaz vrátí počet událostí podle `State`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**Shrnout** operátor seskupuje řádky, které obsahují stejné hodnoty v **podle** klauzule a pak používá funkci agregace (například **počet**) zkombinovat každou skupinu na jeden řádek. Ano v tomto případě je řádek pro každý stav a sloupec pro počet řádků v tomto stavu.

Existuje velká škála agregační funkce a můžete použít několik z nich v jednom **shrnout** operátoru pro vytvoření několika vypočítané sloupce. Například může získat počet výstrahami v jednotlivých stavech a jedinečné číslo výstrahami stav, pak použijte **horní** získat stavy největší vliv storm.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Výsledek **shrnout** operace obsahuje:

- Každý sloupec s názvem v **podle**

- U každého výrazu počítaného sloupce

- Řádek pro každou kombinaci podle hodnot

### <a name="render"></a>Vykreslení

[**vykreslení**](https://docs.microsoft.com/azure/kusto/query/renderoperator): zobrazí výsledky jako grafického výstupu.

Následující dotaz zobrazí sloupcový graf.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Následující dotaz zobrazí graf jednoduché času.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Následující dotaz počty událostí podle času modulo jeden den, rozdělený na intervaly na hodiny a zobrazí graf času.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Následující dotaz porovnává více denní řadu v grafu čas.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **Vykreslení** operátor je funkce na straně klienta spíše než součástí motoru. Je integrovaná do jazyka pro snadné použití. Webová aplikace podporuje následující možnosti: barchart columnchart, piechart promítnu a linechart. 

## <a name="scalar-operators"></a>Skalární operátory

Tato část popisuje některé z vašich nejdůležitějších skalární operátory.

### <a name="bin"></a>BIN()

[**BIN()**](https://docs.microsoft.com/azure/kusto/query/binfunction): zaokrouhlí dolů celé číslo hodnoty násobkem velikosti danou přihrádku.

Následující dotaz vypočítá počet a velikost kbelíku jeden den.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>Case()

[**Case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): vyhodnotí seznam predikáty a vrátí první výsledek výrazu, jehož predikát uspokojen nebo konečné **else** výrazu. Tento operátor můžete kategorizovat nebo seskupení dat:

Následující dotaz vrátí nový sloupec `deaths_bucket` a seskupí úmrtí podle čísla.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

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

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): získá odpovídající regulární výraz v textovém řetězci.

Následující dotaz extrahuje hodnoty konkrétní atribut z trasování.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Tento dotaz používá **nechat** příkazu, který váže názvu (v tomto případě `MyData`) ve výrazu. Pro zbytek oboru, ve kterém **nechat** objevuje příkaz (globálním oboru nebo v rozsahu těla funkce), název slouží k odkazování na jeho vázaná hodnota.

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): interpretuje řetězec jako hodnotu JSON a vrátí hodnotu jako dynamický. Je lepší než používat **extractjson()** fungovat v případě, že je potřeba extrahovat více než jeden element složené objekt JSON.

Následující dotaz extrahuje prvky JSON z pole.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Následující dotaz extrahuje elementy JSON.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Následující dotaz extrahuje JSON elementy s typem dynamická data.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): odečte daný časový interval od aktuální čas UTC.

Následující dotaz vrací data za posledních 12 hodin.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mvexpand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): vrátí začátek týdne obsahující data o posun, pokud je k dispozici

Následující dotaz vrátí začátek týdne s jinou hodnotu posunu.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Tento dotaz používá **rozsah** operátor, který generuje jednosloupcovou tabulku hodnot. Viz také: [ **startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [ **startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [ **startofyear()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [ **startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [ **endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [ **endofweek()**  ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [ **endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), a [ **endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>Between()

[**Between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): odpovídá vstup, který se nachází uvnitř včetně rozsahu.

Následující dotaz vyfiltruje data v daném časovém období.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Následující dotaz vyfiltruje data v daném časovém období se mírnou změnou tři dny (`3d`) od data zahájení.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabulkové operátory

Kusto má mnoho tabulkové operátory, z nichž některé jsou popsané v dalších částech tohoto článku. Tady zaměříme na **analyzovat**. 

### <a name="parse"></a>Analýzy

[**analyzovat**](https://docs.microsoft.com/azure/kusto/query/parseoperator): řetězcový výraz vyhodnocuje a analyzuje jeho hodnotu do jednoho nebo více počítaných sloupců. Existují tři způsoby, jak analyzovat: jednoduché (výchozí), regex a volný.

Následující dotaz analyzuje trasování a extrahuje příslušné hodnoty, použijte výchozí nastavení jednoduché analýzy. Výraz (označované jako StringConstant) je hodnota regulární řetězec a shoda není strict: Rozšířené sloupce musí odpovídat požadované typy.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

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

Následující dotaz trasování rozloží a extrahuje údaje příslušné hodnoty pomocí `kind = regex`. StringConstant může být regulární výraz.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

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

Následující dotaz trasování rozloží a extrahuje údaje příslušné hodnoty pomocí `kind = relaxed`. StringConstant je regulární řetězcová hodnota a shoda není volný: Rozšířené sloupce částečně odpovídá požadované typy.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

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

## <a name="time-series-analysis"></a>Analýzu časových řad

### <a name="make-series"></a>Zkontrolujte series

[**Zkontrolujte series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): agreguje společně skupiny řádků jako [shrnout](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), ale generuje řady (čas) vektoru za každou kombinaci podle hodnot.

Následující dotaz vrátí sadu časových řad pro počet storm událostí za den. Tříměsíční období pro každý stav vyplnění chybějící přihrádky pomocí konstanta 0 se zabývá dotazu:

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Jakmile vytvoříte sadu řad (čas), můžete použít řadu funkcí ke zjištění neobvyklé obrazce, sezónní vzory a mnoho dalších.

Následující dotaz extrahuje nejvyšší tři stavy, které nejvíce událostem došlo v určitý den:

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Další informace najdete v tématu úplný seznam [řady funkcí](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Pokročilé agregace

Jsme probrali základní agregace, jako je třeba **počet** a **shrnout**výše v tomto článku. Tato část představuje pokročilejší možnosti.

### <a name="top-nested"></a>vnořené nahoru

[**vnořené horní**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): vytvoří hierarchické výsledky horní, kde každá úroveň je procházení podrobností podle předchozí hodnoty úrovni.

Tento operátor je užitečné pro scénáře vizualizace řídicího panelu, nebo když je nutné odpovědět na otázku, jako je následující: "najít prvních N hodnot k1 (pomocí některé agregace); pro každý z nich najdete, co jsou hodnoty top-M k2 (s použitím jiného agregace); ..."

Následující dotaz vrátí hierarchickou tabulku s `State` na nejvyšší úrovni, za nímž následuje `Sources`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>modul plug-in Pivot()

[**modul plug-in Pivot()**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): otočí to zapnutím jedinečné hodnoty od jeden sloupec ve vstupní tabulce do více sloupců v tabulce výstupní tabulky. Operátor, který provádí agregace, ve kterém jsou povinné pro všechny zbývající hodnoty sloupců v konečném výstupu.

Následující dotaz aplikuje filtr a otáčí řádky na sloupce.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>DCount()

[**DCount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): vrací odhadovaný počet jedinečných hodnot výrazu ve skupině. Použití [ **count()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) mají spočítat všechny hodnoty.

Následující dotaz vrátí počet jedinečných `Source` podle `State`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): vrací odhadovaný počet jedinečných hodnot výrazů pro řádky, pro které predikát vyhodnotí na hodnotu true.

Následující dotaz vrátí počet různých hodnot položky `Source` kde `DamageProperty < 5000`.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): počítá **dcount** ve výsledcích HyperLogLog (generovaných [ **hll** ](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) nebo [ **hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Následující dotaz používá algoritmus HLL ke generování počet.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): vyhledává řádek ve skupině, která maximalizuje výrazu a vrátí hodnotu jiný výraz (nebo * obnovíte celý řádek).

Následující dotaz vrátí čas poslední záplava sestav v jednotlivých stavech.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): vrátí dynamická pole (JSON) sadu jedinečných hodnot, které přebírá výraz ve skupině.

Následující dotaz vrátí všechny časy při zahlcení byla hlášena každý stav a vytvoří pole ze sady různých hodnot.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mvexpand"></a>mvexpand

[**mvexpand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): rozbalí kolekcí s více hodnotami ze sloupce dynamickým typem tak, aby každá hodnota v kolekci získá samostatný řádek. Všechny ostatní sloupce v rozbaleném řádku jsou duplicitní. Je opakem makelist.

Následující dotaz vygeneruje ukázková data vytvořením sady a poté ho použít k předvedení **mvexpand** možnosti.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mvexpand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): vrací odhadovaný pro zadaný rozbočovač [ **nejbližší pořadí percentilu** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) plnění definované výrazem. Přesnost závisí na hustota na percentilu počtu obyvatel v oblasti. Lze použít pouze v kontextu agregace uvnitř [ **shrnout**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Následující dotaz vypočítá percentily doby trvání storm.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Následující dotaz vypočítá percentily doby trvání storm podle stavu a normalizuje data podle intervalů pět minut (`5m`).

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Různé datové sady

Tato část popisuje prvky, které vám umožní vytvářet složitější dotazy, spojovat data napříč tabulky a dotazu mezi databází a clustery.

### <a name="let"></a>let

[**umožní**](https://docs.microsoft.com/azure/kusto/query/letstatement): zlepšuje modularitu a opakované použití. **Nechat** příkaz umožňuje potenciálně velmi složitý výraz přerušení na více částí, každá vázán na název a společně tvoří tyto části. A **nechat** příkaz lze také vytvořit uživatelem definované funkce a zobrazení (výrazy nad tabulek, jejichž výsledky vypadají nová tabulka). Výrazy vázány **nechat** příkaz může být skalární typu tabulky typu nebo uživatelem definované funkce (výrazy lambda).

Následující příklad vytvoří proměnné tabulky typu a používá ho v následných výrazu.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

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

[**připojení k**](https://docs.microsoft.com/azure/kusto/query/joinoperator): sloučí řádky dvou tabulek a vytvoří novou tabulku porovnáním hodnoty zadané sloupce z každé tabulky. Kusto podporuje celou řadu typů spojení: **fullouter**, **vnitřní**, **innerunique**, **leftanti**, **leftantisemi** , **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter** , **rightsemi**.

Následující příklad připojí pomocí vnitřního spojení dvou tabulek.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

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
> Použití **kde** a **projektu** operátory ke snížení počtu řádků a sloupců ve vstupní tabulky, před spojení. Pokud jedna tabulka je vždy menší než ten druhý, použijte ho jako (potrubím) levé spojení. Sloupce pro shodu join musí mít stejný název. Použití **projektu** operátor v případě potřeby přejmenujte sloupec v jedné z tabulek.

### <a name="serialize"></a>Serializace

[**serializace**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): serializuje řádku nastavíte tak, aby je možné použít funkce, které vyžadují serializovaná data, jako je třeba **row_number()**.

Následující dotaz bude úspěšné, protože je serializovaná data.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Sady řádků se také považují za serializovat, když je výsledkem: **řazení**, **horní**, nebo **rozsah** operátory, může volitelně následovat **projektu**, **projektu tokeny**, **rozšířit**, **kde**, **analyzovat**, **mvexpand**, nebo **trvat** operátory.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Dotazy napříč databázemi a napříč clusterem

[Dotazy napříč databázemi a napříč clusterem](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): můžete dotazovat databázi na stejném clusteru rekapitulací ho jako `database("MyDatabase").MyTable`. Můžete dotazovat databázi na vzdáleném clusteru tak, že na ni odkazuje jako `cluster("MyCluster").database("MyDatabase").MyTable`.

Následující dotaz je volána z jednoho clusteru a dotazování dat z `MyCluster` clusteru. Ke spuštění tohoto dotazu, použijte vlastní název clusteru a název databáze.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analýzy chování uživatelů

Tato část obsahuje prvky a dotazy, které ukazují, jak snadné je k analýze chování uživatelů Kusto.

### <a name="activitycountsmetrics-plugin"></a>modul plug-in activity_counts_metrics

[**modul plug-in activity_counts_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): vypočítá metriky užitečné aktivity (celkový počet hodnot, jednoznačného počtu hodnot, jednoznačného počtu hodnot a agregované jednoznačného počtu). Metriky se počítají pro každý časový interval, pak jsou porovnány a agregovat a s všech předchozích časových oken.

Následující dotaz analyzuje uživatelské přijetí výpočtem denní počty aktivity.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

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

### <a name="activityengagement-plugin"></a>modul plug-in activity_engagement

[**modul plug-in activity_engagement**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): vypočítá poměr engagement aktivity na základě ID sloupce přes posuvné okno časové osy. **modul plug-in activity_engagement** lze použít pro výpočet DAU WAU a MAU (denní, týdenní a měsíční aktivních uživatelů).

Následující dotaz vrátí poměr celkový počet jedinečných uživatelů na základě aplikace každý den ve srovnání s celkový počet jedinečných uživatelů v aplikaci, každý týden, na posouvá sedm dní.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Při výpočtu DAU/MAU, změňte endovým datům a přesunutí okno období (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>modul plug-in activity_metrics

[**modul plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): vypočítá metriky užitečné aktivity (počet jedinečných hodnot, jednoznačného počtu nových hodnot, míra uchování a četnost změn dat) podle aktuálního období okna oproti předchozí okno období.

Následující dotaz vypočítá míru četnosti změn a uchovávání dat pro danou datovou sadu.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>modul plug-in new_activity_metrics

[**modul plug-in new_activity_metrics**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): vypočítá metriky užitečné aktivity (počet jedinečných hodnot, jednoznačného počtu nových hodnot, míra uchování a četnost změn dat) pro kohorta noví uživatelé. Je podobný koncept tento modul plug-in [ **modulu plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), ale se zaměřuje na nové uživatele.

Následující dotaz vypočítá mírou uchování a četnost změn s oknem týden over týden pro nové kohorta uživatelů (uživatelů, které byly přijaty na první týden).

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>modul plug-in session_count

[**modul plug-in session_count**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): vypočítá počet relací na základě ID sloupce nad časovou osu.

Následující dotaz vrátí počet relací. Relace se považuje za aktivní, pokud ID uživatele se zobrazí alespoň jednou v časové rozmezí 100 časové úseky, zatímco vzhled back okno relace je 41 časové úseky.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

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

### <a name="funnelsequence-plugin"></a>modul plug-in funnel_sequence

[**modul plug-in funnel_sequence**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): vypočítá jednoznačného počtu uživatelů, kteří provedli posloupnost státy; ukazuje distribuci předchozí a další stavy, které vedly k nebo byly dodrženy pořadím.

Následující dotaz zobrazí událost, která se stane před a za všechny události tornádu 2007.

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>modul plug-in funnel_sequence_completion

[**modul plug-in funnel_sequence_completion**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): vypočítá Trychtýř dokončené pořadí kroků v rámci různých časových období.

Následující dotaz kontroluje dokončení trychtýřového grafu pořadí: `Hail -> Tornado -> Thunderstorm -> Wind` v "celkové" doby jednu hodinu, čtyři hodiny a jeden den (`[1h, 4h, 1d]`).

**\[**[**Klikněte na tlačítko Spustit dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

Tato část se věnuje [ **funkce**](https://docs.microsoft.com/azure/kusto/query/functions): opakovaně použitelné dotazy, které jsou uloženy na serveru. Funkce mohou být vyvolány v dotazech a dalších funkcí (rekurzivní funkce nejsou podporovány).

> [!NOTE]
> Funkce nelze vytvořit v clusteru help, která je jen pro čtení. Použijte vlastní cluster testu pro tuto část.

Následující příklad vytvoří funkci, která přebírá název stavu (`MyState`) jako argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Následující příklad volá funkci, která načte data pro Texas stavu.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Následující příklad odstraní funkce, který byl vytvořen v prvním kroku.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Další postup

[Referenční informace k jazyku dotaz Kusto](https://aka.ms/kustolangref)

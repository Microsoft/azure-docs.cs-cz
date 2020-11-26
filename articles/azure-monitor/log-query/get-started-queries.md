---
title: Začínáme s dotazy protokolu v Azure Monitor | Microsoft Docs
description: Tento článek popisuje kurz Začínáme s psaním dotazů protokolu v Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: a949c9b34e299e0dc4eccbb62f4b4ebb38d6ccb9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186707"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Začínáme s dotazy protokolů v Azure Monitoru

> [!NOTE]
> Pokud shromažďujete data alespoň z jednoho virtuálního počítače, můžete toto cvičení použít ve vlastním prostředí. Pokud ne, použijte naše ukázkové [prostředí](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), které obsahuje spoustu ukázkových dat.  Pokud už víte, jak dotazovat v KQL, ale stačí rychle vytvořit užitečné dotazy založené na typech prostředků, Projděte si [podokno uložené Ukázkové dotazy](./example-queries.md).

V tomto kurzu se naučíte psát dotazy protokolu v Azure Monitor. Naučíte se, jak:

- Principy struktury dotazů
- Seřadit výsledky dotazu
- Filtrovat výsledky dotazu
- Zadejte časový rozsah.
- Vyberte pole, která chcete zahrnout do výsledků.
- Definování a použití vlastních polí
- Agregace a seskupení výsledků

Kurz použití Log Analytics v Azure Portal najdete v tématu [Začínáme s Azure Monitor Log Analytics](./log-analytics-tutorial.md).<br>
Další podrobnosti o dotazech protokolu v Azure Monitor najdete v tématu [Přehled dotazů protokolu v Azure monitor](log-query-overview.md).

Sledujte níže uvedenou verzi videa v tomto kurzu:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Vytvoření nového dotazu

Dotazy mohou začít buď s názvem tabulky, nebo s příkazem *hledání* . Měli byste začít s názvem tabulky, protože definuje pro dotaz jasný rozsah a zvyšuje výkon dotazů a relevanci výsledků.

> [!NOTE]
> Dotazovací jazyk Kusto používaný službou Azure Monitor rozlišuje velká a malá písmena. Klíčová slova jazyka se obvykle zapisují malými písmeny. Při použití názvů tabulek nebo sloupců v dotazu se ujistěte, že používáte správný případ, jak je znázorněno v podokně schématu.

### <a name="table-based-queries"></a>Dotazy založené na tabulkách

Azure Monitor organizuje data protokolu v tabulkách, přičemž každá se skládá z několika sloupců. Všechny tabulky a sloupce se zobrazí v podokně schématu v Log Analytics na portálu Analytics. Identifikujte tabulku, na kterou vás zajímáte, a pak se podívejte na bitovou část dat:

```Kusto
SecurityEvent
| take 10
```

Výše uvedený dotaz vrátí 10 výsledků z tabulky *SecurityEvent* , a to bez konkrétního pořadí. To je velmi běžný způsob, jak se rychle podívat na tabulku a pochopit její strukturu a obsah. Pojďme se podívat, jak je sestavená:

* Dotaz začíná názvem tabulky *SecurityEvent* – Tato část definuje rozsah dotazu.
* Znak kanálu (|) odděluje příkazy, takže výstup prvního z nich se zadává do vstupu z následujícího příkazu. Můžete přidat libovolný počet prvků s potrubím.
* Následující kanál je příkaz **přijmout** , který vrátí konkrétní počet libovolných záznamů z tabulky.

Dotaz jsme mohli spustit i bez přidání `| take 10` -, který by byl stále platný, ale může vracet až 10 000 výsledků.

### <a name="search-queries"></a>Vyhledávací dotazy

Vyhledávací dotazy jsou méně strukturované a obecně se hodí pro hledání záznamů, které obsahují konkrétní hodnotu v některém z jejich sloupců:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Tento dotaz vyhledá v tabulce *SecurityEvent* záznamy, které obsahují frázi "kryptografie". Z těchto záznamů se vrátí a zobrazí 10 záznamů. Pokud tuto část vynecháme `in (SecurityEvent)` a právě ji spustíte `search "Cryptographic"` , bude hledání probíhat na *všech* tabulkách, což by mohlo trvat déle a být méně efektivní.

> [!WARNING]
> Vyhledávací dotazy jsou obvykle pomalejší než dotazy založené na tabulkách, protože musí zpracovávat více dat. 

## <a name="sort-and-top"></a>Seřadit a nahoru
I **když je** užitečné získat pár záznamů, výsledky se vyberou a zobrazují se v žádném pořadí. Chcete-li získat seřazené zobrazení, můžete **Řadit** podle preferovaného sloupce:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

To může vracet příliš mnoho výsledků, ale může také nějakou dobu trvat. Výše uvedený dotaz seřadí *celou* tabulku SecurityEvent podle sloupce TimeGenerated. Portál Analytics potom omezí zobrazení na zobrazení pouze záznamů 10 000. Tento přístup je samozřejmě neoptimální.

Nejlepším způsobem, jak získat pouze nejnovější 10 záznamů, je použít **horní**, který seřadí celou tabulku na straně serveru a potom vrátí nejvyšší záznamy:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Sestupnění je výchozí pořadí řazení, takže typicky vynecháme argument **DESC** . Výstup bude vypadat nějak takto:

![Prvních 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Kde: filtrování podmínky
Filtry označené podle jejich názvu filtrují data podle konkrétní podmínky. Toto je nejběžnější způsob, jak omezit výsledky dotazu na relevantní informace.

Chcete-li přidat filtr do dotazu, použijte operátor **WHERE** následovaný jednou nebo více podmínkami. Například následující dotaz vrátí pouze záznamy *SecurityEvent* , kde _úroveň_ rovná _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Při psaní podmínek filtrování můžete použít následující výrazy:

| Výraz | Popis | Příklad |
|:---|:---|:---|
| == | Kontrolovat rovnost<br>(rozlišuje velká a malá písmena) | `Level == 8` |
| =~ | Kontrolovat rovnost<br>(nerozlišuje velká a malá písmena) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Kontrolovat nerovnost<br>(oba výrazy jsou identické) | `Level != 4` |
| *and*, *or* | Požadováno mezi podmínkami| `Level == 16 or CommandLine != ""` |

Chcete-li filtrovat podle více podmínek, můžete buď použít **, a**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

nebo více kanálů **, kde** jeden po druhém je elementem:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Hodnoty mohou mít různé typy, takže je možná budete muset přetypovat na provedení porovnání se správným typem. Například sloupec *úrovně* SecurityEvent je typu String, takže je nutné jej přetypovat na číselný typ, jako je *int* nebo *Long*, předtím, než můžete použít numerické operátory: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Zadejte časový rozsah.

### <a name="time-picker"></a>Výběr času

Výběr času je vedle tlačítka Spustit a označuje, že dotazuje pouze záznamy za posledních 24 hodin. Toto je výchozí časový rozsah, který se použije u všech dotazů. Chcete-li získat pouze záznamy z poslední hodiny, vyberte možnost _poslední hodina_ a spusťte dotaz znovu.

![Výběr času](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtr času v dotazu

Můžete také definovat svůj vlastní časový rozsah přidáním filtru času do dotazu. Nejvhodnější je umístit filtr času hned za název tabulky: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Filtr ve výše uvedeném časovém intervalu  `ago(30m)` znamená "před 30 minutami", takže dotaz vrátí pouze záznamy z posledních 30 minut. Mezi další jednotky času patří dny (2D), minuty (25m) a sekundy (desítkách).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt a rozšířené: výběrové a výpočetní sloupce

Pomocí **projektu** můžete vybrat konkrétní sloupce, které chcete zahrnout do výsledků:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Předchozí příklad vygeneruje tento výstup:

![Dotaz na výsledky projektu](media/get-started-queries/project.png)

**Projekt** můžete také použít k přejmenování sloupců a definování nových. Následující příklad používá projekt k provedení následujících akcí:

* Vyberte pouze původní sloupce *počítač* a *TimeGenerated* .
* Zobrazí sloupec *aktivity* jako *EventDetails*.
* Vytvoří nový sloupec s názvem *kód události*. Funkce **substring ()** se používá k získání pouze prvních čtyř znaků z pole Activity.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**Rozšířené** zachová všechny původní sloupce v sadě výsledků a definuje další. Následující dotaz používá k přidání sloupce *kód události* sloupec **Extended** . Všimněte si, že tento sloupec se nemusí zobrazit na konci tabulky. v takovém případě budete muset rozbalit Podrobnosti záznamu a zobrazit ho.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Shrnutí: agregované skupiny řádků
Použijte **sumarizace** k identifikaci skupin záznamů, podle jednoho nebo více sloupců a použijte pro ně agregace. Nejběžnějším využitím **souhrnu** je *počet*, který vrací počet výsledků v každé skupině.

Následující dotaz zkontroluje všechny záznamy *výkonu* za poslední hodinu, seskupuje je podle *ObjectName* a spočítá záznamy v každé skupině: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Někdy je vhodné definovat skupiny podle více dimenzí. Každá jedinečná kombinace těchto hodnot definuje samostatnou skupinu:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Dalším běžným použitím je provádět matematické nebo statistické výpočty na každé skupině. Například následující příkaz vypočítá průměrnou *CounterValue* pro každý počítač:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Výsledky tohoto dotazu bohužel nejsou stejné, protože jsme provedli kombinaci různých čítačů výkonu. Aby bylo toto smysluplnější, doporučujeme vypočítat průměr zvlášť pro každou kombinaci *CounterName* a *počítače*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Shrnout podle sloupce s časem
Seskupení výsledků může být také založeno na časovém sloupci nebo jiné průběžné hodnotě. Jednoduché Shrnutí byste `by TimeGenerated` ale mohli vytvořit skupiny pro každou dobu v milisekundách v časovém rozsahu, protože se jedná o jedinečné hodnoty. 

Chcete-li vytvořit skupiny založené na souvislých hodnotách, je nejlepší rozdělit rozsah na spravovatelné jednotky pomocí **přihrádky**. Následující dotaz analyzuje záznamy *výkonu* , které měří volnou paměť (*k dispozici v MB*) na určitém počítači. Vypočítá průměrnou hodnotu každé období 1 hodiny za posledních 7 dnů:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Chcete-li, aby byl výstup jasný, vyberte ho a zobrazte ho jako časový graf. zobrazí se dostupná paměť v čase:

![Dotazování paměti v čase](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o použití řetězcových dat v dotazu protokolu s použitím [řetězců v Azure Monitorch](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)dotazech protokolu.
- Přečtěte si další informace o agregaci dat v dotazu protokolu s [pokročilými agregacemi v Azure Monitorch dotazech protokolu](/azure/data-explorer/write-queries#advanced-aggregations).
- Naučte se, jak propojit data z několika tabulek pomocí [spojení v Azure monitor dotazy protokolu](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Získejte dokumentaci k celému dotazovacímu jazyku Kusto v [Referenční příručce jazyka KQL](/azure/kusto/query/).
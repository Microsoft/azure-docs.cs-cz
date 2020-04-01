---
title: Začínáme s dotazy protokolů ve Službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kurz pro začínáme psát dotazy protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670175"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Začínáme s dotazy protokolů v Azure Monitoru

> [!NOTE]
> Toto cvičení můžete pracovat ve vlastním prostředí, pokud shromažďujete data alespoň z jednoho virtuálního počítače. Pokud ne, použijte naše [demo prostředí](https://portal.loganalytics.io/demo), které obsahuje spoustu ukázkových dat.

V tomto kurzu se naučíte psát dotazy protokolu v Azure Monitoru. To vás naučí, jak:

- Principy struktury dotazů
- Řazení výsledků dotazu
- Filtrovat výsledky dotazu
- Určení časového rozsahu
- Vyberte pole, která chcete zahrnout do výsledků.
- Definování a použití vlastních polí
- Souhrnné a skupinové výsledky

Kurz o používání Log Analytics na webu Azure Portal najdete v tématu [Začínáme s Azure Monitor Log Analytics](get-started-portal.md).<br>
Další podrobnosti o dotazech protokolu v Azure Monitoru najdete v [tématu Přehled dotazů protokolu v Azure Monitoru](log-query-overview.md).

Postupujte spolu s video verzi tohoto výukového programu níže:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Psaní nového dotazu
Dotazy mohou začínat názvem tabulky nebo příkazem *hledání.* Měli byste začít s názvem tabulky, protože definuje jasný obor pro dotaz a zlepšuje výkon dotazu a relevanci výsledků.

> [!NOTE]
> Dotazovací jazyk Kusto používaný službou Azure Monitor rozlišuje velká a malá písmena. Klíčová slova jazyka se obvykle zapisují malými písmeny. Při použití názvů tabulek nebo sloupců v dotazu, ujistěte se, že použít správný případ, jak je znázorněno na podokně schématu.

### <a name="table-based-queries"></a>Dotazy založené na tabulkách
Azure Monitor uspořádá data protokolu v tabulkách, každý se skládá z více sloupců. Všechny tabulky a sloupce se zobrazují v podokně schématu v Log Analytics na portálu Analytics. Identifikujte tabulku, která vás zajímá, a pak se podívejte na trochu dat:

```Kusto
SecurityEvent
| take 10
```

Výše uvedený dotaz vrátí 10 výsledků z tabulky *SecurityEvent* v žádném konkrétním pořadí. Jedná se o velmi běžný způsob, jak se podívat na stůl a pochopit jeho strukturu a obsah. Podívejme se, jak je postaven:

* Dotaz začíná názvem tabulky *SecurityEvent* - tato část definuje rozsah dotazu.
* Znak kanálu (|) odděluje příkazy, takže výstup prvního ve vstupu následujícího příkazu. Můžete přidat libovolný počet potrubím prvků.
* Za kanálem je příkaz **take,** který vrací určitý počet libovolných záznamů z tabulky.

Mohli bychom skutečně spustit `| take 10` dotaz i bez přidání - to by stále platné, ale může vrátit až 10 000 výsledků.

### <a name="search-queries"></a>Vyhledávací dotazy
Vyhledávací dotazy jsou méně strukturované a obecně vhodnější pro hledání záznamů, které obsahují určitou hodnotu v libovolném sloupci:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Tento dotaz hledá v tabulce *SecurityEvent* záznamy obsahující frázi "Kryptografická". Z těchto záznamů bude vráceno a zobrazeno 10 záznamů. Pokud vynecháme `in (SecurityEvent)` část a `search "Cryptographic"`jen spustit , hledání půjde přes *všechny* tabulky, které by trvalo déle a být méně efektivní.

> [!WARNING]
> Vyhledávací dotazy jsou obvykle pomalejší než dotazy založené na tabulce, protože mají ke zpracování více dat. 

## <a name="sort-and-top"></a>Řazení a shora
Zatímco **take** je užitečné získat několik záznamů, výsledky jsou vybrány a zobrazeny v žádném konkrétním pořadí. Chcete-li získat seřazené zobrazení, můžete **řadit** podle upřednostňovaného sloupce:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

To by mohlo vrátit příliš mnoho výsledků i když a může také nějakou dobu trvat. Výše uvedený dotaz seřadí *celou* tabulku SecurityEvent podle sloupce TimeGenerated. Portál Analytics pak omezí zobrazení tak, aby zobrazoval pouze 10 000 záznamů. Tento přístup samozřejmě není optimální.

Nejlepší způsob, jak získat pouze posledních 10 záznamů, je použít **horní**, který seřadí celou tabulku na straně serveru a vrátí horní záznamy:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Sestupně je výchozí pořadí řazení, takže obvykle vynecháme argument **desc.** Výstup bude vypadat takto:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Kde: filtrování za podmínky
Filtry, jak je uvedeno jejich název, filtrovat data podle určité podmínky. Toto je nejběžnější způsob, jak omezit výsledky dotazu na relevantní informace.

Chcete-li do dotazu přidat filtr, použijte operátor **where** následovaný jednou nebo více podmínkami. Například následující dotaz vrátí pouze *SecurityEvent* záznamy, kde _úroveň_ rovná _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Při psaní podmínek filtru můžete použít následující výrazy:

| Expression | Popis | Příklad |
|:---|:---|:---|
| == | Zkontrolovat rovnost<br>(malá a velká písmena) | `Level == 8` |
| =~ | Zkontrolovat rovnost<br>(malá a velká písmena) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Zkontrolovat nerovnost<br>(oba výrazy jsou identické) | `Level != 4` |
| *a*, *nebo* | Povinné mezi podmínkami| `Level == 16 or CommandLine != ""` |

Chcete-li filtrovat podle více podmínek, můžete použít **a:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

nebo potrubí **více, kde** prvky jeden po druhém:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Hodnoty mohou mít různé typy, takže je možná budete muset přetypovat, abyste mohli provést porovnání se správným typem. Například sloupec *Úroveň* události SecurityEvent je typu String, takže jej musíte přetypovat na číselný typ, například *int* nebo *long*, abyste na něm mohli použít číselné operátory:`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Určení časového rozsahu

### <a name="time-picker"></a>Výběr času
Výběr času je vedle tlačítka Spustit a označuje, že se dotazujeme pouze na záznamy za posledních 24 hodin. Toto je výchozí časový rozsah použitý pro všechny dotazy. Chcete-li získat pouze záznamy za poslední hodinu, vyberte _možnost Poslední hodina_ a spusťte dotaz znovu.

![Výběr času](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Časový filtr v dotazu
Můžete také definovat vlastní časový rozsah přidáním časového filtru do dotazu. Nejlepší je umístit časový filtr bezprostředně za název tabulky: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Ve výše uvedeném časovém filtru `ago(30m)` znamená "před 30 minutami", takže tento dotaz vrací pouze záznamy z posledních 30 minut. Mezi další časové jednotky patří dny (2d), minuty (25 m) a sekundy (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt a rozšíření: výběr a výpočet sloupců
Pomocí **projektu** vyberte konkrétní sloupce, které chcete zahrnout do výsledků:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Předchozí příklad generuje tento výstup:

![Výsledky projektu dotazu](media/get-started-queries/project.png)

**Projekt** můžete také použít k přejmenování sloupců a definování nových. Následující příklad používá projekt k provedení následujících akcí:

* Vyberte pouze původní sloupce *Computer* a *TimeGenerated.*
* Přejmenujte sloupec *Aktivita* na *Podrobnosti události*.
* Vytvořte nový sloupec s názvem *EventCode*. Funkce **substring()** se používá k získání pouze prvních čtyř znaků z pole Aktivita.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** zachová všechny původní sloupce v sadě výsledků a definuje další sloupce. Následující dotaz používá **extend** k přidání sloupce *EventCode.* Všimněte si, že tento sloupec nemusí zobrazit na konci tabulky výsledky v takovém případě budete muset rozbalit podrobnosti o záznamu k jeho zobrazení.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Sumarizovat: agregovat skupiny řádků
Souhrn **použijte** k identifikaci skupin záznamů podle jednoho nebo více sloupců a použít na ně agregace. Nejběžnější použití **souhrnu** je *počet*, který vrací počet výsledků v každé skupině.

Následující dotaz zkontroluje všechny záznamy *Perf* za poslední hodinu, seskupí je podle *objektu ObjectName*a spočítá záznamy v každé skupině: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Někdy má smysl definovat skupiny podle více dimenzí. Každá jedinečná kombinace těchto hodnot definuje samostatnou skupinu:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Dalším běžným použitím je provádění matematických nebo statistických výpočtů pro každou skupinu. Například následující vypočítá průměrnou *hodnotu CounterValue* pro každý počítač:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Bohužel výsledky tohoto dotazu jsou bezvýznamné, protože jsme smíchali různé čítače výkonu. Aby to bylo smysluplnější, měli bychom vypočítat průměr zvlášť pro každou kombinaci *CounterName* a *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Sumarizovat podle časového sloupce
Seskupování výsledků může být také založeno na časovém sloupci nebo jiné průběžné hodnotě. Jednoduše sumarizovat `by TimeGenerated` by však vytvořit skupiny pro každý jednotlivý milisekundv časovém rozmezí, protože se jedná o jedinečné hodnoty. 

Chcete-li vytvořit skupiny založené na průběžných hodnotách, je nejlepší rozdělit rozsah na spravovatelné jednotky pomocí **přihrádky**. Následující dotaz analyzuje záznamy *Perf,* které měří volné paměti *(Dostupné MBytes)* v určitém počítači. Vypočítá průměrnou hodnotu za každé 1 hodinové období za posledních 7 dní:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Chcete-li, aby byl výstup jasnější, vyberte jej zobrazit jako časový graf zobrazující dostupnou paměť v průběhu času:

![Paměť dotazu v průběhu času](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Další kroky

- Další informace o používání dat řetězce v protokolu dotazu s [práce s řetězci v dotazech protokolu Azure Monitor](string-operations.md).
- Další informace o agregaci dat v protokolu dotazu s [rozšířené agregace v dotazech protokolu Azure Monitor](advanced-aggregations.md).
- Zjistěte, jak spojit data z více tabulek pomocí [spojení v dotazech protokolu Azure Monitor](joins.md).
- Získejte dokumentaci k celému dotazovacímu jazyku Kusto v [referenční příručce k jazyku KQL](/azure/kusto/query/).

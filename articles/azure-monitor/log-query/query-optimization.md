---
title: Optimalizace dotazů protokolů v Azure Monitoru
description: Doporučené postupy pro optimalizaci dotazů protokolů v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: c32731ce2de2b0f886a1e21ee8ccad3996e395eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480262"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimalizace dotazů protokolů v Azure Monitoru
Protokoly monitorování Azure používá [Azure Data Explorer (ADX)](/azure/data-explorer/) k ukládání dat protokolu a spouštění dotazů pro analýzu dat. Vytváří, spravuje a udržuje clustery ADX pro vás a optimalizuje je pro úlohy analýzy protokolu. Když spustíte dotaz, je optimalizován a směrován do příslušného clusteru ADX, který ukládá data pracovního prostoru. Protokoly monitorování Azure i Azure Data Explorer používá mnoho mechanismů automatické optimalizace dotazů. Zatímco automatické optimalizace poskytují významné zvýšení, jsou v některých případech, kdy můžete výrazně zlepšit výkon dotazu. Tento článek vysvětluje aspekty výkonu a několik technik k jejich opravě.

Většina technik jsou běžné pro dotazy, které se spouštějí přímo v Azure Data Explorer a Azure Monitor protokoly, i když existuje několik jedinečných azure monitor protokoly úvahy, které jsou popsány zde. Další tipy pro optimalizaci Průzkumníka dat Azure najdete v [tématu Doporučené postupy pro dotaz](/azure/kusto/query/best-practices).

Optimalizované dotazy budou:

- Spouštět rychleji, zkrátit celkovou dobu trvání spuštění dotazu.
- Mají menší šanci na škrticí nebo zamítnuté.

Zvláštní pozornost byste měli věnovat dotazům, které se používají pro opakované a prasklé použití, jako jsou řídicí panely, výstrahy, logic aplikace a Power BI. Dopad neefektivní dotaz v těchto případech je značný.

## <a name="query-performance-pane"></a>Podokno výkonu dotazu
Po spuštění dotazu v Log Analytics klikněte na šipku dolů nad výsledky dotazu zobrazíte podokno výkonu dotazu, které zobrazuje výsledky několika ukazatelů výkonu pro dotaz. Tyto ukazatele výkonnosti jsou popsány v následující části.

![Podokno výkonu dotazu](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indikátory výkonu dotazu

Pro každý spouštěný dotaz jsou k dispozici následující ukazatele výkonu dotazu:

- [Celkový počet procesorů](#total-cpu): Celkové výpočetní prostředky používané ke zpracování dotazu ve všech výpočetních uzlech. Představuje čas používaný pro výpočet, analýzu a načítání dat. 

- [Data použitá pro zpracovaný dotaz](#data-used-for-processed-query): Celková data, ke kterým byl přístup ke zpracování dotazu. Ovlivněno velikostí cílové tabulky, použitým časovým rozsahem, použitými filtry a počtem odkazovaných sloupců.

- [Časové rozpětí zpracovaného dotazu](#time-span-of-the-processed-query): Mezera mezi nejnovějšími a nejstaršími daty, ke kterým bylo přistupováno ke zpracování dotazu. Ovlivněno explicitním časovým rozsahem určeným pro dotaz.

- [Stáří zpracovaných dat](#age-of-processed-data): Mezera mezi nyní a nejstarší data, která byla přístupná ke zpracování dotazu. To velmi ovlivňuje efektivitu načítání dat.

- [Počet pracovních prostorů](#number-of-workspaces): Kolik pracovních prostorů bylo při přístupu během zpracování dotazu z důvodu implicitního nebo explicitního výběru.

- [Počet oblastí](#number-of-regions): Kolik oblastí bylo přistupováno během zpracování dotazů na základě implicitního nebo explicitního výběru pracovních prostorů. Dotazy s více oblastmi jsou mnohem méně efektivní a ukazatele výkonu představují částečné pokrytí.

- [Paralelismus](#parallelism): Označuje, kolik systém byl schopen spustit tento dotaz na více uzlů. Relevantní pouze pro dotazy, které mají vysokou spotřebu procesoru. Ovlivněno použitím specifických funkcí a operátorů.


## <a name="total-cpu"></a>Celkový procesor
Skutečný výpočetní procesor, který byl investován do zpracování tohoto dotazu ve všech uzlech zpracování dotazu. Vzhledem k tomu, že většina dotazů jsou spouštěny na velký počet uzlů, to bude obvykle mnohem větší než doba trvání dotazu skutečně trvalo spustit. 

Doba zpracování dotazu je vynaložena na:
- Načítání dat – načítání starých dat bude spotřebovávat více času než načítání posledních dat.
- Zpracování dat – logika a vyhodnocení dat. 

Jiné než čas strávený v uzlech zpracování dotazů, je další čas, který je vynakládána protokoly Azure Monitor: ověření uživatele a ověření, že mají povoleno přístup k těmto datům, vyhledejte úložiště dat, analyzovat dotaz a přidělit zpracování dotazu Uzly. Tato doba není zahrnuta v celkovém čase procesoru dotazu.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Včasné filtrování záznamů před použitím vysokých funkcí procesoru

Některé příkazy a funkce dotazu jsou velké ve své spotřebě procesoru. To platí zejména pro příkazy, které analyzují JSON a XML nebo extrahují složité regulární výrazy. Taková analýza může dojít explicitně prostřednictvím [parse_json()](/azure/kusto/query/parsejsonfunction) nebo [parse_xml()](/azure/kusto/query/parse-xmlfunction) funkce nebo implicitně při odkazování na dynamické sloupce.

Tyto funkce spotřebovávají procesor v poměru k počtu řádků, které zpracovávají. Nejúčinnější optimalizace je přidat, kde podmínky v rané fázi dotazu, který můžete odfiltrovat co nejvíce záznamů, jak je to možné před spuštěním funkce náročné na procesor.

Například následující dotazy vytvářejí přesně stejný výsledek, ale druhý je zdaleka nejúčinnější jako podmínka [where](/azure/kusto/query/whereoperator) před analýzou vylučuje mnoho záznamů:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Vyhněte se použití vyhodnocených, kde klauzule

Dotazy, které obsahují [kde](/azure/kusto/query/whereoperator) klauzule na vyhodnocený sloupec spíše než na sloupce, které jsou fyzicky k dispozici v datové sadě ztratit účinnost. Filtrování na vyhodnocených sloupcích zabraňuje některým optimalizacím systému při zpracování velkých sad dat.
Například následující dotazy vytvořit přesně stejný výsledek, ale druhý je efektivnější jako [kde](/azure/kusto/query/whereoperator) podmínka odkazuje na vestavěné sloupce

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Používejte příkazy efektivní agregace a dimmentions ve shrnutí a spojení

Zatímco některé agregační příkazy jako [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)a [avg()](/azure/kusto/query/avg-aggfunction) mají nízký dopad procesoru kvůli jejich logice, jiné jsou složitější a zahrnují heuristiku a odhady, které umožňují jejich efektivní provádění. Například [dcount()](/azure/kusto/query/dcount-aggfunction) používá algoritmus HyperLogLog poskytnout blízký odhad na odlišný počet velkých sad dat bez skutečného počítání každou hodnotu; funkce percentilu provádějí podobné aproximace pomocí nejbližšího algoritmu percentilu pořadí. Několik příkazů obsahuje volitelné parametry pro snížení jejich dopadu. Například [makeset()](/azure/kusto/query/makeset-aggfunction) funkce má volitelný parametr definovat maximální velikost sady, což výrazně ovlivňuje procesor a paměť.

[Připojit](/azure/kusto/query/joinoperator?pivots=azuremonitor) a [sumarizovat](/azure/kusto/query/summarizeoperator) příkazy může způsobit vysoké využití procesoru při zpracování velké sady dat. Jejich složitost přímo souvisí s počtem možných hodnot, označovaných jako *mohutnost* `by` , sloupců, které používají jako souhrn nebo jako atributy spojení. Vysvětlení a optimalizaci spojení a shrnutí najdete v jejich dokumentačních článcích a optimalizačních tipech.

Například následující dotazy vytvářejí přesně stejný výsledek, protože **CounterPath** je vždy 1:1 mapován na **CounterName** a **ObjectName**. Druhý je efektivnější, protože dimenze agregace je menší:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Spotřeba procesoru může být také ovlivněna podmínkami nebo rozšířenými sloupci, které vyžadují intenzivní práci s počítačem. Všechny porovnání triviální řetězce, například [equal ==](/azure/kusto/query/datatypes-string-operators) a [startswith](/azure/kusto/query/datatypes-string-operators) mají zhruba stejný dopad procesoru, zatímco rozšířené textové shody mají větší dopad. Konkrétně [has](/azure/kusto/query/datatypes-string-operators) operátor je efektivnější, že [obsahuje](/azure/kusto/query/datatypes-string-operators) operátor. Z důvodu techniky zpracování řetězců je efektivnější hledat řetězce, které jsou delší než čtyři znaky než krátké řetězce.

Například následující dotazy vytvářejí podobné výsledky v závislosti na zásadách pojmenování počítače, ale druhý je efektivnější:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Tento indikátor představuje pouze procesor z okamžitého clusteru. V dotazu s více oblastmi by představoval pouze jednu z oblastí. V dotazu s více pracovními prostory nemusí obsahovat všechny pracovní prostory.


## <a name="data-used-for-processed-query"></a>Data použitá pro zpracovaný dotaz

Kritickým faktorem při zpracování dotazu je objem dat, který je naskenován a použit pro zpracování dotazu. Azure Data Explorer používá agresivní optimalizace, které výrazně snižují objem dat ve srovnání s jinými datovými platformami. Přesto existují kritické faktory v dotazu, které mohou mít vliv na svazek dat, který se používá.

V protokolech monitorování Azure se sloupec **TimeGenerated** používá jako způsob indexování dat. Omezení **TimeGenerated** hodnoty co nejužší rozsah, jak je to možné provede významné zlepšení výkonu dotazu tím, že výrazně omezí množství dat, která má být zpracována.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Vyhněte se zbytečnému používání vyhledávacích a odborových operátorů

Dalším faktorem, který zvyšuje data, která je proces je použití velkého počtu tabulek. K tomu `search *` obvykle `union *` dochází, když a příkazy jsou používány. Tyto příkazy vynucují systému vyhodnotit a skenovat data ze všech tabulek v pracovním prostoru. V některých případech mohou být stovky tabulek v pracovním prostoru. Snažte se co nejvíce vyhnout pomocí "hledat *" nebo jakékoliv hledání bez vymezení do konkrétní tabulky.

Například následující dotazy vytvářejí přesně stejný výsledek, ale poslední z nich je zdaleka nejúčinnější:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Přidání časných filtrů do dotazu

Další metodou ke snížení objemu dat je mít [kde](/azure/kusto/query/whereoperator) podmínky v rané fázi dotazu. Platforma Azure Data Explorer obsahuje mezipaměť, která mu umožňuje vědět, které oddíly obsahují data, která jsou relevantní pro konkrétní podmínku. Například pokud dotaz `where EventID == 4624` obsahuje pak by distribuovat dotaz pouze do uzlů, které zpracovávají oddíly s odpovídající události.

Následující příklad dotazů vytvořit přesně stejný výsledek, ale druhý je efektivnější:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Snížení počtu načtených sloupců

Vzhledem k tomu, že Azure Data Explorer je sloupcové úložiště dat, načítání každého sloupce je nezávislé na ostatních. Počet načtených sloupců přímo ovlivňuje celkový objem dat. Měli byste zahrnout pouze sloupce ve výstupu, které jsou potřebné [sumarzací](/azure/kusto/query/summarizeoperator) výsledků nebo [promítáním](/azure/kusto/query/projectoperator) konkrétních sloupců. Azure Data Explorer má několik optimalizací ke snížení počtu načtených sloupců. Pokud zjistí, že sloupec není potřeba, například pokud není odkazováno v příkazu [summarize,](/azure/kusto/query/summarizeoperator) nebude načíst.

Například druhý dotaz může zpracovat třikrát více dat, protože potřebuje načíst ne jeden sloupec, ale tři:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Časové rozpětí zpracovaného dotazu

Všechny protokoly v protokolech monitorování Azure jsou rozděleny podle sloupce **TimeGenerated.** Počet oddílů, které jsou přístupné přímo souvisí s časovým rozpětím. Snížení časového rozsahu je nejúčinnější způsob, jak zajistit spuštění dotazu výzvu.

Časový rozsah lze nastavit pomocí voliče časového rozsahu na obrazovce Log Analytics, jak je popsáno v [oboru dotazu protokolu a časového rozsahu v Azure Monitor Log Analytics](scope.md#time-range). Toto je doporučená metoda, protože vybraný časový rozsah je předán back-endu pomocí metadat dotazu. 

Alternativní metoda je explicitně zahrnout [where](/azure/kusto/query/whereoperator) condition on **TimeGenerated** v dotazu. Tuto metodu byste měli použít, protože zajišťuje, že časový rozsah je pevný, i když je dotaz použit z jiného rozhraní.
Měli byste zajistit, že všechny části dotazu mají **Filtry TimeGenerated.** Pokud dotaz obsahuje dílčí dotazy načítání dat z různých tabulek nebo stejné tabulky, každý musí obsahovat své [vlastní, kde](/azure/kusto/query/whereoperator) podmínka.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Ujistěte se, že všechny dílčí dotazy mají filtr TimeGenerated.

Například v následujícím dotazu, zatímco tabulka **Perf** bude zkontrolována pouze za poslední den, bude tabulka **Prezenční signál** zkontrolována pro celou svou historii, což může být až dva roky:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Běžným případem, kdy k takové chybě dojde, je, když se k nalezení nejnovějšího výskytu používá [arg_max().](/azure/kusto/query/arg-max-aggfunction) Například:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

To lze snadno opravit přidáním časového filtru do vnitřního dotazu:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Dalším příkladem této chyby je při provádění filtrování časového oboru těsně po [sjednocení](/azure/kusto/query/unionoperator?pivots=azuremonitor) přes několik tabulek. Při provádění unie by měl být každý dílčí dotaz vymezen. Příkaz [let](/azure/kusto/query/letstatement) můžete použít k zajištění konzistence oboru.

Například následující dotaz prohledá všechna data v *tabulkách Prezenční signál* a *Perf,* nejen za poslední 1 den:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Tento dotaz by měl být stanoven takto:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Omezení měření časového rozpětí

Měření je vždy větší než skutečný čas určený. Pokud je například filtr na dotazu 7 dní, může systém prohledat 7,5 nebo 8,1 dne. Důvodem je, že systém je rozdělení dat do bloků v proměnné velikosti. Aby bylo zajištěno, že všechny relevantní záznamy jsou naskenovány, skenuje celý oddíl, který by mohl pokrýt několik hodin a dokonce i více než jeden den.

Existuje několik případů, kdy systém nemůže poskytnout přesné měření časového rozsahu. K tomu dochází ve většině případů, kdy je rozsah dotazu kratší než jeden den nebo v dotazech s více pracovními prostory.


> [!IMPORTANT]
> Tento indikátor zobrazuje pouze data zpracovaná v bezprostředním clusteru. V dotazu s více oblastmi by představoval pouze jednu z oblastí. V dotazu s více pracovními prostory nemusí obsahovat všechny pracovní prostory.

## <a name="age-of-processed-data"></a>Stáří zpracovaných dat
Azure Data Explorer používá několik úrovní úložiště: v paměti, místní SSD disky a mnohem pomalejší objekty BLOB Azure. Čím novější data, tím vyšší je pravděpodobnost, že je uložena v výkonnější vrstvě s menší latencí, což snižuje dobu trvání dotazu a procesor. Kromě samotných dat má systém také mezipaměť pro metadata. Čím starší data, tím menší je pravděpodobnost, že jejich metadata budou v mezipaměti.

Zatímco některé dotazy vyžadují použití starých dat, existují případy, kdy jsou stará data použita omylem. K tomu dochází, když jsou dotazy provedeny bez poskytnutí časového rozsahu v meta-datech a ne všechny odkazy na tabulky obsahují filtr ve **sloupci TimeGenerated.** V těchto případech systém prohledá všechna data uložená v této tabulce. Pokud je uchovávání dat dlouhé, může zahrnovat dlouhé časové rozsahy a tedy data, která jsou stejně stará jako doba uchovávání dat.

Takové případy mohou být například:

- Není nastavení časového rozsahu v Log Analytics s dílčí dotaz, který není omezen. Viz příklad výše.
- Použití rozhraní API bez volitelných parametrů časového rozsahu.
- Použití klienta, který nevynucuje časový rozsah, jako je konektor Power BI.

Viz příklady a poznámky v prostupné části, protože jsou také relevantní v tomto případě.

## <a name="number-of-regions"></a>Počet regionů
Existuje několik situací, kdy může být spuštěn jeden dotaz v různých oblastech:

- Pokud je explicitně uvedeno několik pracovních prostorů a jsou umístěny v různých oblastech.
- Když dotaz s rozsahem prostředků načítá data a data jsou uložena ve více pracovních prostorech, které jsou umístěny v různých oblastech.

Spuštění dotazu mezi oblastmi vyžaduje, aby systém serializovat a přenášet v back-endu velké bloky zprostředkující data, která jsou obvykle mnohem větší než konečné výsledky dotazu. Také omezuje schopnost systému provádět optimalizace, heuristiky a využívat mezipaměti.
Pokud neexistuje žádný skutečný důvod pro skenování všech těchto oblastí, měli byste upravit obor tak, aby pokrýval méně oblastí. Pokud je obor prostředků minimalizován, ale stále se používá mnoho oblastí, může k tomu dojít z důvodu chybné konfigurace. Například protokoly auditu a nastavení diagnostiky jsou odesílány do různých pracovních prostorů v různých oblastech nebo existuje více konfigurací nastavení diagnostiky. 

> [!IMPORTANT]
> Při spuštění dotazu v několika oblastech, cpu a měření dat nebude přesné a bude představovat měření pouze v jedné z oblastí.

## <a name="number-of-workspaces"></a>Počet pracovních prostorů
Pracovní prostory jsou logické kontejnery, které se používají k oddělení a správě dat protokolů. Back-end optimalizuje umístění pracovního prostoru na fyzických clusterech ve vybrané oblasti.

Použití více pracovních prostorů může být výsledkem: 

- Kde je výslovně uvedeno několik pracovních prostorů.
- Když dotaz s rozsahem prostředků načítá data a data jsou uložena ve více pracovních prostorech.
 
Provádění dotazů mezi oblastmi a mezi clustery vyžaduje, aby systém serializoval a přenášel v back-endu velké bloky zprostředkujících dat, které jsou obvykle mnohem větší než konečné výsledky dotazu. Omezuje také systémovou schopnost provádět optimalizace, heuristiky a využití mezipamětí.

> [!IMPORTANT]
> V některých scénářích více pracovních prostorů cpu a data měření nebude přesné a bude představovat měření pouze několik pracovních prostorů.

## <a name="parallelism"></a>Paralelnost
Protokoly monitorování Azure používá velké clustery Průzkumníka dat Azure ke spouštění dotazů a tyto clustery se liší ve škálování, potenciálně získat až desítky výpočetních uzlů. Systém automaticky škáluje clustery podle logiky umístění pracovního prostoru a kapacity.

Chcete-li efektivně spustit dotaz, je rozdělen a distribuován y výpočetníu uzlů na základě dat, která je požadována pro jeho zpracování. Existují situace, kdy systém nemůže tak efektivně provést. To může vést k dlouhé trvání dotazu. 

Mezi chování dotazů, které mohou omezit paralelismus, patří:

- Použití serializace a funkce okna, jako je například [operátor serializovat](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)a funkce [řádku.](/azure/kusto/query/rowcumsumfunction) V některých z těchto případů lze použít funkce časové řady a analýzy uživatelů. K neefektivní serializaci může dojít také v případě, že na konci dotazu nejsou použity následující operátory: [rozsah](/azure/kusto/query/rangeoperator), [řazení](/azure/kusto/query/sortoperator), [řazení](/azure/kusto/query/orderoperator), [horní](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    Použití funkce agregace [dcount()](/azure/kusto/query/dcount-aggfunction) vynutí, aby systém měl centrální kopii odlišných hodnot. Pokud je měřítko dat vysoké, zvažte použití volitelných parametrů funkce dcount ke snížení přesnosti.
-    V mnoha případech operátor [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) snižuje celkový paralelismus. Zkontrolujte náhodné spojení jako alternativu, pokud je výkon problematický.
-    V dotazech oboru prostředků může předběžné spuštění kontrolr RBAC přetrvávat v situacích, kdy je velmi velký počet přiřazení RBAC. To může vést k delším kontrolám, které by vedly k nižšímu paralelismu. Například dotaz se spustí na předplatné, kde existují tisíce prostředků a každý prostředek má mnoho přiřazení rolí na úrovni prostředků, nikoli na předplatné nebo skupiny prostředků.
-    Pokud dotaz zpracovává malé bloky dat, jeho paralelismus bude nízký, protože systém jej nebude šířit mezi mnoho výpočetních uzlů.



## <a name="next-steps"></a>Další kroky

- [Referenční dokumentace pro dotazovací jazyk Kusto](/azure/kusto/query/).

---
title: Optimalizace dotazů protokolu v Azure Monitor
description: Osvědčené postupy pro optimalizaci dotazů protokolu v Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/24/2019
ms.openlocfilehash: 32eee22aa8e9b707d404cb85db6b7fae90d11987
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589841"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimalizace dotazů protokolu v Azure Monitor
Protokoly Azure Monitor používají k ukládání a správě protokolů a dotazů službu [Azure Průzkumník dat (ADX)](/azure/data-explorer/) . Vytváří, spravuje a udržuje clustery ADX za vás a optimalizuje je pro vaši úlohu analýzy protokolů. Když spustíte dotaz, bude optimalizován a směrován do příslušného clusteru ADX, který ukládá pracovní prostor. Protokoly Azure Monitor a Azure Průzkumník dat využívají řadu automatických mechanismů optimalizace dotazů. I když automatické optimalizace poskytují výrazné zvýšení, jsou v některých případech, kdy můžete výrazně vylepšit výkon dotazů. V tomto článku se dozvíte o požadavcích na výkon a o některých technikech jejich řešení.

Většina technik je společná pro dotazy, které se spouštějí přímo v Azure Průzkumník dat a Azure Monitor protokoly, i když existuje několik jedinečných důležitých Azure Monitor protokolů, které jsou zde popsány. Další tipy k optimalizaci pro Azure Průzkumník dat najdete v tématu [osvědčené postupy pro dotazy](/azure/kusto/query/best-practices).

Optimalizované dotazy budou:

- Rychlejší spuštění, zkrácení celkové doby trvání provádění dotazu.
- Mít menší pravděpodobnost omezení nebo odmítnutí.

Měli byste věnovat zvláštní pozornost dotazům, které se používají pro opakující se se využití a shluky, jako jsou řídicí panely a Power BI. Dopad neúčinného dotazu v těchto případech je podstatný.

## <a name="query-performance-pane"></a>Podokno výkon dotazu
Po spuštění dotazu v Log Analytics klikněte na šipku dolů nad výsledky dotazu, abyste zobrazili podokno výkon dotazu, které zobrazuje výsledky několika ukazatelů výkonu pro dotaz. Tyto indikátory výkonu jsou popsány v následující části.

![Podokno výkon dotazu](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Ukazatele výkonu dotazů

Pro každý spuštěný dotaz jsou k dispozici následující indikátory výkonu dotazů:

- [Total CPU](#total-cpu): celkový počet výpočtů používaných pro zpracování dotazu napříč všemi výpočetními uzly. Představuje čas, který se používá pro výpočet, analýzu a načítání dat. 

- [Data použitá pro zpracovaný dotaz](#data-used-for-query-processing): celková data, ke kterým byl přidaný dotaz zpracován. Ovlivněno velikostí cílové tabulky, použitým časovým rozsahem, použitými filtry a počtem odkazovaných sloupců.

- [Časové období zpracovávaného dotazu](#time-range-of-the-data-processed): mezera mezi nejnovějšími a nejstarší daty, ke kterým byl přidaný dotaz zpracován. Ovlivněno explicitním časovým rozsahem dotazu a použitými filtry. Může být větší než explicitní časový rozsah z důvodu dělení dat.

- [Stáří zpracovaných dat](#age-of-the-oldest-data-used): mezera mezi nyní a nejstarší daty, ke kterým byl přidaný dotaz zpracován. Má velmi vliv na efektivitu načítání dat.

- [Počet pracovních prostorů](#number-of-workspaces): Kolik pracovních prostorů bylo během zpracování dotazu k dispozici v důsledku implicitního nebo explicitního výběru.

- [Počet oblastí](#number-of-regions): kolik oblastí bylo během zpracování dotazu k dispozici v důsledku implicitního nebo explicitního výběru pracovních prostorů. Dotazy na více oblastí jsou mnohem méně efektivní a indikátory výkonu představují částečnou pokrytí.

- [Paralelismus](#parallelism): udává, kolik systému bylo možné spustit tento dotaz na více uzlech. Týká se pouze dotazů, které mají vysokou spotřebu procesoru. Ovlivněno používáním určitých funkcí a operátorů.


## <a name="total-cpu"></a>PROCESOR celkem
Skutečný výpočetní procesor, který byl investoval do zpracování tohoto dotazu napříč všemi uzly zpracování dotazů. Vzhledem k tomu, že většina dotazů je prováděna na velkých číslech uzlů, bude obvykle mnohem větší než doba, jakou bude dotaz skutečně proveden. 

Doba zpracování dotazu je strávená na:
- Načítání dat – načtení starých dat bude spotřebovávat víc času než při načítání nedávných dat.
- Zpracování dat – logika a vyhodnocení dat. 

Jiné než čas strávený v uzlech zpracování dotazů je Azure Monitor protokolů k dispozici další čas: ověření uživatele a ověření, že mají povolen přístup k těmto datům, vyhledejte úložiště dat, analyzujte dotaz a přidělte zpracování dotazu. sortiment. Tento čas není zahrnutý v dotazu na celkový čas procesoru.

Některé příkazy a funkce dotazu jsou v jejich spotřebě procesoru těžké. To platí hlavně pro příkazy, které analyzují JSON a XML nebo extrahují komplexní regulární výrazy. Tato analýza může být provedena explicitně prostřednictvím funkcí [parse_json ()](/azure/kusto/query/parsejsonfunction) nebo [parse_xml ()](/azure/kusto/query/parse-xmlfunction) nebo implicitně při odkazování na dynamické sloupce.

Tyto funkce spotřebovávají procesor v poměru k počtu zpracovávaných řádků. Nejúčinnější optimalizací je přidání podmínek WHERE do začátku dotazu, který může vyfiltrovat co nejvíc záznamů, než se spustí funkce náročné na procesor.

Například následující dotazy vydávají přesně stejný výsledek, ale druhá z nich je mnohem nejúčinnější jako podmínka [WHERE]() před analýzou vyloučení mnoha záznamů:

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
```

Dotazy, které obsahují klauzule [WHERE](/azure/kusto/query/whereoperator) u vyhodnoceného sloupce a nikoli pro sloupce, které jsou fyzicky přítomny v datové sadě, ztratí efektivitu. Filtrování u vyhodnocených sloupců brání optimalizaci systému při zpracování velkých sad dat.
Například následující dotazy vytvářejí přesně stejný výsledek, ale druhý je efektivnější, protože podmínka [WHERE](/azure/kusto/query/whereoperator) odkazuje na předdefinovaný sloupec.

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

Zatímco některé agregační příkazy jako [Max ()](/azure/kusto/query/max-aggfunction), [Sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)a [AVG ()](/azure/kusto/query/avg-aggfunction) mají nízký dopad na procesor z důvodu jejich logiky, jiné jsou složitější a zahrnují heuristické a odhady, které umožňují jejich efektivní spouštění. Například [DCount ()](/azure/kusto/query/dcount-aggfunction) používá HyperLogLog algoritmus k poskytnutí přibližného odhadu pro jedinečný počet velkých sad dat, aniž by bylo nutné skutečně počítat každou hodnotu; funkce percentilu jsou podobné aproximace pomocí nejbližšího algoritmu percentilu. Několik příkazů zahrnuje volitelné parametry, které snižují jejich dopad. Například funkce [makeset ()](/azure/kusto/query/makeset-aggfunction) má volitelný parametr pro definování maximální velikosti sady, která VÝZNAMNĚ ovlivňuje procesor a paměť.

Příkazy [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) a [sumarizace](/azure/kusto/query/summarizeoperator) můžou způsobit vysoké využití procesoru při zpracování velké sady dat. Jejich složitost přímo souvisí s počtem možných hodnot, které se označují jako *mohutnost*sloupců, které se používají jako `by` v souhrnu nebo jako atributy spojení. Vysvětlení a optimalizace spojení a shrnutí najdete v článcích dokumentace a tipy k optimalizaci.

Například následující dotazy vytváří přesně stejný výsledek, protože **CounterPath** je vždy mapováno na **CounterName** a **ObjectName**. Druhá je efektivnější, protože dimenze agregace je menší:

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

Využití CPU může být ovlivněno i v případě, že podmínky nebo rozšířené sloupce vyžadující náročné výpočty. Veškerá porovnávání triviálních řetězců, jako [je EQUAL = =](/azure/kusto/query/datatypes-string-operators) a [StartsWith](/azure/kusto/query/datatypes-string-operators) , mají zhruba stejný dopad na procesor, zatímco rozšířené shody textu mají větší vliv. Konkrétně operátor is je efektivnější pro operátor Contains. Z důvodu technik zpracování řetězců je efektivnější Hledat řetězce, které jsou delší než čtyři znaky než krátké řetězce.

Například následující dotazy poskytují podobné výsledky v závislosti na zásadách pojmenovávání počítačů, ale druhá z nich je efektivnější:

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
> Tento ukazatel prezentuje jenom procesor z bezprostředního clusteru. Dotaz ve více oblastech by představoval jenom jednu z oblastí. V dotazu s více pracovními prostory nemusí obsahovat všechny pracovní prostory.


## <a name="data-used-for-query-processing"></a>Data používaná pro zpracování dotazů

Důležitým faktorem při zpracování dotazu je objem dat, který je prohledáván a používán pro zpracování dotazu. Azure Průzkumník dat používá agresivní optimalizace, které výrazně snižují objem dat v porovnání s jinými datovými platformami. Stále existují v dotazu kritické faktory, které mohou ovlivnit objem dat, který se používá.
V protokolu Azure Monitor se sloupec **TimeGenerated** používá jako způsob, jak data indexovat. Omezení **TimeGeneratedch** hodnot na co nejblíže větší rozsah zajistí výrazné zlepšení výkonu dotazů tím, že významně omezí množství dat, která se mají zpracovat.

Dalším faktorem, který zvyšuje počet zpracovaných dat, je použití velkého počtu tabulek. K tomu obvykle dochází, když se používají příkazy `search *` a `union *`. Tyto příkazy vynutí, aby systém vyhodnotil a kontroloval data ze všech tabulek v pracovním prostoru. V některých případech může být v pracovním prostoru stovky tabulek. Snažte se vyhnout co nejvíc pomocí "vyhledávání *" nebo hledání bez jeho oboru na konkrétní tabulku.

Například následující dotazy vytváří přesně stejný výsledek, ale poslední má nejvyšší účinnost:

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

Další metodou, jak snížit objem dat, [je, aby](/azure/kusto/query/whereoperator) byly v počátečním stavu dotazu. Platforma Azure Průzkumník dat zahrnuje mezipaměť, která umožňuje zjistit, které oddíly obsahují data relevantní pro konkrétní podmínku WHERE. Například pokud dotaz obsahuje `where EventID == 4624` pak by měl distribuovat dotaz pouze uzlům, které zpracovávají oddíly s odpovídajícími událostmi.

Následující příklady dotazů vytváří přesně stejný výsledek, ale druhý z nich je efektivnější:

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

Vzhledem k tomu, že Azure Průzkumník dat je sloupcové úložiště dat, načítání každého sloupce je nezávisle na ostatních. Počet sloupců, které jsou načteny přímo, ovlivňuje celkový objem dat. Měli byste zahrnout pouze sloupce ve výstupu, které jsou potřeba při [sumarizaci](/azure/kusto/query/summarizeoperator) výsledků nebo probíhajících [projektů](/azure/kusto/query/projectoperator) pro konkrétní sloupce. Azure Průzkumník dat má několik optimalizací pro snížení počtu načtených sloupců. Pokud zjistí, že sloupec není potřeba, například pokud není odkazován v příkazu [Shrnutí](/azure/kusto/query/summarizeoperator) , nebude ho načíst.

Druhý dotaz může například zpracovat třikrát více dat, protože potřebuje načíst ne jeden sloupec, ale tři:

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

## <a name="time-range-of-the-data-processed"></a>Časový rozsah zpracovaných dat

Všechny protokoly v protokolu Azure Monitor jsou rozdělené podle sloupce **TimeGenerated** . Počet oddílů, které jsou k dispozici, přímo souvisí s časovým rozsahem. Snížení časového rozsahu představuje nejúčinnější způsob, jak zajistit, aby se provádění dotazů zobrazilo.

Časový rozsah lze nastavit pomocí voliče časového rozsahu na obrazovce Log Analytics, jak je popsáno v tématu [rozsah dotazu protokolu a časový rozsah v Azure Monitor Log Analytics](scope.md#time-range). Toto je doporučená metoda, protože vybraný časový rozsah je předán do back-endu pomocí metadat dotazu. 

Alternativním způsobem je explicitní zahrnutí podmínky [WHERE](/azure/kusto/query/whereoperator) na **TimeGenerated** v dotazu. Tuto metodu byste měli použít, protože zajišťuje, že časové rozpětí je vyřešeno, a to i v případě, že je dotaz použit z jiného rozhraní.
Měli byste se ujistit, že všechny části dotazu mají **TimeGenerated** filtry. Když dotaz obsahuje poddotazy načítající data z různých tabulek nebo stejné tabulky, každá z nich musí zahrnovat vlastní podmínku [WHERE](/azure/kusto/query/whereoperator) .

Například v následujícím dotazu se v případě, že je tabulka **výkonu** prohledávána pouze za poslední den, bude prohledána tabulka **prezenčního signálu** pro celou svou historii, což může trvat až dva roky:

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

Běžným případem, kdy dojde k takové chybě, je použití [arg_max ()](/azure/kusto/query/arg-max-aggfunction) k vyhledání nejaktuálnějšího výskytu. Příklad:

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

Tuto možnost lze snadno opravit přidáním časového filtru do vnitřního dotazu:

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

Měření je vždy větší než zadaný skutečný čas. Pokud je například filtr dotazu 7 dní, systém může kontrolovat 7,5 nebo 8,1 dnů. Je to proto, že systém rozdělí data do bloků dat v proměnné velikosti. Aby bylo zajištěno, že všechny relevantní záznamy budou prohledávány, prohledá celý oddíl, který může pokrývat několik hodin a dokonce i více než jeden den.

Existuje několik případů, kdy systém nemůže poskytnout přesné měření časového rozsahu. K tomu dochází ve většině případů, kdy je méně než jeden den nebo v dotazech na více pracovních prostorů.

> [!NOTE]
> Tento indikátor prezentuje pouze data zpracovaná v bezprostředním clusteru. Dotaz ve více oblastech by představoval jenom jednu z oblastí. V dotazu s více pracovními prostory nemusí obsahovat všechny pracovní prostory.

## <a name="age-of-the-oldest-data-used"></a>Stáří nejstarší použité dat
Azure Průzkumník dat využívá několik vrstev úložiště: místní disky SSD v paměti a mnohem pomalejší objekty blob Azure. V novějších datech je vyšší pravděpodobnost, že je uložená v výkonnější úrovni s menší latencí, což snižuje dobu trvání dotazů a CPU. Kromě samotných dat má systém také mezipaměť pro metadata. Starší data, menší pravděpodobnost, že metadata budou v mezipaměti.

I když některé dotazy vyžadují použití starých dat, existují případy, kdy omylem používá stará data. K tomu dochází, pokud jsou dotazy spouštěny bez časového rozsahu v jejich metadatech a ne všechny odkazy tabulky zahrnují filtr na sloupec **TimeGenerated** . V těchto případech bude systém kontrolovat všechna data, která jsou uložena v této tabulce. Pokud je uchovávání dat dlouhé, může se vztahovat na dlouhé časové rozsahy, tedy na data, která jsou stará jako doba uchovávání dat.

Takové případy můžou být například:

- Nenastavuje se časový rozsah v Log Analytics poddotazem, který není omezen. Viz výše uvedený příklad.
- Použití rozhraní API bez volitelných parametrů časového rozsahu.
- Pomocí klienta, který nevynucuje časový rozsah, jako je Power BI konektor.

Podívejte se na příklady a poznámky v části zkontrolují, protože jsou také relevantní v tomto případě.

## <a name="number-of-regions"></a>Počet oblastí
Existuje několik situací, kdy může být jeden dotaz spuštěn v různých oblastech:

- V případě, že je v seznamu výslovně uvedeno několik pracovních prostorů a jsou umístěny v různých oblastech.
- Když dotaz v oboru prostředků načítá data a data jsou uložena ve více pracovních prostorech nacházejících se v různých oblastech.

Provádění dotazů mezi oblastmi vyžaduje, aby systém serializován a přenesl do back-endu velkých bloků mezilehlých dat, která jsou obvykle mnohem větší než konečné výsledky dotazu. Také omezuje schopnost systému provádět optimalizace, heuristické a využité mezipaměti.
Pokud není k dispozici žádný reálný důvod pro kontrolu všech těchto oblastí, měli byste obor upravit tak, aby se kryl méně oblastí. Pokud je obor prostředku minimalizován, ale stále se používá mnoho oblastí, může k tomu dojít z důvodu chyby konfigurace. Například protokoly auditu a nastavení diagnostiky jsou odesílány do různých pracovních prostorů v různých oblastech nebo existují více konfigurací nastavení diagnostiky. 

> [!IMPORTANT]
> Když se dotaz spustí napříč několika oblastmi, měření procesoru a dat nebudou přesné a bude reprezentovat měření pouze v jedné z oblastí.

## <a name="number-of-workspaces"></a>Počet pracovních prostorů
Pracovní prostory jsou logické kontejnery, které slouží k oddělení a správě dat protokolů. Back-end optimalizuje umístění pracovních prostorů na fyzických clusterech v rámci vybrané oblasti.

Využití více pracovních prostorů může mít za následek: 

- Kde je výslovně uvedeno několik pracovních prostorů.
- Když dotaz v oboru prostředků načítá data a data jsou uložena ve více pracovních prostorech.
 
Provádění dotazů mezi oblastmi a mezi clustery vyžaduje, aby systém serializován a přenesl do back-endu velkých bloků mezilehlých dat, která jsou obvykle mnohem větší než konečné výsledky dotazu. Také omezuje schopnost systému provádět optimalizace, heuristické a využití mezipamětí.

> [!IMPORTANT]
> V některých scénářích s více pracovními prostory nebudou měření procesoru a dat přesné a bude reprezentovat měření jenom pro několik pracovních prostorů.

## <a name="parallelism"></a>Paralelismu
Protokoly Azure Monitor používají ke spouštění dotazů velké clustery Azure Průzkumník dat. Tyto clustery se liší ve škálování a můžou získat až 140 výpočetních uzlů. Systém automaticky škáluje clustery podle logiky umístění pracovního prostoru a kapacity.

Chcete-li efektivně spustit dotaz, je rozdělen a distribuován do výpočetních uzlů na základě dat, která jsou požadována pro jeho zpracování. Existují situace, kdy systém nemůže provádět to efektivně. To může mít za následek dlouhou dobu trvání dotazu. 

Chování dotazů, které může snížit paralelismus, zahrnuje:

- Použití funkcí serializace a okno, jako je například [operátor serializace](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [předchozí ()](/azure/kusto/query/prevfunction)a funkce [řádku](/azure/kusto/query/rowcumsumfunction) . V některých případech je možné použít časové řady a funkce uživatelsky Analytics. Neefektivní serializace se může vyskytnout také v případě, že na konci dotazu nejsou použity následující operátory: [Rozsah](/azure/kusto/query/rangeoperator), [řazení](/azure/kusto/query/sortoperator), [pořadí](/azure/kusto/query/orderoperator), [horní](/azure/kusto/query/topoperator), [horní – hitters](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-   Použití agregační funkce [DCount ()](/azure/kusto/query/dcount-aggfunction) vynutí, aby systém měl centrální kopii jedinečných hodnot. V případě vysoké škály dat zvažte použití volitelných parametrů funkce DCOUNT pro snížení přesnosti.
-   V mnoha případech operátor [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) snižuje celkový paralelismus. Pokud je výkon problematický, prověřte náhodné spojení jako alternativu.
-   V dotazech v oboru prostředků se kontroly RBAC před provedením můžou omezit v situacích, kdy existuje velký počet přiřazení RBAC. To může vést k delším kontrolám, které by způsobily nižší paralelismus. Například dotaz se spustí v předplatném, kde jsou tisíce prostředků a každý prostředek má mnoho přiřazení rolí na úrovni prostředků, nikoli na předplatném nebo skupině prostředků.
-   Pokud dotaz zpracovává malé bloky dat, jeho paralelismus bude nízký, protože systém nebude rozložen mezi mnoho výpočetních uzlů.



## <a name="next-steps"></a>Další kroky

- [Referenční dokumentace k dotazovacímu jazyku Kusto](/azure/kusto/query/)

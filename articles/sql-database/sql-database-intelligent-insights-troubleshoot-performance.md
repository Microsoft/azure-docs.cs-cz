---
title: Řešení potíží s výkonem databáze SQL Azure s inteligentního Insights | Microsoft Docs
description: Inteligentní přehledy vám pomůže vyřešit problémy s výkonem databáze SQL Azure.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: 7830a8a4bfc43e158069cc7cdc186e289e166751
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Řešení potíží s výkonem databáze SQL Azure s inteligentního statistiky

Tato stránka obsahuje informace o problémy s výkonem databáze SQL Azure, které jsou zjištěné [inteligentního Statistika](sql-database-intelligent-insights.md) protokolu diagnostiky výkonu databáze. Tento protokol diagnostics lze odeslat buď do [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), nebo řešení třetí strany pro vlastní DevOps výstrahy a vytváření sestav Možnosti.

> [!NOTE]
> Rychlé SQL Database výkonu řešení potíží informace prostřednictvím inteligentního statistiky, v tématu [doporučené řešení potíží s toku](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) vývojový diagram v tomto dokumentu.
>

## <a name="detectable-database-performance-patterns"></a>Vzory výkonu rozpoznat databáze

Problémy s výkonem inteligentního Insights automaticky rozpozná s databází SQL, na základě dobu čekání provádění dotazu, chyby nebo vypršení časových limitů. Výstupy pak vzory zjištěné výkonu protokolu diagnostiky. Vzory rozpoznat výkonu jsou shrnuty v následující tabulce:

| Vzory rozpoznat výkonu | Podrobnosti o výstupem |
| :------------------- | ------------------- |
| [Rozsáhlejší limitů prostředků](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Spotřeba dostupné prostředky (Dtu), databáze pracovních vláken nebo relace přihlášení databáze, které jsou k dispozici na monitorovaných předplatné bylo dosaženo omezení, což způsobí, že problémy s výkonem databáze SQL. |
| [Zvýšení zatížení](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Zvýšení zatížení nebo průběžné akumulace úlohy v databázi byla zjištěna, což způsobí, že problémy s výkonem databáze SQL. |
| [Přetížení paměti](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracovní procesy, které požadovaly uděluje paměti musí čekat přidělení paměti pro statisticky významné množství času. Nebo nahromadění vyšší pracovních procesů, který uděluje paměti požadovaná existuje, jenž ovlivňuje výkon databáze SQL. |
| [Zamykání](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Zamykání nadměrné databáze byla zjištěna, jenž ovlivňuje výkon databáze SQL. |
| [Zvýšená MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Došlo ke změně Maximální stupeň možnost paralelismus (MAXDOP), a ovlivňuje efektivitu provádění dotazu. |
| [Pagelatch kolizí](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Byla zjištěna Pagelatch kolizí, jenž ovlivňuje výkon databáze SQL. Více vláken současně pokusí o přístup stejné stránky vyrovnávací paměť dat v paměti. Výsledkem je zvýšená čekací dobu, která ovlivňuje výkon databáze SQL. |
| [Chybí indexu](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Byl zjištěn problém s chybějící index, jenž ovlivňuje výkon databáze SQL. |
| [Nový dotaz](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Byla zjištěna nový dotaz, který má vliv na celkový výkon databáze SQL. |
| [Statistiky neobvyklou čekání](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Byly zjištěny dobu čekání neobvyklou databáze, která ovlivňuje výkon databáze SQL. |
| [Databáze TempDB kolizí](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Více vláken došlo k pokusu o přístup k stejné databáze tempDB prostředky, které způsobuje kritický bod, který ovlivňuje výkon databáze SQL. |
| [Nedostatek DTU elastického fondu](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Nedostatek dostupné Edtu ve fondu elastické ovlivňuje výkon databáze SQL. |
| [Plánování regrese](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Nový plán nebo změny v zatížení existujícího plánu byla zjištěna, jenž ovlivňuje výkon databáze SQL. |
| [Změna hodnoty obor databáze konfigurace](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Změna konfigurace v databázi ovlivňuje výkon databáze SQL. |
| [Pomalé klienta](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Byla zjištěna pomalá aplikace klienta, který nemůže využívat výstup z databáze SQL dostatečně rychlé, jenž ovlivňuje výkon databáze SQL. |
| [Cenová úroveň přechod na starší verzi](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Přechod na starší verzi akci s cenovou úroveň zmenšit dostupné prostředky, které ovlivňuje výkon databáze SQL. |

> [!TIP]
> Optimalizace výkonu průběžné databáze SQL, povolit [Azure SQL Database automatické ladění](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). Tato jedinečná funkce vestavěné inteligentní SQL Database nepřetržitě monitoruje databázi SQL, automaticky vyladí indexy a platí opravy plán spuštění dotazu.
>

Následující část popisuje vzory výše uvedených rozpoznat výkonu podrobněji.

## <a name="reaching-resource-limits"></a>Rozsáhlejší limitů prostředků

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu kombinuje problémy s výkonem, které se vztahují k dosažení limitů dostupných prostředků, pracovní limity a omezení relací. Po zjištění tento problém s výkonem se pole Popis protokolu diagnostiky označuje, zda problémy s výkonem se prostředků, worker nebo omezení relací.

Prostředky v databázi SQL se obvykle označují jako [DTU prostředky](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu). Skládají se z kombinaci měření prostředků procesoru a vstupů/výstupů (dat a transakční protokol vstupů/výstupů). Vzor dosažení limitů prostředků se rozpozná, když zjistí snížení výkonu dotazu je způsobena dosažení žádné omezení měřená prostředků.

Prostředek omezení relace označuje počet dostupných souběžných přihlášení k databázi SQL. Tento vzor výkonu se rozpozná, když aplikace, které jsou připojené k databázím SQL bylo dosaženo počtu dostupných souběžných přihlášení k databázi. Pokud se aplikace pokusí použít více relací, než je k dispozici v databázi, je vliv na výkon dotazu.

Dosažení limity pracovního procesu je konkrétní případ dosažení omezení prostředků, protože nejsou k dispozici pracovníci počítá ve využití DTU. Dosažení limity pracovního procesu na databázi, může způsobit zvýšení doby čekání konkrétní prostředky, což vede k dotazu snížení výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy hodnoty hash dotazu dotazů, které vliv na výkon a procenta spotřeby prostředků. Tyto informace můžete použít jako východisko pro optimalizaci velikosti pracovní zátěže databáze. Konkrétně můžete optimalizovat dotazy, které ovlivňují snížení výkonu přidáním indexy. Nebo můžete optimalizovat aplikace s více i zatížení distribučním. Pokud chcete snížit zatížení nebo provést optimalizace nemůžete, zvažte zvýšení cenové úrovně se vaše předplatné databáze SQL se zvýšit množství dostupných zdrojů.

Pokud bylo dosaženo omezení relace k dispozici, můžete optimalizovat aplikací snížením počtu přihlášení, provedené v databázi. Pokud nemůžete snížit počet přihlášení z vaší aplikace do databáze, zvažte zvýšení cenová úroveň databáze. Nebo můžete rozdělit a přesuňte databázi do více databází pro více vyvážené distribuce zatížení.

Další návrhy na řešení omezení relací, najdete v části [řešení problémů s omezení přihlašovacích jmen k databázi SQL maximální](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). K dispozici prostředek limity pro vaše předplatné vrstvy naleznete v tématu [limitů prostředků databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Zvýšení zatížení

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu identifikuje problémy způsobené zvýšení zatížení, nebo v jeho závažnější formuláře pile-up zatížení.

Toto zjišťování se provádí pomocí kombinace několik metriky. Základní metrika měří je zjišťování o zvýšení v zatížení ve srovnání s posledních směrného plánu úlohy. Jinou formu zjišťování je založen na měření velký nárůst active pracovních vláken, který dostatečně velký pro vliv na výkon dotazu.

V jeho závažnější formuláře může zatížení nepřetržitě hromadí z důvodu nemohou databáze SQL pro zpracování úlohy. Výsledkem je neustále rostoucí velikost zatížení, která je podmínka pile-up zatížení. Z důvodu tuto podmínku zvětšování čas, který čeká na provedení úlohy. Tato podmínka představuje jeden z nejzávažnějšího problémů s výkonem databáze. Tento problém je zjištěna prostřednictvím monitorování nárůst Počet přerušených pracovních vláken. 

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy počet dotazů, jejichž spuštění zvýšilo a hodnota hash dotazu dotazu s největší příspěvku zvýšení zatížení. Tyto informace můžete použít jako výchozí bod pro optimalizaci zatížení. Dotaz identifikovat jako největší Přispěvatel se zvýšení zatížení je obzvláště užitečná jako výchozí bod.

Můžete uvažovat o distribuci zatížení více rovnoměrně do databáze. Zvažte optimalizaci dotazu, který ovlivňuje výkon přidáním indexy. Je také může distribuovat vaše zatížení mezi více databází. Pokud tato řešení nejsou možné, zvažte zvýšení cenové úrovně se vaše předplatné databáze SQL se zvýšit množství dostupných zdrojů.

## <a name="memory-pressure"></a>Přetížení paměti

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení aktuální databáze výkonu způsobené přetížení paměti, nebo v jeho závažnější formuláře podmínku pile-up paměti, porovnání se směrným plánem posledních sedmi dnů výkonu.

Přetížení paměti označuje výkonovou podmínku, ve kterém je velký počet pracovních vláken uděluje žádajícího paměti v databázi SQL. Vyšší objemy způsobí, že podmínce využití velkého množství paměti, ve kterém je databáze SQL nelze efektivně přidělit paměť pro všechny pracovní procesy, které ji. Jedním z nejčastějších důvodů pro tento problém se týká množství paměti k dispozici k databázi SQL na jedné straně. Na druhé straně zvýšení zatížení způsobí zvýšení pracovních vláken a přetížení paměti.

Závažnější formu přetížení paměti je podmínka pile-up paměti. Tento stav označuje vyšší počet pracovních vláken žádají uděluje paměti než nejsou k dispozici dotazy uvolňování paměti. Tento počet pracovních vláken, která uděluje žádajícího paměti také může být neustále zvýšení (kumulování) vzhledem k tomu, že databázový stroj SQL se nepodařilo přidělit paměť dostatečně efektivně pro splnění těchto požadavků. Podmínka pile-up paměti představuje jeden z nejzávažnějšího problémů s výkonem databáze.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy podrobnosti paměti objektu úložiště s fulltextu (to znamená, pracovní vlákno) označen jako nejvyšší důvod pro velké množství paměti a relevantní časová razítka. Tyto informace můžete použít jako základ pro řešení potíží. 

Můžete optimalizovat nebo odebrat dotazů souvisejících s úředníci s nejvyšší využití paměti. Můžete také zajistit, že nejsou dotazování na data, která nemáte v úmyslu použít. Doporučeným postupem je vždy nutné použít klauzuli WHERE v dotazech. Kromě toho doporučujeme, abyste vytvořili neclusterované indexy hledat data, nikoli jeho kontrolu.

Také můžete snížit zatížení optimalizace nebo distribuci přes více databází. Nebo můžete distribuovat vaše zatížení mezi více databází. Pokud tato řešení nejsou možné, zvažte zvýšení cenové úrovně se vaše předplatné databáze SQL se zvýšit množství paměti k dispozici prostředky pro databázi.

Další tipy pro řešení potíží, najdete v části [paměti uděluje meditačních: Záhadné příjemce paměti systému SQL Server s mnoha názvy](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Zamykání

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení aktuální výkon databáze, ve kterém zamykání nadměrné databáze je zjištěn porovnání se směrným plánem posledních sedmi dnů výkonu. 

V moderní RDBMS zamykání je nezbytné pro implementaci vícevláknové systémy, ve kterých je maximalizovat výkon spuštěním více souběžných pracovních procesů a paralelní databázové transakce, kde je to možné. Zamykání v tomto kontextu odkazuje na předdefinované přístup mechanismus, ve kterém můžete pouze jediné transakce výhradně přístup řádků, stránky, tabulky a soubory, které jsou vyžadované a není pokouší s jinou transakcí pro prostředky. Pokud transakce, která uzamčena prostředky pro použití se provádí s nimi, je vydání zámku na tyto prostředky, což umožňuje dalších transakcí pro přístup k požadované prostředky. Další informace o uzamčení najdete v tématu [Zamknout v databázovém stroji](https://msdn.microsoft.com/library/ms190615.aspx).

Pokud transakce provedený stroj SQL čekají na delší dobu potřebnou pro přístup k prostředkům uzamčen pro použití, tato doba čekání způsobí, že zpomalení výkonu provádění úlohy. 

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy uzamčení podrobnosti, které můžete použít jako základ pro řešení potíží. Můžete analyzovat hlášené blokování dotazů, to znamená, dotazy, které zavést uzamčení snížení výkonu, a je odebrat. V některých případech může být úspěšné při optimalizaci blokování dotazů.

A co možná nejbezpečnějším způsobem zmírnit problém je potřeba uchovat transakce krátký a redukuje zámku nejnákladnější dotazů. Můžete rozdělit dávce velkých operací do menší operací. Dobrým postupem je redukuje zámku dotaz tak, že dotaz co nejúčinnější. Velké kontroly snížit, protože zvýšit pravděpodobnost, blokování a nepříznivě ovlivnit celkový výkon databáze. Pro zjištěné dotazy, které způsobí zamykání můžete vytvořit nové indexy nebo přidat sloupce do stávajícího indexu, aby se zabránilo prohledávání tabulky. 

Další návrhy, najdete v části [řešení blokující problémy, které jsou způsobeny eskalace zámku v systému SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zvýšená MAXDOP

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje, že podmínka, ve kterém byl plán spuštění zvolené dotazu paralelizovaná málo větší, než by musela být. Optimalizátor dotazů SQL Database může zlepšit výkon úloh spuštěním dotazy paralelně pro urychlení věcí, kde je to možné. V některých případech tráví paralelní pracovníci zpracování dotazu delší dobu čekání na další k synchronizaci a sloučení výsledky ve srovnání s provedením stejný dotaz s méně paralelní pracovní procesy, nebo i v některých případech ve srovnání s jeden pracovní vlákno.

Systém expert analyzuje aktuální databáze výkon ve srovnání s obdobím směrného plánu. Určuje, zda dříve spuštěné dotazu běží něco pomalejší, než před protože plán dotazu provádění více paralelizovaná málo než by mělo být.

Možnosti konfigurace serveru MAXDOP pro službu SQL Database je použít k řízení, kolik jader procesoru můžete použít ke spuštění stejný dotaz paralelně. 

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy hodnoty hash dotazu související s dotazy, pro které trvání provádění zvýšit, protože jejich byly paralelizovaná více než by měl mít byla málo. Protokol také výstupy CXP dobu čekání. Tentokrát představuje dobu, kterou jeden organizátora/coordinator vlákno (vlákna 0) čeká jiná vlákna ukončíte před slučování výsledky a průběh. Kromě toho protokol diagnostiky výstupy nízká provádění dotazů čekaly při provádění celkové doby čekání. Tyto informace můžete použít jako základ pro řešení potíží.

Nejprve optimalizovat nebo zjednodušte složitých dotazů. Dobrým postupem je rozdělit dlouho dávkových úloh na menší části. Kromě toho ověřte, že jste vytvořili indexy, aby se vaše dotazy. Maximální stupně paralelního zpracování (MAXDOP) můžete také ručně vynutit pro dotaz, který byl označení nízký provádění. Tato operace konfigurace pomocí T-SQL najdete v tématu [konfigurovat možnosti konfigurace serveru MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Nastavení serveru MAXDOP možnost konfigurace na nulu (0) jako výchozí hodnota označuje, že databáze SQL můžete použít všechny dostupné logické jader procesoru učinit paralelní vlákna pro provádění jeden dotaz. Nastavení MAXDOP na jedna (1) označuje, že pouze jeden základní lze použít pro spuštění jeden dotaz. To znamená, že paralelismus vypnutý. V závislosti na základ za případů k dispozici jader s databází a diagnostiku protokolování informací, můžete vyladit MAXDOP možnost počet jader používá pro provádění paralelního dotazu, který může být problém ve vašem případě vyřešit.

## <a name="pagelatch-contention"></a>Pagelatch kolizí

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje aktuální snížení výkonu zatížení databáze z důvodu pagelatch kolizí porovnání se směrným plánem posledních sedmi dnů zatížení.

Zámky jsou mechanismy zjednodušené synchronizace použít SQL Database k povolení více vláken. Jejich zaručit konzistenci struktury v paměti, které zahrnují indexy, datových stránek a ostatní interní struktury.

V databázi SQL nejsou k dispozici mnoho typů zámků. Pro účely jednoduchost vyrovnávací paměti západek slouží k ochraně stránky v paměti ve fondu vyrovnávací paměti. Zámky vstupně-výstupní operace se používají k ochraně stránky ještě nebyla načtena do fondu vyrovnávací paměti. Vždy, když data jsou zapsána do nebo ze stránky ve fondu vyrovnávací paměti pro čtení, je potřeba nejdřív získat západku vyrovnávací paměti pro stránku pracovní vlákno. Vždy, když pracovní vlákno se pokusí přistoupit ke stránce, která už není k dispozici ve fondu vyrovnávací paměti v paměti, Přišla žádost o vstupně-výstupní operace načíst požadované informace z úložiště. Tato posloupnost událostí, které označuje závažnější formu snížení výkonu.

Kolize na stránce západek nastane, když více vláken současně pokusí získat zámky na stejnou strukturu v paměti, které představuje vyšší čekací dobu pro spuštění dotazu. V případě sporu pagelatch vstupně-výstupní operace, když je přístupná z úložiště, data tato čekací doba je i větší. Výrazně se může ovlivnit výkon pracovního vytížení. Pagelatch kolizí je nejběžnější scénář vláken čekajících na sebe navzájem a neslučitelných pro prostředky na více systémů procesoru.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy pagelatch kolizí podrobnosti. Tyto informace můžete použít jako základ pro řešení potíží.

Protože pagelatch je mechanismus řízení interní databáze SQL, automaticky určují, kdy je použít. Rozhodnutí o aplikaci, včetně návrhu schématu může ovlivnit chování pagelatch z důvodu deterministické chování zámků.

Jednu metodu pro zpracování západky kolizí je nahradit počátečních klíč rovnoměrně distribuuje vložení přes rozsah index klíče sekvenční indexu. Obvykle počáteční sloupec v indexu distribuuje úměrně zatížení. Další metodou pro vezměte v úvahu je vytváření oddílů tabulky. Vytvoření hodnotu hash schéma s počítaný sloupec v tabulce rozdělené na oddíly je běžný postup pro minimalizaci nadměrné západky kolizí. V případě sporu pagelatch vstupně-výstupní operace představení indexy pomáhá zmírnit tento problém s výkonem. 

Další informace najdete v tématu [Diagnostikujte a vyřešte opatřit kolizí v systému SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (ke stažení PDF).

## <a name="missing-index"></a>Chybí indexu

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje aktuální snížení zatížení výkonu databáze, kde je porovnání se směrným plánem posledních sedmi dnů z důvodu chybějící index.

Index se používá k urychlení výkonu dotazů. Nabízí rychlý přístup k datům tabulky snížením počtu stránek datové sady, které je třeba navštívené nebo zkontrolovat.

Prostřednictvím této detekce, pro které by bylo vytváření indexů výhodné výkonu jsou identifikovány konkrétní dotazy, které způsobit snížení výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy hodnoty hash dotazu pro dotazy, které byly identifikovány do mají vliv na výkon úloh. Můžete vytvořit indexů pro tyto dotazy. Můžete také optimalizovat nebo odebrat tyto dotazy, pokud nejsou požadované. Dobrý výkon postupem se vyhnete dotazování na data, která nepoužíváte.

> [!TIP]
> Věděli jste, že vestavěné inteligentní SQL Database může automaticky spravovat přizpůsobené provádění indexů pro vaše databáze?
>
> Pro optimalizaci průběžné výkonu SQL databáze, doporučujeme, abyste povolili [SQL Database automatické ladění](sql-database-automatic-tuning.md). Tato jedinečná funkce vestavěné inteligentní SQL Database nepřetržitě monitoruje databázi SQL a automaticky vyladí a vytváří indexy pro své databáze.
>

## <a name="new-query"></a>Nový dotaz

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje rozpoznání nový dotaz, je špatně provádění a které mají vliv na výkon zatížení porovnání se směrným plánem výkonu 7 dnů.

Zápis dobré provádění dotazu v některých případech může být náročné úlohy. Další informace o programování dotazů najdete v tématu [dotazy SQL zápis](https://msdn.microsoft.com/library/bb264565.aspx). Za účelem optimalizace výkonu existující dotaz, najdete v části [ladění dotazu](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolování informací výstupy až dvě nové většinu využívání procesoru dotazů, včetně jejich hodnoty hash dotazu. Protože zjištěné dotaz ovlivňuje výkon úloh, můžete optimalizovat svůj dotaz. Doporučeným postupem je načíst pouze data, která budete muset použít. Doporučujeme také pomocí klauzule WHERE dotazů. Doporučujeme také zjednodušit složitých dotazů a je rozdělit na menší dotazy. Jiné dobrým postupem je analyzují velké batch dotazy na menší dotazy batch. Představení indexů pro nové dotazy je většinou vhodné zmírnit tento problém s výkonem.

Zvažte použití [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Statistiky neobvyklou čekání

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje snížení výkonu úlohy, ve kterém jsou identifikovány nízká provádění dotazů ve srovnání s posledních sedmi dnů zatížení směrného plánu.

V takovém případě systému nelze klasifikovat nízká provádění dotazů v ostatních kategorií standardní rozpoznat výkon, ale zjistil, že zodpovědná za regresi čekání statistiky. Proto je považuje za jako dotazy s *neobvyklou čekání statistiky*, kde je zodpovědná za regresi neobvyklou čekání statistiky také vystaven. 

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy informace o neobvyklou čekání podrobnosti času, hodnoty hash dotazu ovlivněných dotazy a dobu čekání.

Protože systému nelze úspěšně zjistit příčinu nízká provádění dotazů, diagnostické informace je to dobrý výchozí bod pro ruční řešení potíží. Můžete optimalizovat výkon těchto dotazů. Doporučeným postupem je načíst pouze data, která je nutné použít a zjednodušit a rozdělení složitých dotazů na menší části. 

Další informace o optimalizaci výkonu dotazů najdete v tématu [ladění dotazu](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Databáze TempDB kolizí

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje podmínku výkonu databáze, ve kterém existuje úzkým místem vláken pokusu o přístup k prostředkům databáze tempDB. (Tato podmínka není vstupně-výstupní operace související s). Typický scénář pro tento problém s výkonem je stovky souběžných dotazů, všechny vytvářet, používat a potom zrušit malé databáze tempDB tabulky. Systém zjistil, že počet souběžných dotazů pomocí stejné tabulky databáze tempDB vyšší s dostatečnou statistické násobek ovlivnit výkon databáze ve srovnání s posledních sedmi dnů výkonu směrného plánu.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy databáze tempDB kolizí podrobnosti. Informace můžete použít jako výchozí bod pro řešení potíží. Můžete pokračovat a zmírnit tento druh kolizí zvýšit propustnost celkové zatížení dvě věci: můžete zastavit pomocí dočasných tabulek. Můžete taky použít paměťově optimalizované tabulky. 

Další informace najdete v tématu [Úvod k paměťově optimalizované tabulky](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Nedostatek DTU elastického fondu

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje snížení v aktuální zatížení výkonu databáze porovnání se směrným plánem posledních sedmi dnů. Je z důvodu nedostatku dostupné Dtu ve fondu elastické vašeho předplatného. 

Prostředky v databázi SQL se obvykle označují jako [DTU prostředky](sql-database-what-is-a-dtu.md), které zahrnují kombinaci měření prostředků procesoru a vstupů/výstupů (dat a transakční protokol vstupů/výstupů). [Prostředky Azure elastický fond](sql-database-elastic-pool.md) slouží jako fond prostředky k dispozici eDTU, které jsou sdílené mezi více databází pro účely škálování. Pokud nejsou k dispozici eDTU prostředky ve vašem fondu elastické dostatečně velký pro podporu všechny databáze ve fondu, je zjištěna problém výkonu nedostatku DTU elastického fondu v systému.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy informace o elastického fondu, uvádí nejvyšší spotřeba DTU databáze a nabízí procento DTU fondu používá databázi horní využívání.

Protože je tento stav výkonu související s více databází v elastickém fondu pomocí stejného fondu Edtu, postup řešení potíží se zaměřit na nejvyšší spotřeba DTU databáze. Můžete snížit zatížení databáze využívající horní zahrnující optimalizace využívání horní dotazů na těchto databází. Také můžete zajistit, že nejsou dotazování na data, která nepoužíváte. Další možností je optimalizovat aplikací s použitím nejvyšší spotřeba DTU databáze a rozložit zatížení mezi více databází.

Pokud snížení a optimalizace aktuální zatížení v hlavní databáze spotřeba DTU nejsou možné, zvažte zvýšení elastického fondu cenová úroveň. Například zvýšit má za následek zvýšení dostupný počet jednotek Dtu elastického fondu.

## <a name="plan-regression"></a>Plánování regrese

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje podmínku, ve kterém SQL Database využívá plán spuštění zhoršené dotazu. Neoptimální plán obvykle způsobí, že provádění vyšší dotazů, což vede k už čekat časy pro aktuální a další dotazy.

Databáze SQL určuje plán provádění dotazu s nejmenším náklady na spuštění dotazu. Jako typ změna dotazy a úlohy existující plány jsou někdy už efektivní, nebo možná SQL Database nepodali dobrý hodnocení. Jako řádu oprava plány provádění dotazů se dá ručně vynutit.

Tento vzor rozpoznat výkonu kombinuje tři různé případy regrese plán: nový plán regrese, staré regrese plán a existující změnit plány úloh. Je součástí konkrétní typ regrese plán, který došlo k chybě *podrobnosti* vlastnost v protokolu diagnostiky.

Novou podmínku regrese plán odkazuje do stavu, ve kterém začíná SQL Database, provádění nový plán spuštění dotazu, který není co nejúčinnější původní plán. Starý stav regrese plán týká stavu při SQL Database přepíná z použití nové, efektivnější plánu k původní plánu, který není co nejúčinnější nový plán. Existující úlohy regrese změnit plány odkazuje do stavu, ve kterém starý a nový plány nepřetržitě alternativní, s rovnováhu mezi více přejdete k provádění nízká plánu.

Další informace o plánu regresí najdete v tématu [co je plán Regrese v systému SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy hodnoty hash dotazu, ID kvalitní plán, ID chybný plánu a ID dotazu. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete analyzovat, které plán je lepší, provádí se pro vaše konkrétní dotazy, které můžete identifikovat s hash dotazu zadat. Po určení, které plán funguje lépe pro své dotazy, můžete je ručně vynutit. 

Další informace najdete v tématu [zjistěte, jak brání systému SQL Server plán regresí](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Věděli jste, že vestavěné inteligentní SQL Database může automaticky spravovat přizpůsobené provádění provádění plány dotazů pro vaše databáze?
>
> Pro optimalizaci průběžné výkonu SQL databáze, doporučujeme, abyste povolili [SQL Database automatické ladění](sql-database-automatic-tuning.md). Tato jedinečná funkce vestavěné inteligentní databáze SQL nepřetržitě monitoruje databázi SQL a automaticky vyladí a vytvoří přizpůsobené provádění dotazu provádění plány pro své databáze.
>

## <a name="database-scoped-configuration-value-change"></a>Změna hodnoty obor databáze konfigurace

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje podmínku, ve kterém změny v konfiguraci databáze obor způsobí snížení výkonu, který je zjišťován ve srovnání s posledních sedmi dnů databáze chování pracovního vytížení. Tento vzor označuje, že poslední změny provedené v konfiguraci s rozsahem databáze ještě nezačala výhodné výkon databáze.

Změny konfigurace databáze obor lze nastavit pro každé jednotlivé databáze. Tato konfigurace je případ od případu použít k optimalizaci jednotlivých výkon vaší databáze. Pro každé jednotlivé databáze můžete konfigurovat následující možnosti: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES a ZRUŠTE PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolu výstupy obor databáze konfigurace provedené změny nedávno které způsobit snížení výkonu ve srovnání s předchozí chování zatížení 7 dnů. Můžete se vrátit na předchozí hodnoty změny konfigurace. Také můžete vyladit hodnoty hodnotou dokud nebude dosaženo úroveň požadovaný výkon. Hodnoty konfigurace oboru databáze můžete zkopírovat z podobně jako databáze s dostatečný výkon. Pokud nemůžete řešení výkon, vrátit zpět na výchozí databázi SQL výchozí hodnoty a pokus a systém doladit od tohoto směrného plánu.

Další informace o optimalizaci obor databáze konfigurace a syntaxe T-SQL na změnu konfigurace najdete v tématu [Alter database obor konfigurace (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Pomalé klienta

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu rychlého databáze odesílá výsledky označuje podmínku, ve kterém klienta pomocí databáze SQL nemůže využívat výstup z databáze. Vzhledem k tomu, že databáze SQL není výsledky spuštění dotazů ukládání do vyrovnávací paměti, zpomaluje a čeká, klient využívat výstupy přenášená dotazu než budete pokračovat. Tento stav může také související k síti, která není dostatečně dostatečně rychle přenést výstupy z databáze SQL pro využívání klienta.

Tento stav se vygeneruje pouze v případě, že snížení výkonu se zjistí ve srovnání s posledních sedmi dnů databáze chování pracovního vytížení. Tento problém s výkonem se zjistí, že pouze v případě, že statisticky k výraznému snížení výkonu ve srovnání s předchozí chování výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Tento vzor rozpoznat výkonu označuje podmínku, která na straně klienta. Řešení potíží se vyžaduje aplikace na straně klienta nebo klientské sítě. Protokol diagnostiky výstupy dotazu hodnoty hash a dobu čekání, které se zdá, že čekat nejvíc klient využívat během posledních dvou hodin. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat výkon aplikace pro používání těchto dotazů. Můžete také zvážit problémy s latencí sítě možná. Protože problémy s výkonem snížení bylo založeno na změny v posledních sedmi dnů výkonu směrného plánu, můžete prozkoumat, jestli nedávné změny stavu aplikace nebo sítě způsobila tato událost regrese výkonu. 

## <a name="pricing-tier-downgrade"></a>Cenová úroveň přechod na starší verzi

### <a name="what-is-happening"></a>Co se děje

Tento vzor rozpoznat výkonu označuje podmínku, ve kterém byl snížit cenovou úroveň předplatného databáze SQL. Kvůli nedostatku prostředků pro databáze (Dtu) systém zjistil pokles v aktuální databázi výkon ve srovnání s posledních sedmi dnů směrného plánu.

Kromě toho může být stavu, ve kterém byla cenová úroveň databáze SQL předplatného snížit a poté provedli upgrade na vyšší úroveň během krátké doby. V sekci podrobností protokolu diagnostiky jako cenová úroveň přechod na starší verzi a upgradu je výstupem zjišťování snížení toto dočasný výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Pokud snížit cenovou úroveň a proto Dtu, které jsou k dispozici pro databáze SQL a budete spokojeni s výkon, není nic, které musíte udělat. Pokud snížit cenovou úroveň a budete spokojeni s výkon databáze SQL, snižte zatížení vaší databáze nebo zvažte zvýšení cenovou úroveň na vyšší úrovni.

## <a name="recommended-troubleshooting-flow"></a>Doporučené řešení potíží s toku

 Postupujte podle vývojový diagram pro doporučený postup pro řešení potíží s výkonem pomocí inteligentního statistiky.

Statistika inteligentního přístup prostřednictvím portálu Azure tak, že přejdete k analýze SQL Azure. Pokus o příchozí výstrah výkonu a vyberte ji. Zjistit, co se děje na stránce detekce. Sledujte analýza zadaný hlavní příčiny problému, text dotazu, trendy čas dotazu a incidentu vývoj. Pokus o vyřešení problému pomocí inteligentního Statistika doporučení pro zmírnění problémy s výkonem. 

[![Vývojový diagram odstraňování potíží](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Vyberte vývojový diagram ke stažení PDF verze.

Inteligentní Insights je obvykle nutné jednu hodinu čas k provedení analýza hlavní příčiny problémy s výkonem. Pokud problém nelze najít v inteligentního přehledy a je pro vás velmi důležité, použijte k identifikaci ručně hlavní příčinu problémy s výkonem úložiště dotazů. (Tyto problémy jsou obvykle méně než hodinu stará.) Další informace najdete v tématu [monitorování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Další postup
- Další informace [inteligentního Statistika](sql-database-intelligent-insights.md) koncepty.
- Použití [protokolu diagnostiky výkonu inteligentního Statistika Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitorování [Azure SQL Database pomocí Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Naučte se [shromažďovat a využívat data protokolu z vašich prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

---
title: Řešení problémů s výkonem Azure SQL Database s Intelligent Insights | Dokumentace Microsoftu
description: Intelligent Insights pomáhá řešit problémy s výkonem Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 60d19f6d598c6090e0dc00c61817a0f8aed35003
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889493"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Řešení problémů s výkonem Azure SQL Database s Intelligent Insights

Tato stránka poskytuje informace o Azure SQL Database a zjištěné problémy s výkonem Managed Instance [Intelligent Insights](sql-database-intelligent-insights.md) protokolu diagnostiky výkonu databáze. Telemetrická data diagnostiky protokolu můžete Streamovat do [Azure Log Analytics](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [služby Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), nebo řešení třetí strany pro vlastní výstrahy DevOps a Možnosti vytváření sestav.

> [!NOTE]
> Rychlý výkon databáze SQL pomocí Intelligent Insights Průvodce odstraňováním potíží, najdete v článku [doporučené řešení potíží s tok](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) vývojový diagram v tomto dokumentu.
>

## <a name="detectable-database-performance-patterns"></a>Vzory výkonu zjistitelná databáze

Problémy s výkonem Intelligent Insights automaticky rozpozná s databází SQL Database a Managed Instance na základě čekací dobu provádění dotazů, chyb nebo vypršení časových limitů. Výstupu zjištěné výkonu vzory v protokolu diagnostiky. Vzory zjistitelná výkonu jsou shrnuty v následující tabulce.

| Vzory zjistitelná výkonu | Popis pro Azure SQL Database a elastické fondy | Popis pro databáze ve spravované instanci |
| :------------------- | ------------------- | ------------------- |
| [Dosáhnout omezení prostředků](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Spotřeby dostupných prostředků (Dtu), database pracovních vláken nebo relace přihlášení databáze k dispozici na monitorovaných předplatné bylo dosaženo omezení. To ovlivňuje výkon databáze SQL. | Spotřeba prostředků procesoru dosahuje limity Managed Instance. To ovlivňuje výkon databáze. |
| [Zvýšení zatížení](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Bylo zjištěno zvýšení zatížení nebo průběžné akumulací zatížení na databázi. To ovlivňuje výkon databáze SQL. | Byl zjištěn zvýšení zatížení. To ovlivňuje výkon databáze. |
| [Přetížení paměti](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracovní procesy, které požadované paměti uděluje se muset počkat, než pro přidělení paměti pro statisticky značné množství času. Nebo vyšší nahromadění dělníky, kteří požadované paměti uděluje existuje. To ovlivňuje výkon databáze SQL. | Pracovní procesy, které si vyžádali paměti uděluje čekají na přidělení paměti pro statisticky významná množství času. To ovlivňuje výkon databáze. |
| [Uzamčení](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Uzamčení nadměrné databáze byla zjištěna by to mělo dopad výkon SQL Database. | Uzamčení nadměrné databáze byla zjištěna by to mělo dopad výkon databáze. |
| [Zvýšená MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maximální volnost paralelismu možnost (MAXDOP) se změnila by to ovlivnilo efektivity provádění dotazu. To ovlivňuje výkon databáze SQL. | Maximální volnost paralelismu možnost (MAXDOP) se změnila by to ovlivnilo efektivity provádění dotazu. To ovlivňuje výkon databáze. |
| [Pagelatch kolizí](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Více vláken jsou současně pokouší o přístup stejné vyrovnávací paměti stránky dat v paměti výsledkem je zvýšená čekací dobu a způsobí pagelatch kolize. To ovlivňuje výkon databáze SQL. | Více vláken jsou současně pokouší o přístup stejné vyrovnávací paměti stránky dat v paměti výsledkem je zvýšená čekací dobu a způsobí pagelatch kolize. To ovlivňuje výkon databáze. |
| [Chybějící Index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Chybějící index byl zjištěn by to mělo dopad výkon SQL database. | Chybějící index byl zjištěn by to mělo dopad výkon databáze. |
| [Nový dotaz](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Nový dotaz byl zjištěn by to mělo dopad na celkový výkon SQL Database. | Nový dotaz byl zjištěn by to mělo dopad na celkový výkon databáze. |
| [Statistiky zvýšení čekání](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Zvýšenou databázovou čekací dobu byly zjištěny by to mělo dopad výkon SQL database. | Zvýšenou databázovou čekací dobu byly zjištěny by to mělo dopad výkon databáze. |
| [Databáze TempDB kolizí](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Více vláken se pokoušíte získat přístup stejné databáze TempDB prostředek, který způsobuje kritický bod. To ovlivňuje výkon databáze SQL. | Více vláken se pokoušíte získat přístup stejné databáze TempDB prostředek, který způsobuje kritický bod. To ovlivňuje výkon databáze. |
| [Nedostatek DTU elastického fondu](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Nedostatek dostupné Edtu v elastickém fondu ovlivňuje výkon databáze SQL. | Není k dispozici pro Managed Instance používá modelu virt. jader. |
| [Regrese plán](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Byl zjištěn nový plán nebo ke změně v úloze existující plán. To ovlivňuje výkon databáze SQL. | Byl zjištěn nový plán nebo ke změně v úloze existující plán. To ovlivňuje výkon databáze. |
| [Změna hodnoty konfigurace s rozsahem databáze](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Byla zjištěna změna konfigurace pro SQL Database, bychom ovlivnili výkon databáze. | Byla zjištěna změna konfigurace v databázi, ovlivňuje výkon databáze. |
| [Pomalé klienta](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Pomalé žádosti klienta není schopen dostatečně rychle využívat výstup z databáze. To ovlivňuje výkon databáze SQL. | Pomalé žádosti klienta není schopen dostatečně rychle využívat výstup z databáze. To ovlivňuje výkon databáze. |
| [Přechod na starší cenová úroveň](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Cenové úrovně downgrade akce snížení dostupné prostředky. To ovlivňuje výkon databáze SQL. | Cenové úrovně downgrade akce snížení dostupné prostředky. To ovlivňuje výkon databáze. |

> [!TIP]
> Optimalizace výkonu průběžné služby SQL Database, povolte [automatické ladění Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). Tato jedinečná funkce SQL Database integrované inteligentní funkce nepřetržitě monitoruje vaši databázi SQL, automaticky vyladí indexy a použije opravy plán provádění dotazu.
>

Následující část popisuje vzory zjistitelná výkonu podrobněji.

## <a name="reaching-resource-limits"></a>Dosáhnout omezení prostředků

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu kombinuje problémy s výkonem, které se vztahují k dosažení omezení dostupných prostředků, pracovních procesů limity a omezení relací. Po zjištění, tento problém s výkonem, pole Popis diagnostický protokol označuje, zda se tyto problémy s výkonem související s prostředků, pracovních procesů nebo omezení relace.

Prostředky pro službu SQL Database se obvykle označují [DTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu) nebo [vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) prostředky. Vzor dosažení omezení prostředků je rozpoznán při zjištění snížení výkonu dotazu je způsobeno tím, že žádné limity prostředků měřené dosáhnout.

Omezení prostředků relace označuje počet souběžných přihlášení k dispozici ke službě SQL database. Tento model výkonu je rozpoznán po aplikace, které jsou připojené k databázím SQL bylo dosaženo počtu dostupných souběžných přihlášení k databázi. Pokud se aplikace pokusí použít další relace, než je k dispozici na databázi, je vliv na výkon dotazů.

Dosažení omezení pracovních procesů je zvláštní případ dosáhnout omezení prostředků, protože nejsou dostupné pracovní procesy počítá využití DTU nebo vCore. Dosažení omezení pracovních procesů na databázi může způsobit vzestup specifické podle prostředků čekací dobu, což vede k snížení výkonu dotazů.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vypíše hodnoty hash dotazu dotazů, které měla vliv na výkon a procenta využití prostředků. Tyto informace můžete použít jako výchozí bod pro optimalizaci vašich úloh databáze. Konkrétně můžete optimalizovat dotazy, které ovlivňují snížení výkonu tak, že přidáte indexy. Nebo můžete optimalizovat aplikace s více i zatížení distribuci. Pokud nemůžete můžete snížit zatížení nebo nastavit optimalizace, zvažte zvýšení cenové úrovně předplatného databáze SQL zvýšit objem prostředků, které jsou k dispozici.

Pokud bylo dosaženo omezení dostupné relace, můžete optimalizovat aplikací snížením počtu přihlášení, provedené v databázi. Pokud nemůžete snížit počet přihlášení z vaší aplikace k databázi, zvažte zvýšení cenová úroveň databáze. Nebo můžete rozdělit a přesuňte databázi do více databází pro více vyvážené distribuce zatížení.

Další návrhy na řešení omezení relací, naleznete v tématu [jak zacházet s omezeními maximální přihlašovací jména SQL Database](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Zobrazit [Přehled prostředků omezuje na logickém serveru](sql-database-resource-limits-logical-server.md) informace o omezeních na úrovni serveru a předplatné.

## <a name="workload-increase"></a>Zvýšení zatížení

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu identifikuje problémy způsobené zvýšení zatížení, nebo v podobě závažnější pile-up pracovního vytížení.

Tato detekce se provádí prostřednictvím kombinace několik metrik. Základní metrika měří je zjistit zvýšení zatížení ve srovnání se standardními hodnotami posledních úloh. Další způsob, jak detekce vychází měření velký nárůst v aktivní pracovních vláken, který je dostatečně velký, aby vliv na výkon dotazů.

V podobě přísnější může zatížení průběžně hromadí lokalizovat služby SQL database pro zpracování úlohy. Výsledkem je neustále rostoucím velikost pracovního vytížení, které je podmínka pile-up pracovního vytížení. Kvůli této výjimce roste čas, který čeká na spuštění úlohy. Tento stav představuje jeden z nejzávažnější problémy s výkonem databáze. Tento problém byl zjištěn prostřednictvím monitorování nárůst počtu přerušené pracovní vlákna. 

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol Vypíše počet dotazů, jejichž provedení zvýšil a hodnota hash dotazu dotazu s největší příspěvek na zvýšení zatížení. Tyto informace můžete použít jako výchozí bod pro optimalizaci zatížení. Dotaz identifikována jako největší přispěvateli na zvýšení zatížení je užitečné zejména jako výchozí bod.

Můžete zvážit, distribuci zatížení rozkládalo rovnoměrněji do databáze. Uvažujte o optimalizaci dotazu, který ovlivňuje výkon tak, že přidáte indexy. Také může rozdělit vaše zatížení mezi několika databázemi. Pokud tato řešení není možné, zvažte zvýšení cenové úrovně předplatného databáze SQL zvýšit objem prostředků, které jsou k dispozici.

## <a name="memory-pressure"></a>Přetížení paměti

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu indikuje snížení výkonnosti v aktuální databázi výkonu způsobené tlaku na paměť, nebo v podobě závažnější podmínku pile-up paměti ve srovnání s poslední základní úrovně výkonu sedm dní.

Přetížení paměti označuje výkonovou podmínku, ve kterém je velký počet pracovních vláken udělí žádost o paměti v databázi SQL. Velký objem způsobí, že podmínku využití velkého množství paměti, ve kterém databáze SQL není schopen efektivně přidělit paměť pro všechny pracovní procesy, které si ji vyžádat. Mezi nejběžnější důvody pro tento problém se týká množství paměti k dispozici pro databáze SQL na jedné straně. Na druhé straně nárůst zatížení způsobí zvýšení v pracovních vláken a přetížení paměti.

Závažnější formu přetížení paměti je podmínka pile-up paměti. Tento stav indikuje, vyšší počet pracovních vláken žádáte uděluje paměti než dotazy uvolnění paměti. Tento počet pracovních vláken, které také udělí žádost o paměti mohou být nepřetržitě zvýšení (kumulování) vzhledem k tomu, že databázový stroj SQL není schopen efektivně dostatečně přidělit paměť pro splnění těchto požadavků. Stav paměti pile-up představuje jeden z nejzávažnější problémy s výkonem databáze.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupy podrobnosti úložiště paměti objektu s fulltextu (to znamená, pracovní vlákno) označené jako nejvyšší důvod vysoké využití paměti a relevantní časová razítka. Tyto informace můžete použít jako základ pro řešení potíží. 

Můžete optimalizovat nebo dotazů souvisejících s úředníci s nejvyšším využitím paměti odebrat. Také zajistit, že nejsou dotazování na data, který nemáte v úmyslu použít. Dobrým postupem je vždycky potřeba použít klauzuli WHERE v dotazech. Kromě toho doporučujeme vytvořit neseskupené indexy vyhledávání dat, než ho naskenovat.

Také můžete snížit zatížení optimalizace nebo distribuci přes více databází. Nebo můžete k distribuci vašich úloh mezi několik databází. Pokud tato řešení není možné, zvažte zvýšení cenové úrovně předplatného databáze SQL zvýšit objem paměťových prostředků dostupných v databázi.

Další tipy pro řešení potíží, najdete v části [paměti uděluje meditačních: záhadnými a často příjemce paměti systému SQL Server s mnoha názvy](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Uzamykání

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu indikuje snížení aktuální výkonu databáze, ve kterém uzamčení nadměrné databáze je zjištěn ve srovnání s poslední základní úrovně výkonu sedm dní. 

V moderních relační databázový systém a uzamčení je nezbytné k implementaci s více vlákny systémy, ve kterých se výkon maximalizuje spuštěním více souběžných pracovních procesů a paralelní databázové transakce, kde je to možné. Uzamčení v tomto kontextu označuje integrovaný přístup mechanismus, ve kterém můžete pouze jedné transakce výhradně přístup řádků, stránky, tabulky a soubory, které jsou vyžadovány a ne soutěžit s jinou transakcí pro prostředky. Po dokončení transakce, která uzamčené prostředky pro použití s nimi s těmito prostředky zámek je uvolněn, což umožňuje, aby ostatní transakce pro přístup k požadovaným prostředkům. Další informace o uzamčení, naleznete v tématu [uzamčení v databázovém stroji](https://msdn.microsoft.com/library/ms190615.aspx).

Pokud transakce provedeny modulem SQL čekají na delší dobu, po kterou se má přístup k prostředkům uzamčen pro použití, tato čekací doba způsobí, že zpomalení výkonu provádění úloh. 

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupy zamykání podrobnosti, které můžete použít jako základ pro řešení potíží. Můžete analyzovat ohlášené blokování dotazů, to znamená, dotazy, které zavádějí zamykání snížení výkonu, a je odebrat. V některých případech může být úspěšné při optimalizaci blokování dotazů.

Nejjednodušší a nejbezpečnější způsob, jak tyto problémy zmírnit je Udržovat transakce krátký a aby se snížil objem zámek nejdražší dotazů. Můžete rozdělit velké hromadné operace do menších operací. Aby se snížil objem zámek dotaz tak, že dotaz co nejúčinnější je dobrým zvykem. Snížit velké kontrol, protože zvýšit šanci na zablokování a nepříznivě ovlivnit celkový výkon databáze. Zjištěné dotazů, které způsobují uzamčení můžete vytvořit nový indexy nebo přidání sloupce do stávajícího indexu, aby se zabránilo prohledávání tabulky. 

Další návrhy najdete v části [řešení blokující problémy, které jsou způsobeny uzamknout Eskalace v systému SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zvýšená MAXDOP

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu označuje podmínku, ve kterém se plán provádění zvolený dotaz paralelizován více, než by byla. Optimalizace dotazů SQL Database můžete zvýšit výkon úloh, že zpracování dotazů paralelně věci urychlit, kde je to možné. V některých případech paralelních pracovních zpracování dotazu věnovat víc času čekáním na jiné k synchronizaci a sloučit výsledky porovnání se provádí stejný dotaz s menší počet paralelních pracovních procesů nebo dokonce v některých případech, ve srovnání s jeden pracovní podproces.

Expertní systém analyzuje aktuální výkonu databáze ve srovnání s směrné období. Určuje, zda dříve spuštěný dotaz běží pomaleji, než před vzhledem k tomu, že plán provádění dotazu je paralelizovaná více než by mělo být.

Možnosti konfigurace serveru MAXDOP pro službu SQL Database slouží k řízení, kolik Procesorových jader je možné spustit stejný dotaz paralelně. 

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vypíše hodnoty hash dotazu související s dotazy, u kterých zvýšit dobu trvání spuštění, protože byly více než měla být paralelizována. Protokol také výstup CXP čekací dobu. Tentokrát představuje čas, kdy jeden médií/koordinátor vlákno (vlákno 0) čeká na všechna vlákna do dokončení a sloučení výsledků a průběh. Kromě toho diagnostický protokol výstupy, které špatné provádí dotazy čekaly ve spuštění celkovou dobu čekání. Tyto informace můžete použít jako základ pro řešení potíží.

Nejprve optimalizovat nebo zjednodušení složitých dotazů. Dobrým postupem je rozdělte dlouhé dávkových úloh Hive do menších. Kromě toho Ujistěte se, které jste vytvořili indexů pro podporu vašich dotazů. Maximální volnost paralelismu (MAXDOP) můžete také ručně vynutit pro dotaz, který byla označena jako nízký. Tato operace konfigurace s použitím jazyka T-SQL, najdete v článku [nakonfigurovat možnosti konfigurace serveru MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Nastavení serveru MAXDOP možnosti konfigurace na nulu (0), protože výchozí hodnota označuje, že SQL Database můžete použít k dispozici logických jader procesoru paralelizovat vlákna pro provádění pomocí jediného dotazu. Nastavení MAXDOP pro jeden (1) označuje, že pouze jedno jádro lze použít pro provedení jednoho dotazu. V praxi to znamená, že je vypnuto paralelismu. V závislosti na základ za případ dostupných jader na databázi a Diagnostika protokolování informací, možnost MAXDOP počet jader využívaných pro provádění paralelního dotazu, který může být problém ve vašem případě můžete ladit.

## <a name="pagelatch-contention"></a>Pagelatch kolizí

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu indikuje snížení výkonu úlohy aktuální databáze z důvodu kolize pagelatch porovnané se standardními hodnotami v posledních sedm dní úlohy.

Zámky jsou zjednodušené synchronizace mechanismus používaný službou SQL Database k povolení multithreadingu. Jejich zaručit konzistenci struktury v paměti, které zahrnují indexy, datových stránek a jiných vnitřní struktury.

V databázi SQL jsou k dispozici mnoho typů zámků. V zájmu jednoduchosti vyrovnávací paměti zámky slouží k ochraně stránek v paměti ve fondu vyrovnávací paměti. Zámky vstupně-výstupních operací se používají k ochraně stránky ještě nebyla načtena do fondu vyrovnávacích pamětí. Pokaždé, když se data zapisují do nebo číst ze stránky ve fondu vyrovnávacích pamětí, pracovní vlákno je potřeba nejdřív získat západku vyrovnávací paměti pro stránku. Pokaždé, když pracovní vlákno se pokusí otevřít stránku, která ještě není k dispozici ve fondu vyrovnávacích pamětí, se požadavek vstupně-výstupní operace se načíst požadované informace z úložiště. Tahle posloupnost událostí označuje závažnější formu snížení výkonu.

Spory na zámků stránky vyvolá se v případě více vláken současně pokusí získat zámky na stejnou strukturu v paměti, která zavádí zvýšenou čekací doba pro provedení dotazu. V případě sporu pagelatch vstupně-výstupních operací, když data musí být přístupné ze služby storage, tato čekací doba je ještě větší. Značně může ovlivnit výkon úloh. Pagelatch kolizí je nejběžnější scénář vláken čekajících na sebe navzájem a soutěží o prostředky ve více procesorů systémech.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupy pagelatch podrobnosti o sporu. Tyto informace můžete použít jako základ pro řešení potíží.

Protože pagelatch je mechanismus interní kontroly služby SQL Database, automaticky určuje, kdy je můžete použít. Rozhodnutí o aplikaci, včetně návrhu schématu může ovlivnit chování pagelatch kvůli deterministické chování zámky.

Jedním ze způsobů pro zpracování západky překročila kolizí je sekvenční index klíč nahradit počátečních klíčem a vloží rovnoměrně distribuuje mezi rozsahem indexu. Obvykle počáteční sloupec v indexu distribuuje zatížení proporcionálně. Jinou metodou vezměte v úvahu je dělení tabulky. Vytvoření hodnoty hash s vypočítaným sloupcem na dělenou tabulku schéma vytváření oddílů je běžný postup pro snížení rizik souvisejících s nadměrné západku kolize. V případě sporu pagelatch vstupně-výstupních operací Představujeme indexy pomáhá ke zmírnění tohoto problému s výkonem. 

Další informace najdete v tématu [diagnostikovat a vyřešit opatřit kolizí v systému SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (soubor PDF ke stažení).

## <a name="missing-index"></a>Chybějící Index

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu indikuje aktuální databázi pracovního vytížení snížení výkonu ve srovnání s posledních sedmi dnů základní z důvodu chybějící index.

Index se používá ke zrychlení výkonu dotazů. Poskytuje rychlý přístup k datům table snížením počtu stránek datové sady, které vyžadují navštívili, nebo zkontrolovat.

Prostřednictvím této detekce, pro který by bylo výhodné výkon vytváření indexů se identifikují určité dotazy, které způsobit snížení výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vypíše hodnoty hash dotazu pro dotazy, které byly identifikovány ovlivňovat výkon úloh. Můžete sestavit indexy pro tyto dotazy. Můžete také optimalizovat nebo odstranit tyto dotazy, pokud nejsou povinné. Dobrý výkon postupem je Vyhněte se dotazování na data, které nepoužíváte.

> [!TIP]
> Víte, že integrované inteligentní funkce SQL Database může automaticky spravovat ty indexy databází?
>
> Pro optimalizaci výkonu průběžné služby SQL Database, doporučujeme, abyste povolili [automatické ladění SQL Database](sql-database-automatic-tuning.md). Tato jedinečná funkce SQL Database integrované inteligentní funkce nepřetržitě monitoruje vaši databázi SQL a automaticky vyladí a vytvoří indexy databází.
>

## <a name="new-query"></a>Nový dotaz

### <a name="what-is-happening"></a>Co se děje

Tento model výkonu indikuje, že nový dotaz byla nalezena, která je špatně provádění a by to mělo dopad na výkon úloh porovnané se standardními hodnotami výkonu sedm dní.

Zápis dobré provádění dotazu někdy může být náročné úlohy. Další informace o psaní dotazů najdete v tématu [psaní SQL dotazy](https://msdn.microsoft.com/library/bb264565.aspx). Optimalizujte stávající výkon dotazů, najdete v článku [optimalizace dotazů](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolování informací výstupy až dvě nové většina využívání procesoru dotazů, včetně jejich hodnotám hash dotazu. Protože zjištěné dotazu má vliv na výkon úloh, můžete optimalizovat dotaz. Dobrým postupem je načíst pouze data, která je třeba použít. Doporučujeme také používat dotazy s klauzulí WHERE. Doporučujeme také zjednodušení složitých dotazů a jejich rozdělte na menší dotazy. Jiné dobrým postupem je rozdělit velké dávkové dotazy na menší dotazy služby batch. Představujeme indexů pro nové dotazy je obvykle vhodné ke zmírnění tohoto problému s výkonem.

Zvažte použití [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Statistiky zvýšení čekání

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu indikuje snížení výkonu zatížení ve které jsou označeny špatné provádění dotazů ve srovnání s posledních sedm dní úlohy standardních hodnot.

V takovém případě systém nelze klasifikovat špatné provádění dotazů v ostatních kategoriích zjistitelná výkonu, ale zjistila za regresi čekání statistiky. Proto je vyhodnotí jako dotazů s *zvýšit statistiky čekání*, kde je zodpovědná za regresi čekání statistiky také přístupný. 

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolování informací výstupů na zvýšené čekací čas podrobnosti a hodnoty hash dotazu ovlivněné dotazů.

Protože systém nemohl úspěšně zjistit původní příčinu špatné provádění dotazů, diagnostických informací je dobrým výchozím bodem pro řešení potíží s ruční. Můžete optimalizovat výkon těchto dotazů. Dobrým postupem je načíst pouze data, která je nutné použít a zjednodušit a rozdělit do menších složitým dotazům. 

Další informace o optimalizaci výkonu dotazů najdete v tématu [optimalizace dotazů](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Databáze TempDB kolizí

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu označuje výkonovou podmínku databáze, ve které existuje kritickým bodem vláken pokusu o přístup k prostředkům databázi tempDB. (Tato podmínka není vstupně-výstupní operace související s). Typický scénář pro tento problém s výkonem se stovky souběžných dotazů, že všechny vytvářet, používat a potom vyřaďte databázi tempDB malé tabulky. Systém zjistil, že zvýšením počtu souběžných dotazů pomocí stejných tabulkách databáze tempDB s dostatečnou statistické význam má být ovlivněn výkon databáze ve srovnání s poslední základní úrovně výkonu sedm dní.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vrátí podrobnosti o sporu databázi tempDB. Informace můžete použít jako výchozí bod pro řešení potíží. Můžete pokračovat zmírnění tento druh kolize a zvýší propustnost celkovou úlohu dvě věci: můžete zastavit pomocí dočasných tabulek. Můžete použít také paměťově optimalizovaných tabulkách. 

Další informace najdete v tématu [Úvod do paměťově optimalizovaných tabulkách](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Nedostatek DTU elastického fondu

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu indikuje snížení výkonnosti v porovnání se směrným plánem posledních sedmi dnů výkon úloh aktuální databáze. Je z důvodu nedostatku dostupné Dtu v elastickém fondu vašeho předplatného. 

Prostředky pro službu SQL Database se obvykle označují jako [DTU prostředky](sql-database-service-tiers.md#dtu-based-purchasing-model), obsahovat kombinaci měření prostředky procesoru a vstupů/výstupů (dat a protokolů transakcí vstupně-výstupní operace). [Elastický fond Azure prostředky](sql-database-elastic-pool.md) slouží jako fond prostředky k dispozici eDTU, které jsou sdílené mezi několika databázemi pro účely škálování. Když prostředky k dispozici eDTU v elastickém fondu nejsou dostatečně velký pro podporu všech databází ve fondu, systém zjistí problém nedostatek výkonu jednotek DTU elastického fondu.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol Vypíše informace o elastickém fondu, uvádí hlavní využívání jednotek DTU databáze a poskytuje procento DTU fondu používá databáze využívající nahoru.

Vzhledem k tomu, že je tento stav výkonu související s více databází pomocí stejného fondu Edtu v elastickém fondu, postup řešení potíží se zaměřují na hlavní využívání jednotek DTU databáze. Můžete snížit zatížení databáze využívající horní zahrnující optimalizace dotazů horní využívání nad těmito databázemi. Můžete také zajistit, že nejsou dotazování na data, která nepoužíváte. Další možností je k optimalizaci aplikací pomocí hlavní databází využívání jednotek DTU a rozložit zatížení mezi několika databázemi.

Nejsou-li snížení a optimalizace aktuální pracovní zatížení hlavní databáze využívající DTU je to možné, zvažte zvýšení vašeho elastického fondu cenovou úroveň. Například navýšení počtu výsledků zvýšení dostupný počet jednotek Dtu elastického fondu.

## <a name="plan-regression"></a>Regrese plán

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu označuje podmínku, ve kterém SQL Database využívá plán spuštění neoptimální dotazu. Neoptimální plán obvykle způsobí, že spuštění zvýšenou dotazu, což vede k delšímu čekání aktuální a dalších dotazů.

SQL database určuje plán provádění dotazu s nejnižší náklady na provedení dotazu. Jako typ změny dotazy a úlohy někdy stávajících plánů už nejsou efektivní nebo možná SQL Database nevytvořili dobré posouzení. Jak opravy plánům spuštění dotazů se dá ručně vynutit.

Tento model zjistitelná výkonu kombinuje tři různé případy regrese plánu: nový plán regrese, původní plán regrese a existující úlohy změnit plány. Je součástí konkrétního typu plánu regrese, ke které došlo *podrobnosti* vlastnost v protokolu diagnostiky.

Nové podmínky regrese plán odkazuje na stavu, ve kterém SQL Database začne provádět nový plán provádění dotazu, který není tak účinné jako původní plán. Staré regrese podmínku plán týká stavu při přepnutí SQL Database pomocí nového, efektivnější plán na původní plán, který není tak účinné jako nový plán. Existující regrese změnit plány úloh odkazuje na stavu, ve kterém původní a nové plány průběžně alternativní, s zůstatek na účtu, budete více směrem k provádění špatné plánu.

Další informace o plánu regrese, naleznete v tématu [co je plán Regrese v systému SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vypíše hodnoty hash dotazu, ID dobrého plánu, plán chybné ID a dotazu ID. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete analyzovat, s jakým plánem je lepší pro určité dotazy, které chcete zjistit pomocí dotazu uvedené hodnoty hash. Až zjistíte, s jakým plánem fungovala lépe pro své dotazy, můžete ji ručně vynutit. 

Další informace najdete v tématu [zjistěte, jak systém SQL Server zabraňuje regresím plán](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Víte, že integrované inteligentní funkce SQL Database může automaticky spravovat ty plánům spuštění dotazů pro vaše databáze?
>
> Pro optimalizaci výkonu průběžné služby SQL Database, doporučujeme, abyste povolili [automatické ladění SQL Database](sql-database-automatic-tuning.md). Tato jedinečná funkce SQL Database integrované inteligentní funkce nepřetržitě monitoruje vaši databázi SQL a automaticky vyladí a vytvoří ty dotazu plánům spuštění vašich databází.
>

## <a name="database-scoped-configuration-value-change"></a>Změna hodnoty konfigurace s rozsahem databáze

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu označuje podmínku, ve kterém změny v konfiguraci s rozsahem databáze způsobí snížení výkonu, se zjištěným ve srovnání s poslední chování úloh databáze sedm dní. Tento model označuje, že nebude nedávné změny konfigurace s rozsahem databáze zdá se být výhodné výkonu vaší databáze.

Změny konfigurace s rozsahem databáze můžete nastavit pro každé jednotlivé databáze. Tato konfigurace se používá na případ od případu pro optimalizaci individuální výkon vaší databáze. Pro každé jednotlivé databáze můžete konfigurovat následující možnosti: MAXDOP LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES a vymazat PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolů výstupů s rozsahem databáze konfigurace provedené změny nedávno způsobující snížení výkonu ve srovnání s předchozím chování úloh sedm dní. Můžete vracet změny konfigurace na předchozí hodnoty. Také můžete vyladit hodnoty hodnotou dokud není dosaženo úrovně požadovaného výkonu. Obor databáze konfigurační hodnoty můžete zkopírovat z podobně jako databáze s vyhovující výkon. Pokud nemůžete vyřešit výkon, vrátit k výchozí databázi SQL výchozí hodnoty a pokuste se vyladění od těchto standardních hodnot.

Další informace o optimalizaci s rozsahem databáze konfigurace a syntaxi T-SQL na změnu konfigurace najdete v tématu [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Pomalé klienta

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkon tak rychle, jak databáze odesílá výsledky označuje podmínku, ve kterém klienta pomocí databáze SQL nelze využít výstup z databáze. Protože databáze SQL se ukládá výsledky prováděnou dotazů ve vyrovnávací paměti, může zpomalit a počká, aby klient mohl využívat výstupy přenášená dotazu než budete pokračovat. K tomuto stavu může také související k síti, která není dostatečně dostatečně rychle přenášet vytvořené jako výstupy z databáze SQL k používání klienta.

Tento stav se vygeneruje pouze v případě zjištění regrese výkonu ve srovnání s poslední chování úloh databáze sedm dní. Tento problém s výkonem se zjistí, že pouze v případě, že dojde k snížení statisticky významná výkonu ve srovnání s předchozím chování výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Tento model zjistitelná výkonu označuje podmínku, na straně klienta. Řešení potíží s vyžádáním na aplikace na straně klienta nebo klientské sítě. Diagnostický protokol vypíše hodnoty hash dotazu a dobu čekání, které vypadá to, že se čeká na maximum pro klienta k využití v posledních dvou hodinách. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat výkon vaší aplikace pro používání těchto dotazů. Můžete zvážit také problémy s latencí sítě je to možné. Vzhledem k tomu, že problém snížení výkonu byl založen na změnu v hodnotě poslední základní úrovně výkonu sedm dní, můžete prozkoumat, jestli nedávné změny aplikací nebo sítí podmínku způsobil této události regrese výkonu. 

## <a name="pricing-tier-downgrade"></a>Přechod na starší cenová úroveň

### <a name="what-is-happening"></a>Co se děje

Tento model zjistitelná výkonu označuje podmínku, ve kterém byl snížit cenovou úroveň předplatného SQL Database. Z důvodu omezení prostředků pro databáze (Dtu) systém zjistil pokles v aktuální výkonu databáze porovnané se standardními hodnotami v posledních sedm dní.

Kromě toho může být podmínka, ve kterém byl cenovou úroveň předplatného SQL Database downgradovat a poté provedli upgrade na vyšší úroveň během krátké doby. Detekce toto snížení výkonu dočasné je výstupem v sekci podrobností diagnostický protokol jako cen přechod na starší úroveň a upgrade.

### <a name="troubleshooting"></a>Řešení potíží

Je-li snížit cenovou úroveň a proto Dtu databáze SQL k dispozici, a budete spokojeni s výkonem, nic, co je potřeba. Je-li snížit cenovou úroveň a budete spokojeni se výkon vaší databáze SQL, snížit vaše databázové procesy využívající nebo zvažte zvýšení cenovou úroveň na vyšší úrovni.

## <a name="recommended-troubleshooting-flow"></a>Doporučené řešení potíží s toku

 Postupujte podle vývojový diagram pro doporučený postup pro řešení problémů s výkonem s použitím inteligentní přehledy.

Intelligent Insights přístup prostřednictvím webu Azure portal tak, že přejdete do služby Azure SQL Analytics. Pokus o příchozí výstrah výkonu a vyberte ji. Zjistit, co se děje na stránce detekcí. Sledujte analýza zadaného původní příčiny problému, text dotazu, dotaz čas trendy a incidentů vývoj. Pokus o vyřešení problému s využitím inteligentních přehledů doporučení pro snížení rizik souvisejících s výkonem. 

[![Vývojový diagram odstraňování potíží](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Vyberte vývojový diagram stahovat PDF verzi.

Intelligent Insights musí obvykle hodinu čas k provedení analýzy hlavní příčiny potíží s výkonem. Pokud nemůžete najít svůj problém v Intelligent Insights a je pro vás velmi důležité, pomocí Query Store ručně zjistit původní příčinu potíží s výkonem. (Tyto problémy jsou obvykle méně než hodinu stará.) Další informace najdete v tématu [monitorování výkonu pomocí Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Další postup
- Přečtěte si [Intelligent Insights](sql-database-intelligent-insights.md) koncepty.
- Použití [protokolování diagnostiky výkonu Intelligent Insights Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitorování [Azure SQL Database s využitím Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Zjistěte, jak [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

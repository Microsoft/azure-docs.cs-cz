---
title: Řešení potíží s výkonem s využitím služby Intelligent Insights
description: Intelligent Insights pomáhá řešit problémy s výkonem Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: c42db1445c939069f334d04ea26d54cdb843c336
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488823"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Řešení potíží s Azure SQL Database a problémy s výkonem spravované instance Azure SQL pomocí Intelligent Insights
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tato stránka poskytuje informace o Azure SQL Database a potížích s výkonem spravované instance Azure SQL, které byly zjištěny prostřednictvím protokolu [Intelligent Insights](intelligent-insights-overview.md) prostředků. Metriky a protokoly prostředků se dají streamovat do [Azure monitor protokolů](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)nebo řešení třetí strany pro vlastní funkce upozorňování a vytváření sestav DevOps.

> [!NOTE]
> Průvodce odstraňováním potíží s rychlým výkonem pomocí Intelligent Insights najdete v tématu [doporučený vývojový diagram postupu řešení potíží](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) v tomto dokumentu.
>
> Inteligentní přehledy jsou funkce ve verzi Preview, která není dostupná v následujících oblastech: Západní Evropa, Severní Evropa, Západní USA 1 a Východní USA 1.

## <a name="detectable-database-performance-patterns"></a>Zjistitelné vzorce výkonu databáze

Intelligent Insights automaticky detekuje problémy s výkonem na základě čekací doby spuštění dotazu, chyb nebo časových limitů. Intelligent Insights výstupy zjistily vzorce výkonu v protokolu prostředků. V následující tabulce jsou shrnuty zjistitelné vzorce výkonu.

| Rozpoznatelné vzory výkonu | Azure SQL Database | Spravovaná instance Azure SQL |
| :------------------- | ------------------- | ------------------- |
| [Dosažení limitů prostředků](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Vaše omezení prostředků dosáhly spotřeby dostupných prostředků (DTU), pracovních vláken databáze nebo relací přihlášení k databázi dostupným u monitorovaného předplatného. To má vliv na výkon. | Spotřeba prostředků procesoru dosáhne svých omezení prostředků. To má vliv na výkon databáze. |
| [Zvýšení zatížení](intelligent-insights-troubleshoot-performance.md#workload-increase) | Zjistilo se zvýšení zátěže nebo nepřetržité akumulace úloh v databázi. To má vliv na výkon. | Bylo zjištěno zvýšení zátěže. To má vliv na výkon databáze. |
| [Tlak paměti](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Zaměstnanci, kteří vyžadují nároky na paměť, musí čekat na přidělení paměti pro statisticky významné množství času nebo zvýšené akumulace pracovníků, kteří vyžádali nároky na paměť. To má vliv na výkon. | Zaměstnanci, kteří požadují nároky na paměť, čekají na přidělení paměti ve statistickém významném časovém intervalu. To má vliv na výkon databáze. |
| [Uzamčení](intelligent-insights-troubleshoot-performance.md#locking) | Bylo zjištěno nadměrné uzamčení databáze ovlivňující výkon. | Bylo zjištěno nadměrné uzamčení databáze ovlivňující výkon databáze. |
| [Zvýšená MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Možnost Maximální stupeň paralelismu (MAXDOP) se změnila vlivem efektivity provádění dotazu. To má vliv na výkon. | Možnost Maximální stupeň paralelismu (MAXDOP) se změnila vlivem efektivity provádění dotazu. To má vliv na výkon. |
| [PAGELATCH spory](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Více vláken se souběžně pokouší o přístup ke stejné stránce vyrovnávací paměti dat v paměti, což vede k nárůstu čekací doby a způsobila kolize PAGELATCH. To má vliv na výkon. | Více vláken se souběžně pokouší o přístup ke stejné stránce vyrovnávací paměti dat v paměti, což vede k nárůstu čekací doby a způsobila kolize PAGELATCH. To má vliv na výkon databáze. |
| [Chybějící index](intelligent-insights-troubleshoot-performance.md#missing-index) | Byl zjištěn chybějící index, který má vliv na výkon. | Byl zjištěn chybějící index vlivu na výkon databáze. |
| [Nový dotaz](intelligent-insights-troubleshoot-performance.md#new-query) | Byl zjištěn nový dotaz ovlivňující celkový výkon. | Byl zjištěn nový dotaz vlivu na celkový výkon databáze. |
| [Zvýšená Statistika čekání](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Zvýšily se doby čekání databáze, které mají vliv na výkon. | Bylo zjištěno zvýšení doby čekání databáze ovlivňující výkon databáze. |
| [Obsah databáze TempDB](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Více vláken se snaží získat přístup ke stejnému prostředku databáze TempDB, což způsobuje kritický bod. To má vliv na výkon. | Více vláken se snaží získat přístup ke stejnému prostředku databáze TempDB, což způsobuje kritický bod. To má vliv na výkon databáze. |
| [Nedostatek DTU elastického fondu](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Nedostatek dostupných eDTU v elastickém fondu má vliv na výkon. | Není k dispozici pro spravovanou instanci Azure SQL, protože používá model vCore. |
| [Plánování regrese](intelligent-insights-troubleshoot-performance.md#plan-regression) | Zjistil se nový plán nebo změna zatížení stávajícího plánu. To má vliv na výkon. | Zjistil se nový plán nebo změna zatížení stávajícího plánu. To má vliv na výkon databáze. |
| [Změna hodnoty konfigurace v oboru databáze](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Zjistila se změna konfigurace v databázi ovlivňující výkon databáze. | Zjistila se změna konfigurace v databázi ovlivňující výkon databáze. |
| [Pomalý klient](intelligent-insights-troubleshoot-performance.md#slow-client) | Pomalý klient aplikace nemůže využít výstup z databáze dostatečně rychle. To má vliv na výkon. | Pomalý klient aplikace nemůže využít výstup z databáze dostatečně rychle. To má vliv na výkon databáze. |
| [Downgrade cenové úrovně](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Akce downgrade cenové úrovně snížila dostupné prostředky. To má vliv na výkon. | Akce downgrade cenové úrovně snížila dostupné prostředky. To má vliv na výkon databáze. |

> [!TIP]
> Pro průběžnou optimalizaci výkonu databází povolte [Automatické ladění](automatic-tuning-overview.md). Tato integrovaná funkce inteligentních funkcí průběžně monitoruje vaši databázi, automaticky optimalizuje indexy a aplikuje opravy plánu provádění dotazů.
>

Následující část popisuje zjistitelné vzorce výkonu podrobněji.

## <a name="reaching-resource-limits"></a>Dosažení limitů prostředků

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu kombinuje problémy s výkonem, které se týkají dosahování dostupných limitů prostředků, omezení pracovních procesů a omezení relací. Po zjištění tohoto problému s výkonem bude pole Popis diagnostického protokolu označovat, zda je problém s výkonem spojen s omezeními prostředků, pracovních procesů nebo relací.

Prostředky na Azure SQL Database se typicky označují jako [DTU](service-tiers-dtu.md) nebo prostředky [Vcore](service-tiers-vcore.md) a prostředky na spravované instanci Azure SQL se označují jako prostředky Vcore. Způsob, jakým se dosáhne omezení počtu prostředků, se rozpozná, když se zjistí, že se zjistilo snížení výkonu dotazů, díky většímu počtu měřených prostředků.

Počet omezení prostředku označuje počet dostupných souběžných přihlášení k databázi. Tento vzor výkonu se rozpozná, když aplikace, které jsou připojené k databázím, dosáhly počtu dostupných souběžných přihlášení k databázi. Pokud se aplikace pokusí použít více relací, než je v databázi k dispozici, je ovlivněn výkon dotazů.

Dosažení limitů pracovních procesů je konkrétní případ dosahování omezení prostředků, protože dostupné pracovní procesy se nezapočítávají do DTU nebo vCore využití. Dosažení limitů pracovních procesů v databázi může způsobit dobu čekání specifickou pro prostředky, což vede ke snížení výkonu dotazů.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol obsahuje výstupy dotazů na hodnoty hash dotazů, které ovlivnily procentuální hodnoty výkonu a využití prostředků. Tyto informace můžete použít jako výchozí bod pro optimalizaci zatížení databáze. Konkrétně můžete optimalizovat dotazy, které ovlivňují snížení výkonu přidáním indexů. Nebo můžete optimalizovat aplikace s více než distribucí úloh. Pokud nemůžete snížit zatížení nebo provést optimalizace, zvažte zvýšení cenové úrovně předplatného vaší databáze, abyste zvýšili množství dostupných zdrojů.

Pokud jste dosáhli dostupných omezení relací, můžete optimalizovat aplikace tím, že omezíte počet přihlášení provedených v databázi. Pokud nemůžete snížit počet přihlášení z vašich aplikací do databáze, zvažte zvýšení cenové úrovně vašeho odběru databáze. Nebo můžete databázi rozdělit a přesunout do více databází, abyste mohli pružně rozloženou distribuci úloh.

Další návrhy na řešení omezení relací najdete v tématu [jak se zabývat omezeními maximálního počtu přihlášení](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Informace o omezeních na úrovni serveru a předplatného najdete v tématu [Přehled omezení prostředků na serveru](resource-limits-logical-server.md) .

## <a name="workload-increase"></a>Zvýšení zatížení

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu identifikuje problémy způsobené zvýšením zátěže nebo v nejpřísnějším tvaru zatížení.

Tato detekce se provádí kombinací několika metrik. Měření základní metriky detekuje zvýšení zatížení v porovnání s předchozími standardními hodnotami úloh. Další forma detekce je založena na měření velkého nárůstu aktivních pracovních vláken, která jsou dostatečně velká pro ovlivnění výkonu dotazů.

V nejpřísnější formě se může úloha průběžně rozpracovat z důvodu neschopnosti databáze zpracovávat úlohy. Výsledkem je průběžné zvětšování velikosti pracovního vytížení, což je podmínka pro sestavování zatížení. Z důvodu této podmínky je čas, kdy zatížení čeká na spuštění, růst. Tento stav představuje jeden z nejzávažnějších problémů s výkonem databáze. Tento problém se detekuje prostřednictvím monitorování zvýšení počtu přerušených pracovních vláken.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vystaví počet dotazů, jejichž spuštění se zvýšilo, a hodnotu hash dotazu dotazu, který má největší podíl na zvýšení zatížení. Tyto informace můžete použít jako výchozí bod pro optimalizaci zatížení. Dotaz identifikovaný jako největší přispěvatel na zvýšení zatížení je zvláště užitečný jako výchozí bod.

Můžete uvažovat o rovnoměrné distribuci úloh do databáze. Zvažte optimalizaci dotazu, který má vliv na výkon přidáním indexů. Vaše úlohy můžete také distribuovat mezi více databází. Pokud tato řešení nejsou možná, zvažte zvýšení cenové úrovně vašeho předplatného databáze a zvyšte množství dostupných prostředků.

## <a name="memory-pressure"></a>Přetížení paměti

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu indikuje snížení výkonu aktuální databáze způsobené tlakem paměti, nebo v přísnějším tvaru podmínky pro sestavování paměti, a to v porovnání s minulým 7 denním směrným plánem výkonu.

Tlak paměti označuje stav výkonu, ve kterém je velký počet pracovních vláken požadujících udělení paměti. Vysoký svazek způsobuje stav vysokého využití paměti, ve kterém databáze nemůže efektivně přidělit paměť všem pracovníkům, kteří je požadují. Jedním z nejběžnějších příčin tohoto problému je v souvislosti s množstvím paměti dostupnými pro databázi na jedné straně. Na druhé straně zvýšení zátěže způsobuje zvýšení počtu pracovních vláken a zatížení paměti.

Přísnější forma zatížení paměti je podmínka pro sestavování paměti. Tento stav označuje, že větší počet pracovních vláken požaduje žádosti o paměť, než kolik dotazů uvolňuje paměť. Tento počet pracovních vláken požadujících nároky na paměť se taky může průběžně zvyšovat (piling), protože databázový stroj nemůže dostatečně efektivně přidělit paměť, aby splnila požadavky. Podmínka rozstavování paměti představuje jeden z nejzávažnějších problémů s výkonem databáze.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupuje informace o úložišti objektů paměti s údajem o pracovníkovi (tj. pracovní vlákno) označené jako nejvyšší důvod pro vysoké využití paměti a příslušná časová razítka. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat nebo odebrat dotazy týkající se úředníků s nejvyšším využitím paměti. Také se můžete ujistit, že nechcete zadávat dotazy na data, která neplánujete použít. Dobrým postupem je vždy používat v dotazech klauzuli WHERE. Kromě toho doporučujeme, abyste vytvořili neclusterované indexy pro hledání dat místo kontroly.

Můžete také snížit zatížení tím, že ho optimalizujete nebo distribuujete přes více databází. Nebo můžete distribuovat úlohy mezi více databázemi. Pokud tato řešení nejsou možná, zvažte zvýšení cenové úrovně vaší databáze, aby se zvýšila velikost paměťových prostředků, které jsou k dispozici pro databázi.

Další návrhy pro řešení potíží najdete v tématu [paměť udělující Meditation: příjemce záhadnými SQL Server paměti s mnoha názvy](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Uzamčení

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu indikuje snížení výkonu aktuální databáze, ve kterém se v porovnání s minulým 7 denním stupněm výkonu detekuje nadměrné uzamykání databáze.

V moderních RDBMS jsou zámky zásadní pro implementaci vícevláknových systémů, ve kterých se výkon maximalizuje tím, že spustí víc souběžných pracovních procesů a paralelních transakcí databáze tam, kde je to možné. Uzamykání v tomto kontextu odkazuje na vestavěný přístupový mechanismus, ve kterém má výhradní přístup k řádkům, stránkám, tabulkám a souborům, které jsou povinné a které nesoutěží s jinou transakcí pro prostředky, jenom jedna transakce. Při použití transakce, která uzamkl prostředky k použití, se u těchto prostředků uvolní zámek, který umožňuje ostatním transakcím přístup k požadovaným prostředkům. Další informace o uzamykání najdete v tématu [zámek v databázovém stroji](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Pokud transakce prováděné nástrojem SQL Engine čekají na získání přístupu k prostředkům uzamčeným pro použití, tato čekací doba způsobí zpomalení výkonu při provádění úloh.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupuje informace o zamykání, které můžete použít jako základ pro řešení potíží. Můžete analyzovat hlášené blokující dotazy, to znamená dotazy, které zavádějí snížení výkonu uzamykání, a odebrat je. V některých případech může být při optimalizaci blokujících dotazů úspěšný.

Nejjednodušší a nejbezpečnější způsob, jak tento problém zmírnit, je udržet krátké transakce a snížit nároky na nejbezpečnější počet nejdražších dotazů. Velkou dávku operací můžete rozdělit do menších operací. Dobrým postupem je snížit nároky na zámek dotazů tím, že dotaz vyměníte tak, aby co nejefektivnější bylo. Snižte Velká prověřování, protože zvyšují pravděpodobnost zablokování a nepříznivě ovlivní celkový výkon databáze. U identifikovaných dotazů, které způsobují uzamykání, můžete vytvořit nové indexy nebo přidat sloupce do existujícího indexu, abyste se vyhnuli prohledávání tabulky.

Další návrhy najdete v tématu řešení [potíží blokujících problémy, které jsou způsobeny eskalací zámku v SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zvýšená MAXDOP

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje stav, ve kterém byl zvolený plán spuštění dotazu více paralelní, než by měl být. Optimalizátor dotazů může zlepšit výkon úloh tím, že se paralelně spouští dotazy, aby se co nejrychleji urychlily. V některých případech Parallel Worker zpracovává dotazy stráví více času tím, že synchronizuje a sloučí výsledky v porovnání se spouštěním stejného dotazu s menším počtem paralelních pracovních procesů, nebo i v některých případech v porovnání s jedním pracovním vláknem.

Systém expertů analyzuje aktuální výkon databáze v porovnání se směrným obdobím. Určuje, zda byl dříve spuštěný dotaz pomalejší než dřív, protože plán spouštění dotazů je více paralelismuější, než by měl být.

Možnost konfigurace serveru MAXDOP slouží k řízení toho, kolik PROCESORových jader lze použít k paralelnímu spuštění stejného dotazu.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostické protokoly výstupy dotazují hodnoty hash týkající se dotazů, u kterých se doba spuštění zvýšila, protože byla větší než v nich. Protokol také výstupy CXP čekací doby. Tento čas představuje čas jednoho nebo více vláken (vlákno 0) čekajících na dokončení všech ostatních vláken před sloučením výsledků a přesunutím dopředu. Kromě toho diagnostický protokol vypíše dobu čekání, kterou nekvalitní dotazy čekaly na celkovém spuštění. Tyto informace můžete použít jako základ pro řešení potíží.

Nejprve Optimalizujte nebo Zjednodušte složité dotazy. Dobrým postupem je rozdělení dlouhých dávkových úloh na menší. Kromě toho se ujistěte, že jste vytvořili indexy pro podporu vašich dotazů. Můžete také ručně vyhovět maximálnímu stupni paralelismu (MAXDOP) pro dotaz, který byl označen jako nekvalitní provádění. Pokud chcete tuto operaci nakonfigurovat pomocí T-SQL, přečtěte si téma [Konfigurace možnosti konfigurace serveru MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Nastavení možnosti konfigurace serveru MAXDOP na hodnotu nula (0) jako výchozí hodnota znamená, že databáze může použít všechny dostupné jádra procesoru k paralelizovat vláknům pro spuštění jednoho dotazu. Nastavení MAXDOP na jednu (1) značí, že k jednomu spuštění dotazu lze použít pouze jednu jader. V praktických případech to znamená, že paralelismus je vypnutý. V závislosti na jednotlivých případech, dostupných jádrech databáze a diagnostickém protokolu můžete ladit možnost MAXDOP na počet jader používaných pro paralelní zpracování dotazů, které by mohly problém vyřešit.

## <a name="pagelatch-contention"></a>PAGELATCH spory

### <a name="what-is-happening"></a>Co se děje

Tento vzorec výkonu indikuje, že aktuální úloha snížení výkonu z důvodu PAGELATCH kolize v porovnání s minulým 7 denním zatížením.

Zámky jsou odlehčené synchronizační mechanismy, které slouží k povolení multithreadingu. Zaručují konzistenci struktur v paměti, které obsahují indexy, datové stránky a další interní struktury.

K dispozici je celá řada typů zámků. V zájmu zjednodušení se k ochraně stránek v paměti ve fondu vyrovnávací paměti používají západce vyrovnávací paměti. Zámky v/v slouží k ochraně stránek, které ještě nebyly načteny do fondu vyrovnávací paměti. Pokaždé, když jsou data zapisována nebo čtena ze stránky ve fondu vyrovnávací paměti, musí pracovní vlákno nejprve získat západku vyrovnávací paměti. Pokaždé, když se pracovní podproces pokusí o přístup ke stránce, která již není dostupná ve fondu vyrovnávací paměti, je k načtení požadovaných informací z úložiště proveden požadavek v/v. Tato posloupnost událostí označuje více závažnou formu snížení výkonu.

Kolizí na západce stránky nastává, když se více vláken současně pokouší získat zámky ve stejné struktuře v paměti, což zavádí zvýšenou dobu čekání na provedení dotazu. V případě kolize PAGELATCH v/v, kdy se data musí dostat z úložiště, je tato čekací doba ještě větší. Může to značně ovlivnit výkon úloh. Kolize PAGELATCH je nejběžnější scénář, který se na sebe vzájemně čeká a který je konkurenční pro prostředky v několika systémech CPU.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostické protokoly výstupy PAGELATCH podrobnosti o sporech. Tyto informace můžete použít jako základ pro řešení potíží.

Vzhledem k tomu, že PAGELATCH je mechanismus interního řízení, určuje automatické určení, kdy se mají použít. Rozhodnutí o aplikacích, včetně návrhu schématu, mohou ovlivnit chování PAGELATCH z důvodu deterministického chování zámků.

Jednou z metod pro zpracování kolizí zámků je nahrazení sekvenčního klíče indexu pomocí nesekvenčního klíče, aby bylo možné rovnoměrně distribuovat vložení do rozsahu indexu. První sloupec v indexu obvykle distribuuje úlohu poměrně. Další metodou, kterou je třeba zvážit, je vytváření oddílů tabulky. Vytvoření schématu vytváření oddílů hash s vypočítaným sloupcem v dělené tabulce je běžným přístupem pro zmírnění nadměrného kolizí zámků. V případě kolize PAGELATCH v/v, zavedení indexů pomáhá zmírnit tento problém s výkonem.

Další informace najdete v tématu [Diagnostika a řešení kolizí na západce při SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (stažení PDF).

## <a name="missing-index"></a>Chybějící index

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu indikuje, že aktuální zatížení databázové úlohy je v porovnání s neplatným 7 dny vzhledem k chybějícímu indexu.

Index se používá ke zrychlení výkonu dotazů. Poskytuje rychlý přístup k tabulkovým datům tím, že snižuje počet stránek datové sady, které je potřeba navštívit nebo kontrolovat.

Konkrétní dotazy, které způsobily snížení výkonu, jsou identifikovány prostřednictvím této detekce, pro které by vytváření indexů mohlo být přínosem výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostické protokoly výstupy dotazů pro dotazy, které byly identifikovány pro ovlivnění výkonu úloh. Můžete sestavit indexy pro tyto dotazy. Tyto dotazy můžete optimalizovat nebo odebrat i v případě, že se nevyžadují. Dobrým postupem výkonu je vyhnout se dotazování na data, která nepoužíváte.

> [!TIP]
> Věděli jste, že integrované inteligentní funkce můžou automaticky spravovat nejlepší indexy pro vaše databáze?
>
> V případě optimalizace průběžného výkonu doporučujeme povolit [Automatické ladění](automatic-tuning-overview.md). Tato jedinečná integrovaná funkce inteligentních funkcí průběžně monitoruje vaši databázi a automaticky ladí a vytváří indexy pro vaše databáze.
>

## <a name="new-query"></a>Nový dotaz

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu indikuje, že se zjistil nový dotaz, který je špatně prováděný a ovlivňuje výkon úloh v porovnání s denním směrným plánem výkonu.

Psaní vhodného dotazu někdy může být náročný úkol. Další informace o zápisu dotazů naleznete v tématu [zápis SQL dotazů](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)). Pokud chcete optimalizovat stávající výkon dotazů, přečtěte si téma [ladění dotazů](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupuje informace až do dvou nových většiny dotazů využívajících procesor, včetně jejich hodnot hash dotazů. Vzhledem k tomu, že zjištěný dotaz má vliv na výkon úlohy, můžete optimalizovat svůj dotaz. Dobrým postupem je načíst jenom data, která potřebujete použít. Doporučujeme také používat dotazy s klauzulí WHERE. Doporučujeme také, abyste zjednodušili složité dotazy a rozdělují je na menší dotazy. Dalším osvědčeným postupem je rozdělení velkých dávkových dotazů na menší dávkové dotazy. Zavedení indexů pro nové dotazy je obvykle dobrým zvykem pro zmírnění tohoto problému s výkonem.

V Azure SQL Database zvažte použití [Query Performance Insight](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Zvýšená Statistika čekání

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje snížení výkonu úlohy, při kterém jsou zjištěny nekvalitní dotazy v porovnání s minulými sedmi denními plány úloh.

V takovém případě systém nemůže klasifikovat nekvalitní dotazy v rámci jakýchkoli jiných standardních kategorií výkonu, ale zjistil, že je statistika čekání zodpovědná za regresi. Proto je považuje za dotazy se *zvýšenými statistikami čekání*, kde je také zpřístupněna Statistika čekání zodpovědná za regresi.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol výstupuje informace o zvýšené době čekání a hodnotách hash dotazů příslušných dotazů.

Vzhledem k tomu, že systém nemohl úspěšně identifikovat hlavní příčinu nekvalitních dotazů, jsou diagnostické informace dobrým výchozím bodem pro ruční odstraňování potíží. Můžete optimalizovat výkon těchto dotazů. Dobrým postupem je načíst jenom data, která potřebujete použít, a zjednodušit a rozdělte složité dotazy na menší.

Další informace o optimalizaci výkonu dotazů najdete v tématu [optimalizace dotazů](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>Obsah databáze TempDB

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje stav výkonu databáze, ve kterém existuje kritické místo pro vlákna, které se pokoušejí získat přístup k prostředkům databáze tempDB. (Tato podmínka nesouvisí s v/v.) Typický scénář pro tento problém s výkonem je stovky souběžných dotazů, které při vytváření, používání a vyřazení malých tabulek tempDB využívají. Systém zjistil, že počet souběžných dotazů, které používají stejné tabulky tempDB, se zvýšil s dostatečným statistickým významem, který má vliv na výkon databáze v porovnání s minulými sedmi denními směrnými plány.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostické protokoly mají výstupy podrobností o kolize obsahu tempDB. Tyto informace můžete použít jako výchozí bod pro řešení potíží. Existují dvě věci, které můžete využít k zmírnění tohoto typu sporů a zvýšení propustnosti celkové zátěže: dočasné tabulky můžete přestat používat. Můžete také použít paměťově optimalizované tabulky.

Další informace najdete v tématu [Úvod do paměťově optimalizovaných tabulek](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Nedostatek DTU elastického fondu

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje snížení výkonu aktuální databázové úlohy v porovnání s denním dnem 7 dní. Je to z důvodu nedostatku dostupného Dtuu v elastickém fondu vašeho předplatného.

[Prostředky elastického fondu Azure](elastic-pool-overview.md) se používají jako fond dostupných prostředků sdílených mezi více databázemi pro účely škálování. Pokud dostupné prostředky eDTU v elastickém fondu nejsou dostatečně velké, aby podporovaly všechny databáze ve fondu, systém nezjistí problém s výkonem elastického fondu DTU.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostické protokoly výstupy obsahují informace o elastickém fondu, uvádí nejvyšší DTU náročné databáze a poskytuje procentní podíl DTU fondu, který využívala nejvyšší databáze.

Vzhledem k tomu, že tento stav výkonu souvisí s více databázemi pomocí stejného fondu eDTU v elastickém fondu, postup řešení potíží se zaměřuje na nejdůležitější databáze náročné na DTU. Můžete snížit zatížení na vysoce náročných databázích, které zahrnují optimalizaci vysoce náročných dotazů na tyto databáze. Také se můžete ujistit, že nepoužíváte dotazování na data, která nepoužíváte. Dalším přístupem je optimalizace aplikací s využitím horních databází náročných na DTU a opětovné distribuce úloh mezi více databázemi.

Pokud není možné snížit a optimalizovat aktuální zatížení na nejvyšší náročné databáze s využitím DTU, zvažte zvýšení cenové úrovně elastického fondu. Toto zvýšení má za následek zvýšení dostupného Dtuu v elastickém fondu.

## <a name="plan-regression"></a>Plánování regrese

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje podmínku, ve které databáze využívá optimální plán spouštění dotazů. Neoptimální plán obvykle způsobuje zvýšené provádění dotazů, což vede k delší době čekání pro aktuální a další dotazy.

Databázový stroj určuje plán spouštění dotazů s nejmenšími náklady na provedení dotazu. V důsledku změny typu dotazů a zatížení někdy existující plány již nejsou efektivní, nebo pravděpodobně databázový stroj nevedl k dobrému posouzení. V důsledku opravy se dají plány spouštění dotazů ručně vynutit.

Tento zjistitelný vzor výkonu kombinuje tři různé případy regrese: nový plán regrese, regresní regrese a stávající plány změnily zatížení. Konkrétní typ regresní regrese, ke kterému došlo, je k dispozici ve vlastnosti *Details* v diagnostickém protokolu.

Podmínka nový plán regrese odkazuje na stav, ve kterém databázový stroj spustí spuštění nového plánu provádění dotazů, který není tak efektivní jako původní plán. Podmínka regrese starého plánu odkazuje na stav, když databázový stroj přepne z použití nového, účinnějšího plánu do starého plánu, který není tak efektivní jako nový plán. Stávající plány změnily regresní úlohy, které se týkají stavu, ve kterém se stará a nové plány průběžně střídavě vztahují k tomuto zůstatmu plánu.

Další informace o plánování regresí najdete v tématu [co je plánování regresí v SQL Server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server).

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol vypíše hodnoty hash dotazů, dobrý identifikátor ID, ID chybného plánu a ID dotazů. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete analyzovat, který plán je lepší pro konkrétní dotazy, které můžete identifikovat pomocí zadaných hodnot hash dotazů. Až zjistíte, který plán pro vaše dotazy funguje lépe, můžete ho ručně vynutit.

Další informace najdete v tématu [informace o tom, jak SQL Server brání v plánování regresí](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Věděli jste, že integrovaná funkce inteligentních funkcí dokáže automaticky spravovat nejlepší plány provádění dotazů pro vaše databáze?
>
> V případě optimalizace průběžného výkonu doporučujeme povolit [Automatické ladění](automatic-tuning-overview.md). Tato integrovaná funkce inteligentních funkcí průběžně monitoruje vaši databázi a automaticky ladí a vytváří osvědčené plány provádění dotazů pro vaše databáze.

## <a name="database-scoped-configuration-value-change"></a>Změna hodnoty konfigurace v oboru databáze

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje stav, ve kterém Změna konfigurace v rámci databáze způsobuje regresi výkonu, která je zjištěna v porovnání s minulým 7 denním chováním úloh. Tento model označuje, že nedávná změna v konfiguraci s rozsahem databáze nemusí být výhodná pro váš výkon databáze.

Změny konfigurace v rámci databáze lze nastavit pro každou jednotlivou databázi. Tato konfigurace se používá na základě případu pro optimalizaci individuálního výkonu vaší databáze. Pro každou jednotlivou databázi je možné nakonfigurovat následující možnosti: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES a vymazat PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostický protokol má za následek změny konfigurace v rozsahu databáze, které byly nedávno způsobeny snížením výkonu v porovnání s předchozím 7 denním chováním úloh. Změny konfigurace můžete vrátit zpět na předchozí hodnoty. Můžete také ladit hodnotu podle hodnoty, dokud nebude dosaženo požadované úrovně výkonu. Hodnoty konfigurace pro rozsah databáze můžete kopírovat z podobné databáze s uspokojivým výkonem. Pokud se vám nedaří vyřešit výkon, vraťte se k výchozím hodnotám a pokuste se vyladit od tohoto směrného plánu.

Další informace o optimalizaci konfigurace s rozsahem databáze a syntaxi T-SQL při změně konfigurace najdete v tématu [Změna konfigurace v oboru databáze (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Pomalý klient

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje stav, ve kterém klient používající databázi nemůže spotřebovat výstup z databáze tak rychle, jak databáze odesílá výsledky. Vzhledem k tomu, že databáze neukládá výsledky provedených dotazů do vyrovnávací paměti, zpomaluje a počká, než klient zpracuje odeslané výstupy dotazů, než bude pokračovat. Tato podmínka také může souviset se sítí, která není dostatečně rychlá, aby mohla přenášet výstupy z databáze na náročného klienta.

Tato podmínka je vygenerována pouze v případě, že je v porovnání se zjištěným chováním pracovního vytížení databáze zjištěna výkonnost regrese. Tento problém s výkonem se zjistí jenom v případě, že se v porovnání s předchozím chováním výkonu vyskytne statisticky významné snížení výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Tento zjistitelný vzor výkonu indikuje stav na straně klienta. Řešení potíží je vyžadováno v aplikaci na straně klienta nebo v síti na straně klienta. Diagnostický protokol vypíše hodnoty hash dotazů a časy čekání, které zdánlivě čekají na jejich využití v posledních dvou hodinách. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat výkon aplikace, abyste mohli tyto dotazy spotřebovat. Můžete také zvážit možné problémy s latencí sítě. Vzhledem k tomu, že problém snížení výkonu byl založen na změně v posledních sedmi denních základech, můžete prozkoumat, zda změny stavu aplikace nebo sítě způsobily tuto událost regrese výkonu.

## <a name="pricing-tier-downgrade"></a>Downgrade cenové úrovně

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu indikuje stav, ve kterém byla cenová úroveň vašeho odběru databáze downgradovaná. Vzhledem k tomu, že se snížila dostupnost prostředků (DTU) pro databázi, systém zjistil, že došlo k poklesu aktuálního výkonu databáze v porovnání s minulými sedmi dny.

Kromě toho by mohlo dojít k situaci, kdy byla cenová úroveň vašeho odběru databáze v krátkém časovém limitu downgradovaná a pak upgradována na vyšší úroveň. Detekce tohoto dočasného snížení výkonu je předvedená v části podrobností diagnostického protokolu jako downgrade a upgrade cenové úrovně.

### <a name="troubleshooting"></a>Řešení potíží

Pokud jste snížili vaši cenovou úroveň, a proto DTU k dispozici a jste spokojeni s výkonem, je potřeba nic dělat. Pokud jste snížili svou cenovou úroveň a nejste spokojeni s výkonem databáze, snižte zatížení vaší databáze nebo zvažte zvýšení cenové úrovně na vyšší úroveň.

## <a name="recommended-troubleshooting-flow"></a>Doporučený postup řešení potíží

 Pokud chcete řešit problémy s výkonem pomocí Intelligent Insights, postupujte podle vývojového diagramu.

Přejděte na Azure SQL Analytics, abyste měli přístup Intelligent Insights přes Azure Portal. Pokuste se najít výstrahu příchozího výkonu a vyberte ji. Určete, co se děje na stránce detekce. Sledujte poskytnutou analýzu původní příčiny problému, text dotazu, trendy času dotazů a vývoj incidentů. Pokuste se problém vyřešit pomocí Intelligent Insights doporučení pro zmírnění potíží s výkonem.

[![Řešení potíží s tokem grafu](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Vyberte vývojový diagram, ve kterém chcete stáhnout verzi PDF.

Intelligent Insights obvykle potřebuje jednu hodinu, než se provede analýza hlavní příčiny problému s výkonem. Pokud nemůžete najít problém v Intelligent Insights a je pro vás velmi důležité, použijte úložiště dotazů k ruční identifikaci hlavní příčiny potíží s výkonem. (Tyto problémy jsou obvykle méně než jedna hodina stará.) Další informace najdete v tématu [monitorování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Další kroky

- Naučte se [Intelligent Insights](intelligent-insights-overview.md) koncepty.
- Použijte [protokol Intelligent Insightsho diagnostiky výkonu](intelligent-insights-use-diagnostics-log.md).
- Monitorování pomocí [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Naučte se [shromažďovat a využívat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md).
---
title: Řešení potíží s výkonem s využitím služby Intelligent Insights
description: Inteligentní přehledy vám pomohou vyřešit problémy s výkonem azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 739bba7ed9ab4770a762c08fccc422ce048ae11d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214096"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Řešení potíží s výkonem azure SQL databáze pomocí inteligentních přehledů

Tato stránka obsahuje informace o problémech s výkonem azure SQL database a spravované instance zjištěných prostřednictvím protokolu prostředků [Inteligentní přehledy.](sql-database-intelligent-insights.md) Metriky a protokoly prostředků lze streamovat do [protokolů Azure Monitor ,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)nebo řešení jiného výrobce pro vlastní funkce upozornění a vytváření sestav DevOps.

> [!NOTE]
> Průvodce rychlým řešením potíží s výkonem databáze SQL pomocí inteligentních přehledů naleznete v doporučeném vývojovém [diagramu pro řešení potíží](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) v tomto dokumentu.

## <a name="detectable-database-performance-patterns"></a>Zjistitelné vzory výkonu databáze

Inteligentní přehledy automaticky zjišťuje problémy s výkonem databází v Azure SQL Database na základě čekacích dob, chyb nebo časových limitů. Výstupy Inteligentní přehledy zjistily vzorce výkonu v protokolu prostředků databáze SQL. Zjistitelné vzory výkonu jsou shrnuty v následující tabulce.

| Rozpoznatelné vzory výkonu | Popis azure sql databáze a elastických fondů | Popis databází ve spravované instanci |
| :------------------- | ------------------- | ------------------- |
| [Dosažení limitů prostředků](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Spotřeba dostupných prostředků (DTU), podprocesů pracovních procesů databáze nebo návštěvy přihlášení databáze, které jsou k dispozici na monitorovaném předplatném, dosáhla limitů. To má vliv na výkon databáze SQL. | Spotřeba prostředků procesoru dosahuje limitů spravované instance. To má vliv na výkon databáze. |
| [Zvýšení pracovní zátěže](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Bylo zjištěno zvýšení pracovního vytížení nebo nepřetržitá akumulace zatížení v databázi. To má vliv na výkon databáze SQL. | Bylo zjištěno zvýšení pracovního vytížení. To má vliv na výkon databáze. |
| [Tlak paměti](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracovníci, kteří požadovali granty paměti, musí čekat na přidělení paměti pro statisticky významné množství času nebo větší akumulaci pracovníků, kteří požadovali granty paměti. To má vliv na výkon databáze SQL. | Pracovníci, kteří požádali o přidělení paměti, čekají na přidělení paměti po statisticky významné množství času. To má vliv na výkon databáze. |
| [Zamykání](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Bylo zjištěno nadměrné zamykání databáze, které ovlivňuje výkon databáze SQL. | Bylo zjištěno nadměrné zamykání databáze ovlivňující výkon databáze. |
| [Zvýšená MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maximální stupeň paralelismu možnost (MAXDOP) se změnil ovlivňuje efektivitu provádění dotazu. To má vliv na výkon databáze SQL. | Maximální stupeň paralelismu možnost (MAXDOP) se změnil ovlivňuje efektivitu provádění dotazu. To má vliv na výkon databáze. |
| [Pagelatch tvrzení](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Více vláken se současně pokouší o přístup ke stejným stránkám vyrovnávací paměti v paměti, což má za následek prodloužení čekací doby a vyvolání tvrzení pagelatch. To ovlivňuje sql databáze výkonu. | Více vláken se současně pokouší o přístup ke stejným stránkám vyrovnávací paměti v paměti, což má za následek prodloužení čekací doby a vyvolání tvrzení pagelatch. To ovlivňuje výkon databáze. |
| [Chybějící index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Byl zjištěn chybějící index ovlivňující výkon databáze SQL. | Byl zjištěn chybějící index ovlivňující výkon databáze. |
| [Nový dotaz](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Byl zjištěn nový dotaz ovlivňující celkový výkon databáze SQL. | Byl zjištěn nový dotaz ovlivňující celkový výkon databáze. |
| [Zvýšená statistika čekání](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Byly zjištěny delší čekací doby databáze ovlivňující výkon databáze SQL. | Byly zjištěny delší čekací doby databáze ovlivňující výkon databáze. |
| [Tvrzení TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Více vláken se pokoušíte získat přístup ke stejnému prostředku TempDB, což způsobuje kritické místo. To má vliv na výkon databáze SQL. | Více vláken se pokoušíte získat přístup ke stejnému prostředku TempDB, což způsobuje kritické místo. To má vliv na výkon databáze. |
| [Nedostatek elastického bazénu DTU](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Nedostatek dostupných eDTU v elastickém fondu ovlivňuje výkon databáze SQL. | Není k dispozici pro spravovanou instanci, protože používá model virtuálních jader. |
| [Regrese plánu](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Byl zjištěn nový plán nebo změna pracovního vytížení existujícího plánu. To má vliv na výkon databáze SQL. | Byl zjištěn nový plán nebo změna pracovního vytížení existujícího plánu. To má vliv na výkon databáze. |
| [Změna hodnoty konfigurace s rozsahem databáze](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Byla zjištěna změna konfigurace v databázi SQL, která ovlivnila výkon databáze. | Byla zjištěna změna konfigurace v databázi, která ovlivnila výkon databáze. |
| [Pomalý klient](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Pomalý klient aplikace nemůže dostatečně rychle spotřebovat výstup z databáze. To má vliv na výkon databáze SQL. | Pomalý klient aplikace nemůže dostatečně rychle spotřebovat výstup z databáze. To má vliv na výkon databáze. |
| [Snížení úrovně ocenění](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Akce snížení úrovně ocenění snížila dostupné prostředky. To má vliv na výkon databáze SQL. | Akce snížení úrovně ocenění snížila dostupné prostředky. To má vliv na výkon databáze. |

> [!TIP]
> Chcete-li provádět průběžnou optimalizaci výkonu databáze SQL, povolte [automatické ladění azure sql database](sql-database-automatic-tuning.md). Tato jedinečná funkce integrované inteligence databáze SQL nepřetržitě monitoruje databázi SQL, automaticky ladí indexy a aplikuje opravy plánu spuštění dotazu.
>

Následující část popisuje zjistitelné vzory výkonu podrobněji.

## <a name="reaching-resource-limits"></a>Dosažení limitů prostředků

### <a name="what-is-happening"></a>Co se děje

Tento rozpoznatelný vzor výkonu kombinuje problémy s výkonem, které souvisejí s dosažením dostupných omezení prostředků, omezení pracovních prostředků a omezení relací. Po zjištění tohoto problému s výkonem označuje pole popisu protokolu diagnostiky, zda problém s výkonem souvisí s omezeními prostředků, pracovníka nebo relace.

Prostředky v databázi SQL jsou obvykle označovány na [prostředky DTU](sql-database-what-is-a-dtu.md) nebo [virtuálních jader.](sql-database-service-tiers-vcore.md) Vzor dosažení omezení prostředků je rozpoznán, když zjištěné snížení výkonu dotazu je způsobeno dosažením některého z naměřených omezení prostředků.

Omezení relace označuje počet dostupných souběžných přihlášení do databáze SQL. Tento vzor výkonu je rozpoznán, když aplikace, které jsou připojeny k databázím SQL dosáhly počtu dostupných souběžných přihlášení do databáze. Pokud se aplikace pokusí použít více relací, než jsou k dispozici v databázi, výkon dotazu je ovlivněna.

Dosažení omezení pracovních prostředků je specifický případ dosažení omezení prostředků, protože dostupní pracovníci se nepočítají v využití DTU nebo virtuálních jader. Dosažení omezení pracovního procesu v databázi může způsobit nárůst čekacích dob specifických pro prostředky, což vede ke snížení výkonu dotazu.

### <a name="troubleshooting"></a>Řešení potíží

Výstupy protokolu diagnostiky dotazu na dotazy, které ovlivnily procento výkonu a spotřeby prostředků. Tyto informace můžete použít jako výchozí bod pro optimalizaci zatížení databáze. Zejména můžete optimalizovat dotazy, které ovlivňují snížení výkonu přidáním indexů. Nebo můžete optimalizovat aplikace s rovnoměrnější rozložení pracovního vytížení. Pokud se vám nedaří snížit zatížení nebo provést optimalizace, zvažte zvýšení cenové úrovně předplatného databáze SQL, abyste zvýšili množství dostupných prostředků.

Pokud jste dosáhli dostupných omezení relace, můžete optimalizovat aplikace snížením počtu přihlášení do databáze. Pokud se vám nedaří snížit počet přihlášení z vašich aplikací do databáze, zvažte zvýšení cenové úrovně databáze. Nebo můžete rozdělit a přesunout databázi do více databází pro vyváženější rozložení pracovního vytížení.

Další návrhy na řešení omezení relace naleznete v tématu [Jak se vypořádat s omezeními maximální přihlášení databáze SQL](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Informace o omezeních na úrovni serveru a předplatného najdete v [tématu Přehled omezení prostředků na serveru SQL Database.](sql-database-resource-limits-database-server.md)

## <a name="workload-increase"></a>Zvýšení pracovního vytížení

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu identifikuje problémy způsobené zvýšení pracovního vytížení nebo ve své závažnější podobě, zatížení hromadu.

Tato detekce se provádí prostřednictvím kombinace několika metrik. Základní měřená metrika detekuje zvýšení pracovního vytížení ve srovnání s minulým směrným plánem pracovního vytížení. Druhá forma detekce je založena na měření velkého nárůstu aktivních pracovních podprocesů, která je dostatečně velká, aby ovlivnila výkon dotazu.

Ve své závažnější podobě může pracovní vytížení neustále hromadit z důvodu neschopnosti databáze SQL pro zpracování úlohy. Výsledkem je neustále rostoucí velikost pracovního vytížení, což je podmínka nahromadění pracovního vytížení. Z důvodu této podmínky se zvětšuje doba, po kterou pracovní vytížení čeká na spuštění. Tato podmínka představuje jeden z nejzávažnějších problémů s výkonem databáze. Tento problém je zjištěn a to prostřednictvím sledování zvýšení počtu přerušených pracovních podprocesů.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy počet dotazů, jejichž spuštění se zvýšila a hash dotazu dotazu s největším příspěvkem ke zvýšení pracovního vytížení. Tyto informace můžete použít jako výchozí bod pro optimalizaci pracovního vytížení. Dotaz označený jako největší přispěvatel ke zvýšení pracovního vytížení je obzvláště užitečný jako výchozí bod.

Můžete zvážit distribuci úloh rovnoměrněji do databáze. Zvažte optimalizaci dotazu, který ovlivňuje výkon přidáním indexů. Můžete také distribuovat úlohy mezi více databází. Pokud tato řešení nejsou možné, zvažte zvýšení cenové úrovně předplatného databáze SQL zvýšit množství dostupných prostředků.

## <a name="memory-pressure"></a>Zatížení paměti

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení výkonu aktuální databáze způsobené tlakpaměti nebo v jeho závažnější podobě stav hromadit paměti, ve srovnání s posledních sedm dní směrného plánu výkonu.

Tlak v paměti označuje podmínku výkonu, ve kterém je velký počet pracovních podprocesů požadujících paměťové granty v databázi SQL. Velký svazek způsobí, že vysoké využití paměti podmínku, ve kterém databáze SQL není schopen efektivně přidělit paměť všem pracovníkům, kteří o ni požádat. Jedním z nejčastějších důvodů tohoto problému souvisí s množstvím paměti, které jsou k dispozici pro databázi SQL na jedné straně. Na druhou stranu zvýšení pracovního vytížení způsobuje zvýšení pracovních vláken a tlak paměti.

Těžší forma tlaku paměti je paměť pile-up stavu. Tato podmínka označuje, že vyšší počet pracovních podprocesů požadují přidělení paměti, než jsou dotazy uvolnění paměti. Tento počet pracovních podprocesů požadujících přidělení paměti také může být neustále zvyšuje (hromadí) protože databázový stroj SQL není schopen přidělit paměť dostatečně efektivně, aby splňovaly požadavky. Podmínka hromadit paměti představuje jeden z nejzávažnějších problémů s výkonem databáze.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy podrobnosti úložiště objektu paměti s úředníkem (to znamená pracovní vlákno) označeny jako nejvyšší důvod pro vysoké využití paměti a příslušná časová razítka. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat nebo odebrat dotazy týkající se úředníci s nejvyšším využitípaměti. Můžete se také ujistit, že nedotazujete data, která nechcete použít. Osvědčeným postupem je vždy používat klauzuli WHERE ve vašich dotazech. Kromě toho doporučujeme vytvořit indexy bez clusterů k hledání dat, nikoli k jejich prohledání.

Můžete také snížit zatížení optimalizací nebo distribucí přes více databází. Nebo můžete distribuovat úlohy mezi více databází. Pokud tato řešení nejsou možné, zvažte zvýšení cenové úrovně předplatného databáze SQL zvýšit množství paměťových prostředků, které jsou k dispozici pro databázi.

Další návrhy řešení potíží naleznete v [tématu Memory granty meditace: tajemný SQL Server paměti spotřebitele s mnoha jmény](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Uzamčení

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení výkonu aktuální databáze, ve kterém je zjištěno nadměrné uzamčení databáze ve srovnání s poslední sedmidenní výkon směrného plánu.

V moderních RDBMS uzamčení je nezbytné pro implementaci vícevláknových systémů, ve kterých je maximální výkon spuštěním více souběžných pracovníků a paralelní databázové transakce, kde je to možné. Uzamčení v tomto kontextu odkazuje na předdefinovaný mechanismus přístupu, ve kterém pouze jedna transakce může výhradně přistupovat k řádkům, stránkám, tabulkám a souborům, které jsou požadovány a nesoutěží s jinou transakcí pro prostředky. Když je transakce, která uzamkla prostředky pro použití, je uvolněnzámek těchto prostředků, který umožňuje další transakce pro přístup k požadovaným prostředkům. Další informace o uzamčení naleznete [v tématu Lock v databázovém stroji](https://msdn.microsoft.com/library/ms190615.aspx).

Pokud transakce prováděné sql engine čekají na delší dobu pro přístup k prostředkům uzamčen pro použití, tato čekací doba způsobí zpomalení výkonu provádění úlohy.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolu výstupy uzamčení podrobnosti, které můžete použít jako základ pro řešení potíží. Můžete analyzovat hlášené blokování dotazů, to znamená dotazy, které zavádějí snížení výkonu uzamčení a jejich odebrání. V některých případech může být úspěšná při optimalizaci blokování dotazů.

Nejjednodušší a nejbezpečnější způsob, jak zmírnit problém je udržet transakce krátké a snížit stopu zámku nejdražší dotazy. Můžete rozdělit velkou dávku operací do menších operací. Dobrým postupem je snížit stopu zámku dotazu tím, že dotaz co nejúčinnější. Snížit velké prohledává, protože zvyšují pravděpodobnost zablokování a nepříznivě ovlivnit celkový výkon databáze. Pro identifikované dotazy, které způsobují uzamčení, můžete vytvořit nové indexy nebo přidat sloupce do existujícího indexu, abyste se vyhnuli prohledávacím tabulka.

Další návrhy naleznete v tématu [Jak vyřešit problémy s blokováním, které jsou způsobeny eskalací zámků na serveru SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zvýšená MAXDOP

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje podmínku, ve které byl zvolený plán spuštění dotazu paralelizován více, než měl být. Optimalizace dotazů SQL Database může zvýšit výkon pracovního vytížení prováděním dotazů paralelně, aby se věci urychlily, kde je to možné. V některých případech paralelní pracovníci zpracování dotazu tráví více času čekání na sebe synchronizovat a sloučit výsledky ve srovnání s prováděním stejného dotazu s menším počtem paralelních pracovníků, nebo dokonce v některých případech ve srovnání s jedním pracovním vláknem.

Expertní systém analyzuje aktuální výkon databáze ve srovnání s výchozím obdobím. Určuje, zda dříve spuštěný dotaz běží pomaleji než dříve, protože plán spuštění dotazu je paraleličtější, než by měl být.

Možnost konfigurace serveru MAXDOP v databázi SQL se používá k řízení, kolik jader procesoru lze použít ke spuštění stejného dotazu paralelně.

### <a name="troubleshooting"></a>Řešení potíží

Výstupy protokolu diagnostiky dotazu hashe související s dotazy, pro které se zvýšila doba provádění, protože byly paralelizovány více, než by měly být. Protokol také výstupy CXP čekací doby. Tento čas představuje čas jednoho organizátora nebo koordinátora podprocesu (vlákno 0) čeká na dokončení všech ostatních vláken před sloučením výsledků a posunu vpřed. Kromě toho protokol diagnostiky výstupy čekací doby, které nevýkonné dotazy čekaly v provádění celkově. Tyto informace můžete použít jako základ pro řešení potíží.

Nejprve optimalizujte nebo zjednodušte složité dotazy. Dobrým postupem je rozdělit dlouhé dávkové úlohy na menší. Kromě toho se ujistěte, že jste vytvořili indexy pro podporu dotazů. Můžete také ručně vynutit maximální stupeň paralelismu (MAXDOP) pro dotaz, který byl označen jako nefunkční. Chcete-li tuto operaci nakonfigurovat pomocí t-SQL, přečtěte si téma [Konfigurace možnosti konfigurace serveru MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Nastavení možnosti konfigurace serveru MAXDOP na nulu (0) jako výchozí hodnoty znamená, že databáze SQL může používat všechna dostupná logická jádra procesoru k paralelizaci vláken pro spuštění jednoho dotazu. Nastavení MAXDOP na jeden (1) označuje, že pouze jedno jádro lze použít pro spuštění jednoho dotazu. V praxi to znamená, že paralelismus je vypnutý. V závislosti na případ-za-case základ, dostupné jádra do databáze a informace protokolu diagnostiky, můžete optimalizovat maxdop možnost počet jader používaných pro paralelní spuštění dotazu, které by mohly vyřešit problém ve vašem případě.

## <a name="pagelatch-contention"></a>Tvrzení pagelatchu

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení výkonu aktuální úlohy databáze z důvodu pagelatch tvrzení ve srovnání s posledních sedm dní pracovního plánu směrného plánu.

Západky jsou zjednodušené synchronizační mechanismy používané sql database povolit multithreading. Zaručují konzistenci struktur v paměti, které zahrnují indexy, datové stránky a další vnitřní struktury.

Existuje mnoho typů zámků k dispozici v databázi SQL. Pro účely jednoduchosti zámky vyrovnávací paměti se používají k ochraně stránek v paměti ve fondu vyrovnávací paměti. Zámky vi slouží k ochraně stránek, které ještě nejsou načteny do fondu vyrovnávací paměti. Vždy, když jsou data zapsána nebo přečtena ze stránky ve fondu vyrovnávací paměti, pracovní vlákno musí nejprve získat západku vyrovnávací paměti pro stránku. Vždy, když se pracovní vlákno pokusí o přístup ke stránce, která ještě není k dispozici ve fondu vyrovnávací paměti v paměti, je proveden požadavek vi pro načtení požadovaných informací z úložiště. Tato posloupnost událostí označuje závažnější formu snížení výkonu.

Konflikty na stránce západky dochází při více vláken současně pokusí získat zámky na stejné struktury v paměti, která zavádí delší čekací doba na spuštění dotazu. V případě pagelatch vi vi kolize, když data je třeba přistupovat z úložiště, tato čekací doba je ještě větší. Může výrazně ovlivnit výkon pracovního vytížení. Pagelatch tvrzení je nejběžnější scénář podprocesů čeká na sebe a soutěží o prostředky na více systémů PROCESORU.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy pagelatch podrobnosti tvrzení. Tyto informace můžete použít jako základ pro řešení potíží.

Vzhledem k tomu, že pagelatch je mechanismus vnitřní kontroly databáze SQL, automaticky určuje, kdy je použít. Rozhodnutí o aplikaci, včetně návrhu schématu, může ovlivnit chování pagelatch z důvodu deterministické chování západek.

Jednou z metod pro zpracování tvrzení západka je nahradit sekvenční klíč indexu s nesekvenční klíč rovnoměrně distribuovat vloží v rozsahu indexu. Úvodní sloupec v indexu obvykle distribuuje zatížení proporcionálně. Další metodou, která je třeba zvážit, je dělení tabulky. Vytvoření schématu dělení hash s vypočítaným sloupcem v dělené tabulce je běžný mačkání konfliktů s nadměrnou západkou. V případě pagelatch vi vi kolize, zavedení indexy pomáhá zmírnit tento problém výkonu.

Další informace naleznete v [tématu Diagnostika a řešení tvrzení o západce na serveru SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (stažení PDF).

## <a name="missing-index"></a>Chybějící index

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje snížení výkonu aktuální úlohy databáze ve srovnání s posledním sedmidenním směrným plánem z důvodu chybějícího indexu.

Index se používá k urychlení výkonu dotazů. Poskytuje rychlý přístup k datům tabulky snížením počtu stránek datové sady, které je třeba navštívit nebo naskenovat.

Konkrétní dotazy, které způsobily snížení výkonu jsou identifikovány prostřednictvím této detekce, pro které vytváření indexů by bylo prospěšné pro výkon.

### <a name="troubleshooting"></a>Řešení potíží

Výstupy protokolu diagnostiky dotazu hashe pro dotazy, které byly identifikovány ovlivnit výkon pracovního vytížení. Můžete vytvořit indexy pro tyto dotazy. Tyto dotazy můžete také optimalizovat nebo odebrat, pokud nejsou vyžadovány. Dobrým postupem výkonu je vyhnout se dotazování na data, která nepoužíváte.

> [!TIP]
> Věděli jste, že integrovaná inteligence sql database může automaticky spravovat nejvýkonnější indexy pro vaše databáze?
>
> Pro průběžnou optimalizaci výkonu databáze SQL doporučujeme povolit [automatické ladění databáze SQL Database](sql-database-automatic-tuning.md). Tato jedinečná funkce integrované inteligence sql database nepřetržitě monitoruje vaši databázi SQL a automaticky ladí a vytváří indexy pro vaše databáze.
>

## <a name="new-query"></a>Nový dotaz

### <a name="what-is-happening"></a>Co se děje

Tento vzor výkonu označuje, že je zjištěn nový dotaz, který pracuje špatně a ovlivňuje výkon pracovního vytížení ve srovnání se sedmidenním směrným plánem výkonu.

Psaní dobře fungující dotaz někdy může být náročný úkol. Další informace o psaní dotazů naleznete v [tématu Zápis dotazů SQL](https://msdn.microsoft.com/library/bb264565.aspx). Informace o optimalizaci výkonu existujících dotazů naleznete v [tématu Optimalizace dotazů](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy informací až dva nové dotazy nejvíce náročné na procesor, včetně jejich dotazů hashe. Vzhledem k tomu, že zjištěný dotaz ovlivňuje výkon pracovního vytížení, můžete optimalizovat dotaz. Osvědčeným postupem je načíst pouze data, která potřebujete použít. Doporučujeme také používat dotazy s klauzulí WHERE. Doporučujeme také zjednodušit složité dotazy a rozdělit je do menších dotazů. Dalším osvědčeným postupem je rozdělit velké dávkové dotazy do menších dávkových dotazů. Zavedení indexů pro nové dotazy je obvykle vhodné zmírnit tento problém s výkonem.

Zvažte použití [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Zvýšená statistika čekání

### <a name="what-is-happening"></a>Co se děje

Tento rozpoznatelný vzor výkonu označuje snížení výkonu pracovního vytížení, ve kterém jsou identifikovány dotazy s nedostatečným výkonem ve srovnání s posledním sedmidenním směrným plánem pracovního vytížení.

V tomto případě systém nelze klasifikovat dotazy s nedostatečným výkonem v jiných kategoriích standardní zjistitelné výkonu, ale zjistil statistiku čekání odpovědné za regrese. Proto je považuje za dotazy se *zvýšenými statistikami čekání*, kde je také vystavena statistika čekání zodpovědná za regresi.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy informace o zvýšené čekací doby podrobnosti a dotazy hash y ovlivněných dotazů.

Vzhledem k tomu, že systém nemohl úspěšně identifikovat hlavní příčinu dotazů s nedostatečným výkonem, jsou informace o diagnostice dobrým výchozím bodem pro ruční řešení potíží. Můžete optimalizovat výkon těchto dotazů. Dobrým postupem je načíst pouze data, která potřebujete použít, a zjednodušit a rozdělit složité dotazy na menší.

Další informace o optimalizaci výkonu dotazů naleznete v [tématu Optimalizace dotazů](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Tvrzení TempDB

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje podmínku výkonu databáze, ve kterém existuje kritické místo podprocesů, které se pokoušejí o přístup k prostředkům tempDB. (Tato podmínka není související s iO.) Typický scénář pro tento problém s výkonem jsou stovky souběžných dotazů, které všechny vytvořit, použít a potom přetáhnout malé tabulky tempDB. Systém zjistil, že počet souběžných dotazů používajících stejné tabulky tempDB se zvýšil s dostatečnou statistickou významností, aby ovlivnil výkon databáze ve srovnání s uplynulým sedmidenním směrným plánem výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Diagnostika protokolu výstupy podrobnosti konflikty tempDB. Informace můžete použít jako výchozí bod pro řešení potíží. Existují dvě věci, které můžete sledovat ke zmírnění tohoto druhu kolize a zvýšit propustnost celkové zatížení: Můžete přestat používat dočasné tabulky. Můžete také použít tabulky optimalizované pro paměť.

Další informace naleznete [v tématu Úvod do tabulek optimalizovaných pro paměť](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Nedostatek elastického bazénu DTU

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje snížení výkonu aktuálního zatížení databáze ve srovnání s posledním sedmidenním směrným plánem. Je to kvůli nedostatku dostupných dtu v elastickém fondu vašeho předplatného.

Prostředky v databázi SQL jsou obvykle označovány jako [prostředky DTU](sql-database-purchase-models.md#dtu-based-purchasing-model), které se skládají z kombinované míry prostředků procesoru a vi (vi protokolu dat a transakcí). [Prostředky elastického fondu Azure](sql-database-elastic-pool.md) se používají jako fond dostupných prostředků eDTU sdílených mezi více databázemi pro účely škálování. Pokud jsou k dispozici prostředky eDTU ve vašem elastickém fondu nejsou dostatečně velké pro podporu všech databází ve fondu, je zjištěn problém s nedostatkem výkonu elastického fondu DTU systémem.

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky výstupy informace o elastickém fondu, uvádí horní Databáze náročné DTU a poskytuje procento fondu DTU používá databáze s nejvyšší spotřebou.

Vzhledem k tomu, že tato podmínka výkonu souvisí s více databázemi pomocí stejného fondu eDTU v elastickém fondu, kroky řešení potíží se zaměřují na horní databáze náročné na DTU. Můžete snížit zatížení v databázích s nejvyšší spotřebou, což zahrnuje optimalizaci dotazů s nejvyšší spotřebou na těchto databázích. Můžete také zajistit, že nedotazujete na data, která nepoužíváte. Dalším přístupem je optimalizace aplikací pomocí nejvyšších databází náročných na DTU a redistribuce úlohy mezi více databází.

Pokud snížení a optimalizace aktuální úlohy na horní databáze náročné DTU nejsou možné, zvažte zvýšení cenové úrovně elastického fondu. Toto zvýšení má za následek zvýšení dostupných DTU v elastickém fondu.

## <a name="plan-regression"></a>Regrese plánu

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje podmínku, ve které databáze SQL využívá neoptimální plán spuštění dotazu. Neoptimální plán obvykle způsobí zvýšené spuštění dotazu, což vede k delší čekací doby pro aktuální a další dotazy.

Databáze SQL určuje plán spuštění dotazu s nejnižšími náklady na spuštění dotazu. Jako typ dotazů a úloh změnit, někdy existující plány již nejsou efektivní, nebo snad SQL Database neprovede dobré posouzení. Jako věc opravy plány spuštění dotazu lze ručně vynuceno.

Tento rozpoznatelný vzor výkonu kombinuje tři různé případy regrese plánu: regrese nového plánu, regrese starého plánu a existující plány změněné zatížení. Konkrétní typ regrese plánu, ke kterému došlo, je k dispozici ve *vlastnosti details* v protokolu diagnostiky.

Podmínka regrese nového plánu odkazuje na stav, ve kterém SQL Database spustí nový plán spuštění dotazu, který není tak efektivní jako starý plán. Podmínka regrese starého plánu odkazuje na stav, kdy databáze SQL přepne z použití nového, efektivnějšího plánu na starý plán, který není tak efektivní jako nový plán. Existující plány změnily regresi pracovního vytížení odkazuje na stav, ve kterém se staré a nové plány neustále střídají, přičemž zůstatek směřuje spíše k plánu s nedostatečným výkonem.

Další informace o regresi plánu naleznete v tématu [Co je regrese plánu na serveru SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/).

### <a name="troubleshooting"></a>Řešení potíží

Protokol diagnostiky vyhlásí hodnotu hash dotazu, dobré ID plánu, chybné ID plánu a ID dotazů. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete analyzovat, který plán je lepší výkon pro konkrétní dotazy, které můžete identifikovat s dotazem hashe s poskytnutými. Poté, co určíte, který plán funguje lépe pro vaše dotazy, můžete jej ručně vynutit.

Další informace naleznete v [tématu How HOW SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Věděli jste, že integrovaná inteligence sql database může automaticky spravovat plány provádění dotazů pro vaše databáze?
>
> Pro průběžnou optimalizaci výkonu databáze SQL doporučujeme povolit [automatické ladění databáze SQL Database](sql-database-automatic-tuning.md). Tato jedinečná funkce integrované inteligence databáze SQL průběžně monitoruje databázi SQL a automaticky ladí a vytváří plány provádění dotazů pro vaše databáze.
>

## <a name="database-scoped-configuration-value-change"></a>Změna hodnoty konfigurace s rozsahem databáze

### <a name="what-is-happening"></a>Co se děje

Tento zjistitelný vzor výkonu označuje podmínku, ve které změna konfigurace s rozsahem databáze způsobí regresi výkonu, která je zjištěna ve srovnání s chováním zatížení poslední sedmidenní databázové úlohy. Tento vzor označuje, že poslední změna provedené konfigurace s rozsahem databáze se nezdá být prospěšné pro výkon databáze.

Změny konfigurace s rozsahem databáze lze nastavit pro každou jednotlivou databázi. Tato konfigurace se používá případ od případu k optimalizaci individuálního výkonu databáze. Pro každou jednotlivou databázi lze nakonfigurovat následující možnosti: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES a CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Řešení potíží

Protokol protokolu diagnostiky výstupy databáze s rozsahem změny, které byly provedeny nedávno, které způsobily snížení výkonu ve srovnání s předchozí sedmidenní chování pracovního vytížení. Změny konfigurace můžete vrátit k předchozím hodnotám. Hodnotu můžete také vyladit podle hodnoty, dokud nebude dosaženo požadované úrovně výkonu. Můžete zkopírovat hodnoty konfigurace oboru databáze z podobné databáze s uspokojivým výkonem. Pokud se vám nedaří řešit výkon, vraťte se k výchozím hodnotám výchozí databáze SQL a pokuste se doladit od tohoto směrného plánu.

Další informace o optimalizaci konfigurace s rozsahem databáze a syntaxi T-SQL při změně konfigurace naleznete v tématu [Alter database-scoped configuration (Transact-SQL).](https://msdn.microsoft.com/library/mt629158.aspx)

## <a name="slow-client"></a>Pomalý klient

### <a name="what-is-happening"></a>Co se děje

Tento rozpoznatelný vzor výkonu označuje podmínku, ve které klient používající databázi SQL nemůže spotřebovat výstup z databáze tak rychle, jak databáze odešle výsledky. Vzhledem k tomu, že SQL Database není ukládání výsledků spuštěných dotazů ve vyrovnávací paměti, zpomaluje a čeká na klienta využívat výstupy přenášených dotazů před pokračováním. Tato podmínka také může souviset se sítí, která není dostatečně rychlá pro přenos výstupů z databáze SQL do náročného klienta.

Tato podmínka je generována pouze v případě, že regrese výkonu je zjištěna ve srovnání s posledních sedmidenní chování zatížení databáze. Tento problém s výkonem je zjištěn pouze v případě, že dojde ke statisticky významnému snížení výkonu ve srovnání s předchozím chováním výkonu.

### <a name="troubleshooting"></a>Řešení potíží

Tento zjistitelný vzor výkonu označuje stav na straně klienta. Řešení potíží je vyžadováno v aplikaci na straně klienta nebo v síti na straně klienta. Protokol diagnostiky výstupy dotazu hash a čekací doby, které se zdají být čekání nejvíce pro klienta využívat během posledních dvou hodin. Tyto informace můžete použít jako základ pro řešení potíží.

Můžete optimalizovat výkon aplikace pro spotřebu těchto dotazů. Můžete také zvážit možné problémy s latencí sítě. Vzhledem k tomu, že problém se snížením výkonu byl založen na změně v posledním sedmidenním směrném plánu výkonu, můžete zjistit, zda nedávné změny podmínek aplikace nebo sítě způsobily tuto událost regrese výkonu.

## <a name="pricing-tier-downgrade"></a>Snížení úrovně ocenění

### <a name="what-is-happening"></a>Co se děje

Tento rozpoznatelný vzor výkonu označuje podmínku, ve které byla snížena úroveň ocenění předplatného databáze SQL. Z důvodu snížení prostředků (DTU) k dispozici v databázi, systém zjistil pokles výkonu aktuální databáze ve srovnání s posledních sedmidenní směrný plán.

Kromě toho může existovat podmínka, ve které byla snížena úroveň ocenění předplatného databáze SQL a v krátké době upgradována na vyšší úroveň. Detekce tohoto dočasného snížení výkonu je výstupv části podrobnosti protokolu diagnostiky jako snížení úrovně ocenění a upgrade.

### <a name="troubleshooting"></a>Řešení potíží

Pokud jste snížili cenovou úroveň, a proto dtu, které jsou k dispozici pro databázi SQL, a jste spokojeni s výkonem, není nic, co musíte udělat. Pokud jste snížili cenovou úroveň a nejste spokojeni s výkonem databáze SQL, snižte databázové úlohy nebo zvažte zvýšení cenové úrovně na vyšší úroveň.

## <a name="recommended-troubleshooting-flow"></a>Doporučený tok řešení potíží

 Podle vývojového diagramu doporučujeme doporučený přístup k řešení problémů s výkonem pomocí inteligentních přehledů.

Získejte přístup k inteligentním přehledům prostřednictvím portálu Azure prostřednictvím Azure Portal prostřednictvím Azure SQL Analytics. Pokuste se vyhledat příchozí výstrahu výkonu a vyberte ji. Zjistěte, co se děje na stránce zjišťování. Dodržujte zadanou analýzu hlavní příčiny problému, textu dotazu, trendů času dotazu a vývoje incidentů. Pokuste se problém vyřešit pomocí doporučení Inteligentní přehledy pro zmírnění problému s výkonem.

[![Poradce při potížích s vývojovým diagramem](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Vyberte vývojový diagram a stáhněte si verzi PDF.

Inteligentní přehledy obvykle potřebuje jednu hodinu času k provedení analýzy hlavní příčiny problému s výkonem. Pokud nemůžete najít problém v inteligentních přehledech a je pro vás důležitý, použijte úložiště dotazů k ruční identifikaci hlavní příčiny problému s výkonem. (Obvykle jsou tyto problémy méně než jednu hodinu staré.) Další informace naleznete v [tématu Sledování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Další kroky

- Seznamte se s koncepty [inteligentních přehledů.](sql-database-intelligent-insights.md)
- Použijte [protokol diagnostiky diagnostiky výkonu databáze Azure AZURE .](sql-database-intelligent-insights-use-diagnostics-log.md)
- Monitorujte [Azure SQL Database pomocí Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Naučte se [shromažďovat a využívat data protokolu z prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).

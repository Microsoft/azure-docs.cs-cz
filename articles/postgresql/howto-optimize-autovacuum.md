---
title: Optimalizace autovacuum ve službě Azure Database pro PostgreSQL server
description: Tento článek popisuje, jak můžete optimalizovat autovacuum ve službě Azure Database pro PostgreSQL server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 0f8db7dd3a90e06587a7e0e05f33cb6fba5c72e1
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539785"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Optimalizace autovacuum v databázi Azure PostgreSQL server 
Tento článek popisuje, jak efektivně optimalizovat autovacuum na – Azure Database for PostgreSQL.

## <a name="overview-of-autovacuum"></a>Přehled autovacuum
PostgreSQL používá MVCC k umožnění vyšší souběžnosti databáze. Každá aktualizace vede insert a delete a každý odstranit výsledkem řádky se obnovitelného označená k odstranění. Označením soft způsobí identifikace dead řazených kolekcí členů, který bude vyprázdněno později. Postgresql – to vše dosahuje spuštěním vakuová úlohy.

Vakuová úlohy můžete aktivovat ručně nebo automaticky. Další dead řazených kolekcí členů bude existovat, pokud je databáze dochází k velkým aktualizace nebo odstranění operace a méně při nečinnosti.  Potřebné ke spuštění a takový častěji je větší, pokud se databáze nachází v případě velkého zatížení; vytváření, spouštění úloh Hive vakuová **ručně** nepraktické.

Dá se Autovacuum a výhody ladění. Výchozí hodnoty, které se dodává s PostgreSQL, pokuste se zjistit produktu funguje na všech typech zařízení, včetně Raspberry instrukce pro zpracování a hodnoty ideální konfiguraci závisí na několika faktorech:
- Celkový počet prostředků, které jsou k dispozici – velikost skladové položky a úložiště.
- Využití prostředků.
- Vlastnosti jednotlivého objektu.

## <a name="autovacuum-benefits"></a>Výhody Autovacuum
Čas od času při spuštění a takový, může způsobit dead řazených kolekcí členů, která shromažďují:
- Data nafouknutí - větší databáze a tabulky.
- Větší neoptimální indexy.
- Zvýšená vstupně-výstupních operací.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Monitorování vyhledávání s dotazy autovacuum
Následující ukázkový dotaz slouží k identifikaci počet nefunkční a funkční řazených kolekcí členů v tabulce s názvem "XYZ": "Vyberte relace %{relname/, n_dead_tup, n_live_tup, (n_dead_tup / n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables relace %{relname/ WHERE ="XYZ"objednávky podle n_dead_tup DESC;.

## <a name="autovacuum-configurations"></a>Konfigurace Autovacuum
Parametry konfigurace, které řídí autovacuum točí kolem dvě základní otázky:
- Kdy ji spustit?
- Kolik by ho vyčištění po jeho spuštění?

Níže jsou některé autovacuum konfigurační parametry, které můžete aktualizovat na základě výše uvedené dotazy a pokyny:
Parametr|Popis|Výchozí hodnota
---|---|---
autovacuum_vacuum_threshold|Určuje minimální počet aktualizovaných nebo odstraněných řazených kolekcí členů potřebné k aktivaci takový v jedné tabulce. Výchozí hodnota je 50 řazené kolekce členů. Tento parametr lze nastavit pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Toto nastavení lze přepsat pro jednotlivé tabulky tak, že změníte parametry tabulky úložiště.|50
autovacuum_vacuum_scale_factor|Určuje velikost tabulky se při rozhodování o tom, jestli se má spustit takový přidat do autovacuum_vacuum_threshold zlomek. Výchozí hodnota je 0.2 (20 procent velikosti tabulky). Tento parametr lze nastavit pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Toto nastavení lze přepsat pro jednotlivé tabulky tak, že změníte parametry tabulky úložiště.|5 procent
autovacuum_vacuum_cost_limit|Určuje mezní hodnoty nákladů, který se použije při automatických operacích a takový. Pokud hodnotu -1 je zadána (což je výchozí hodnota), bude použita hodnota regulární vacuum_cost_limit. Pokud existuje více než jeden pracovní proces. hodnota proporcionálně distribuovat mezi spuštěné pracovní procesy autovacuum. Součet limity pro každý zaměstnanec nepřekračuje hodnotu této proměnné. Tento parametr lze nastavit pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Toto nastavení lze přepsat pro jednotlivé tabulky tak, že změníte parametry tabulky úložiště.|-1
autovacuum_vacuum_cost_delay|Určuje hodnotu náklady na zpoždění, který se použije při automatických operacích a takový. Pokud není zadána hodnota -1, pravidelně vacuum_cost_delay hodnota se použije. Výchozí hodnota je 20 MS. Tento parametr lze nastavit pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Toto nastavení lze přepsat pro jednotlivé tabulky tak, že změníte parametry tabulky úložiště.|20 ms
autovacuum_nap_time|Určuje že minimální zpoždění mezi autovacuum běží na libovolné dané databázi. V každé funkce round proces démon zkontroluje databáze a problémy s příkazy a takový a analyzovat podle potřeby pro tabulky v této databázi. Zpoždění se měří v sekundách a výchozí hodnota je jedna minuta (1 min.). Tento parametr lze nastavit pouze v souboru postgresql.conf nebo na příkazovém řádku serveru.|15 s
autovacuum_max_workers|Určuje maximální počet procesů autovacuum (jiné než Spouštěč autovacuum), které mohou běžet v daný okamžik. Výchozí hodnota je 3. Tento parametr lze nastavit pouze při spuštění serveru.|3
Výše uvedené nastavení lze přepsat pro jednotlivé tabulky tak, že změníte parametry tabulky úložiště.  

## <a name="autovacuum-cost"></a>Autovacuum náklady
Níže jsou náklady "na" spuštění vakuová operace:
- Zámek je umístěn na data, na kterých stránek a takový spouští.
- Výpočetní a paměťové prostředky se používají při spuštění a takový.

Z toho vyplývá, že takový neměli spouštět buď příliš často nebo příliš málo, musí se jednat o adaptivním na zatížení. Doporučujeme, abyste testování všech změn parametr autovacuum kvůli nevýhody každé z nich.

## <a name="autovacuum-start-trigger"></a>Autovacuum spuštění triggeru
Autovacuum se aktivuje, když počet dead řazených kolekcí členů překročí autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples reltuples zde je konstanta.

Vyčištění z autovacuum musí držet krok s zatížení databáze, v opačném případě může místo a vyzkoušet obecné mohou zpomalit zpracování dotazů. Amortizovaných v čase, vyčistí sazbou za které takový dead řazených kolekcí členů by se měl rovnat rychlost, jakou jsou vytvářeny dead řazené kolekce členů.

Databáze se mnoho aktualizací/odstranění mají další dead řazených kolekcí členů a potřebovat více místa. Obecně platí databáze s mnoha aktualizací/odstranění je výhodné nízké hodnoty autovacuum_vacuum_scale_factor a minimální hodnoty autovacuum_vacuum_threshold, aby se zabránilo dlouhotrvající akumulací dead řazených kolekcí členů. Můžete použít vyšší hodnoty pro oba parametry s menší databázemi vzhledem k tomu je méně naléhavé potřeby a takový. Připomenutí, že časté vacuuming pochází za cenu výpočetní a paměťové prostředky.

Výchozí měřítko 20 procent funguje dobře u tabulek s nízké procentuální hodnoty dead řazených kolekcí členů, ale ne na tabulky s vysoké procento dead řazené kolekce členů. Například na 20 GB tabulku, kterou to se přeloží na 4 GB paměti dead řazených kolekcí členů a na 1 TB tabulky je 200 GB dead řazených kolekcí členů.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo na úrovni instance. V současné době tyto parametry můžete nastavit na úrovni tabulky pouze ve službě Azure Database for PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Provádí se odhad nákladů autovacuum
Je spuštěné autovacuum "nákladné" a parametrů pro řízení modul runtime vakuová operací. Následující parametry pomoct s odhadem náklady na provoz a takový:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Procesu vakuová přečte fyzické stránky a kontroluje dead řazené kolekce členů. Každé stránky ve shared_buffers je považován za mít náklady na 1 (vacuum_cost_page_hit), všechny ostatní stránky jsou považovány za mají svou cenu 20 (vacuum_cost_page_dirty), pokud existují dead řazených kolekcí členů, nebo 10 (vacuum_cost_page_miss), pokud neexistuje žádný dead řazené kolekce členů. Vakuová operace skončí autovacuum_vacuum_cost_limit překračuje procesu.  

Po dosažení limitu procesu v režimu spánku po dobu určeného parametrem autovacuum_vacuum_cost_delay před se spouští znovu. Pokud není dosaženo limitu, autovacuum začne po hodnotu zadanou pomocí parametru autovacuum_nap_time.

Stručně řečeno parametry autovacuum_vacuum_cost_delay a autovacuum_vacuum_cost_limit určit, kolik čištění dat můžou za jednotku času. Všimněte si, že výchozí hodnoty jsou moc nízká pro většinu cenové úrovně. Optimální hodnoty pro tyto parametry jsou závislé na úroveň cen a by měl být nakonfigurovaný odpovídajícím způsobem.

Parametr autovacuum_max_workers určuje maximální počet procesů autovacuum, které můžou běžet současně.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo na úrovni instance. V současné době tyto parametry můžete nastavit na úrovni tabulky pouze ve službě Azure Database for PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Optimalizace autovacuum na tabulku
Všechny výše uvedené parametry konfigurace lze nakonfigurovat jednu tabulku, například:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Je Autovacuum na synchronní zpracování tabulky. Větší procento dead řazených kolekcí členů tabulka má vyšší "cost" k autovacuum.  Rozdělení tabulky, které mají vysoký počet aktualizací/odstranění do několika tabulek pomůže autovacuum paralelní zpracování a snížení "cost" pro dokončení autovacuum v jedné tabulce. Můžete také zvýšit počet pracovních procesů paralelní autovacuum zajistit, že pracovní procesy jsou liberally naplánované.

## <a name="next-steps"></a>Další postup
Projděte si následující documenatation PostgreSQL Další informace o používání a ladění autovacuum:
 - Dokumentace k PostgreSQL - [kapitola 18, konfigurace serveru](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - Dokumentace k PostgreSQL – [kapitoly 24, rutinní úlohy údržby databáze](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)

---
title: Optimalizace autovacuum na serveru Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete optimalizovat autovacuum na serveru Azure Database for PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: e8e9991f20481deee85a6d582582335eb98e3c24
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815213"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Optimalizace autovacuum na serveru Azure Database for PostgreSQL 
Tento článek popisuje, jak efektivně optimalizovat autovacuum na serveru Azure Database for PostgreSQL.

## <a name="overview-of-autovacuum"></a>Přehled autovacuum
PostgreSQL používá řízení souběžnosti více verzí (MVCC) k umožnění vyšší souběžnosti databáze. Každá aktualizace vede insert a delete a každý odstranit výsledkem řádky se obnovitelného označená k odstranění. Konfigurace soft označení identifikuje dead řazených kolekcí členů, který bude vyprázdněno později. Provádět tyto úlohy spustí PostgreSQL vakuová úlohu.

Vakuová úlohy můžete aktivovat ručně nebo automaticky. Další dead řazených kolekcí členů existovat, pokud je databáze dojde k velkým aktualizace nebo odstranění operace. Méně dead řazených kolekcí členů existovat, pokud je databáze je v nečinnosti. Je potřeba vakuová častěji při zatížení databáze je těžká, díky spouštění úloh Hive vakuová *ručně* nepraktické.

Dá se Autovacuum a výhody ladění. Výchozí hodnoty, které se dodává s PostgreSQL došlo k pokusu o Ujistěte se, že produkt funguje na všech typech zařízení. Mezi ně patří Raspberry instrukce pro zpracování. Ideální konfiguraci hodnoty závisí:
- Celkové množství prostředků k dispozici, jako je například velikost SKU a úložiště.
- Využití prostředků.
- Vlastnosti jednotlivého objektu.

## <a name="autovacuum-benefits"></a>Výhody Autovacuum
Pokud není vakuová čas od času, může způsobit dead řazených kolekcí členů, která shromažďují:
- Data nafouknutí jako větší databáze a tabulky.
- Větší neoptimální indexy.
- Zvýšená vstupně-výstupních operací.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor vyhledávání s dotazy autovacuum
Následující ukázkový dotaz slouží k identifikaci počet nefunkční a funkční řazených kolekcí členů v tabulce s názvem XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Konfigurace Autovacuum
Parametry konfigurace, které řídí autovacuum jsou založeny na odpovědi na dvě základní otázky:
- Kdy ji spustit?
- Kolik by ho vyčištění po jeho spuštění?

Tady jsou některé autovacuum konfigurační parametry, které můžete aktualizovat v závislosti na předchozí dotazy, společně s pokyny.
Parametr|Popis|Výchozí hodnota
---|---|---
autovacuum_vacuum_threshold|Určuje minimální počet aktualizovaných nebo odstraněných řazených kolekcí členů potřebné k aktivaci vakuová operace v jedné tabulce. Výchozí hodnota je 50 řazené kolekce členů. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Přepíše nastavení pro jednotlivé tabulky, změňte parametry tabulky úložiště.|50
autovacuum_vacuum_scale_factor|Určuje velikost tabulky se při rozhodování o tom, jestli se má aktivovat vakuová operaci přidat do autovacuum_vacuum_threshold zlomek. Výchozí hodnota je 0.2, což je 20 procent společností z žebříčku velikost tabulky. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Přepíše nastavení pro jednotlivé tabulky, změňte parametry tabulky úložiště.|5 procent
autovacuum_vacuum_cost_limit|Určuje mezní hodnoty nákladů používaných pro automatické vakuová operace. Pokud je zadán -1, a to je výchozí nastavení, je použita hodnota regulární vacuum_cost_limit. Pokud existuje více než jeden pracovní proces, je hodnota proporcionálně distribuovat mezi spuštěné autovacuum pracovních procesů. Součet limity pro každý zaměstnanec nepřekračuje hodnotu této proměnné. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Přepíše nastavení pro jednotlivé tabulky, změňte parametry tabulky úložiště.|-1
autovacuum_vacuum_cost_delay|Určuje hodnotu náklady na zpoždění používaných pro automatické vakuová operace. Pokud není zadána hodnota -1, pravidelně vacuum_cost_delay hodnota se používá. Výchozí hodnota je 20 MS. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Přepíše nastavení pro jednotlivé tabulky, změňte parametry tabulky úložiště.|20 ms
autovacuum_nap_time|Určuje že minimální zpoždění mezi autovacuum běží na libovolné dané databázi. V každé funkce round proces démon zkontroluje databáze a problémy s příkazy a takový a analyzovat podle potřeby pro tabulky v této databázi. Zpoždění se měří v sekundách a výchozí hodnota je jedna minuta (1 min.). Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru.|15 s
autovacuum_max_workers|Určuje maximální počet procesů autovacuum, než Spouštěč autovacuum, který můžete spustit v daný okamžik. Výchozí hodnota je 3. Tento parametr lze nastavte pouze při spuštění serveru.|3
Přepsání nastavení pro jednotlivé tabulky, změňte parametry tabulky úložiště. 

## <a name="autovacuum-cost"></a>Autovacuum náklady
Tady jsou náklady "na" spuštění vakuová operace:

- Stránky data, takový, na kterých běží jsou zamknuté.
- Výpočetní a paměťové prostředky se používají při vakuová úloha běží.

V důsledku toho není vakuová úlohy spustit buď příliš často nebo příliš málo. Vakuová úloha potřebuje přizpůsobit je tak zatížení. Otestujte všechny změny parametr autovacuum kvůli nevýhody každé z nich.

## <a name="autovacuum-start-trigger"></a>Autovacuum spuštění triggeru
Autovacuum se aktivuje, když počet dead řazených kolekcí členů překročí autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Tady reltuples hodnotu konstanty.

Vyčištění z autovacuum musí držet krok s zatížení databáze. V opačném případě může být místo a prostředí obecné mohou zpomalit zpracování dotazů. Amortizovaných v čase, rychlost, jakou operaci vakuová vyčistí dead řazených kolekcí členů by se měl rovnat rychlost, jakou jsou vytvářeny dead řazené kolekce členů.

Databáze s mnoha aktualizace a odstranění mají další dead řazených kolekcí členů a potřebovat více místa. Obecně platí databáze s mnoha aktualizuje a odstraní z nízké hodnoty autovacuum_vacuum_scale_factor a autovacuum_vacuum_threshold výhodu. Nízké hodnoty zabraňují dlouhotrvající akumulací dead řazených kolekcí členů. Vyšší hodnoty můžete použít pro oba parametry s menší databáze, protože jsou méně naléhavé potřeby vacuuming. Časté vacuuming dodává za cenu výpočetní a paměťové prostředky.

Výchozí měřítko 20 procent funguje dobře u tabulek s nízkou procento dead řazené kolekce členů. Nebude fungovat dobře u tabulek s vysoký podíl dead řazené kolekce členů. V tabulce, 20 GB, například toto měřítko přeloží na 4 GB paměti dead řazené kolekce členů. V tabulce 1 TB je 200 GB dead řazených kolekcí členů.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo na úrovni instance. V současné době můžete nastavit tyto parametry na úrovni tabulky pouze ve službě Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Odhad nákladů na autovacuum
Spuštěné autovacuum je "nákladné" a parametrů pro řízení modul runtime vakuová operací. Následující parametry pomoct s odhadem náklady na provoz a takový:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Procesu vakuová přečte fyzické stránky a kontroluje dead řazené kolekce členů. Každé stránky ve shared_buffers se považuje za se účtuje 1 (vacuum_cost_page_hit). Všechny ostatní stránky jsou považovány za náklady na 20 (vacuum_cost_page_dirty), pokud existují dead řazených kolekcí členů, nebo 10 (vacuum_cost_page_miss), pokud neexistuje žádný dead řazené kolekce členů. Vakuová operace skončí autovacuum_vacuum_cost_limit překračuje procesu. 

Po dosažení limitu procesu v režimu spánku po dobu určenou parametrem autovacuum_vacuum_cost_delay dříve, než začne znovu. Pokud není dosaženo limitu, autovacuum začne po hodnotu zadanou pomocí parametru autovacuum_nap_time.

Stručně řečeno parametry autovacuum_vacuum_cost_delay a autovacuum_vacuum_cost_limit určit, kolik čištění dat můžou za časovou jednotku. Všimněte si, že výchozí hodnoty jsou moc nízká pro většinu cenové úrovně. Optimální hodnoty pro tyto parametry jsou závislé na úroveň cen a by měl být nakonfigurovaný odpovídajícím způsobem.

Parametr autovacuum_max_workers určuje maximální počet procesů autovacuum, které lze spustit souběžně.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo na úrovni instance. V současné době můžete nastavit tyto parametry na úrovni tabulky pouze ve službě Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimalizace autovacuum na tabulku
Můžete nakonfigurovat všechny předchozí parametry konfigurace jednu tabulku. Tady je příklad:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum je synchronního procesu na tabulku. Větší procento dead řazených kolekcí členů, která má tabulka, tím vyšší "cost" k autovacuum. Rozdělte tabulky, které mají vysoký počet aktualizace a odstranění do několika tabulek. Rozdělení tabulky umožňuje paralelní zpracování autovacuum a snížit "cost" pro dokončení autovacuum v jedné tabulce. Můžete také zvýšit počet pracovních procesů paralelní autovacuum zajistit, že jsou naplánovány liberally pracovních procesů.

## <a name="next-steps"></a>Další postup
Další informace o tom, jak používat a vyladit autovacuum, naleznete v následující dokumentaci PostgreSQL:

 - [Kapitola 18, konfigurace serveru](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Kapitola 24, rutina úlohy údržby databáze](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)

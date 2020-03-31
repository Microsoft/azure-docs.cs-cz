---
title: Optimalizace automatického vakua – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete optimalizovat automatické vakuum v databázi Azure pro PostgreSQL – jeden server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770182"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimalizace automatického vakua v databázi Azure pro PostgreSQL – jeden server
Tento článek popisuje, jak efektivně optimalizovat automatické vakuum na azure databázi pro postgresql server.

## <a name="overview-of-autovacuum"></a>Přehled automatického vakua
PostgreSQL používá multiversion concurrency control (MVCC) k povolení větší souběžnosti databáze. Každá aktualizace má za následek vložení a odstranění a každé odstranění má za následek, že řádky jsou pro odstranění jemně označeny. Měkké značení identifikuje mrtvé řazené kolekce členů, které budou později vymazány. K provedení těchto úkolů, PostgreSQL spustí vakuovou úlohu.

Vakuová úloha může být spuštěna ručně nebo automaticky. Další mrtvé řazené kolekce členů existují, když databáze dojde k velké aktualizace nebo odstranění operací. Méně mrtvých řazených kolekcí členů existují, když je databáze nečinná. Je třeba vakuum častěji, když je zatížení databáze těžké, což umožňuje spuštění vakuové *úlohy ručně* nepohodlné.

Automatické vakuum lze konfigurovat a výhody z ladění. Výchozí hodnoty, které PostgreSQL dodává s pokusit se zajistit, aby produkt funguje na všech typech zařízení. Mezi tato zařízení patří Raspberry Pis. Ideální hodnoty konfigurace závisí na:
- Celkový počet dostupných prostředků, jako je skladová položka a velikost úložiště.
- Využití prostředků.
- Charakteristiky jednotlivých objektů.

## <a name="autovacuum-benefits"></a>Výhody automatického vakua
Pokud čas od času nevysáváte, může dojít k tomu, že mrtvé řazené kolekce členů mohou mít za následek:
- Nadýmání dat, například větší databáze a tabulky.
- Větší suboptimální indexy.
- Zvýšená vstupně-výkon.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor nafouknutí s automatickými dotazy vakuum
Následující ukázkový dotaz je určen k identifikaci počtu mrtvých a živých řazených kolekcí členů v tabulce s názvem XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Konfigurace automatického vakua
Konfigurační parametry, které řídí automatické vakuum, jsou založeny na odpovědích na dvě klíčové otázky:
- Kdy by to mělo začít?
- Kolik by měl vyčistit poté, co začne?

Zde jsou některé parametry konfigurace automatického vakua, které můžete aktualizovat na základě předchozích otázek, spolu s některými pokyny.

Parametr|Popis|Výchozí hodnota
---|---|---
autovacuum_vacuum_threshold|Určuje minimální počet aktualizovaných nebo odstraněných řazených kolekcí n-ti, které jsou potřeba ke spuštění vakuové operace v libovolné tabulce. Výchozí hodnota je 50 n-tic. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|50
autovacuum_vacuum_scale_factor|Určuje zlomek velikosti tabulky, kterou chcete přidat do autovacuum_vacuum_threshold při rozhodování, zda chcete spustit operaci vakua. Výchozí hodnota je 0,2, což je 20 procent velikosti tabulky. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|5 procent
autovacuum_vacuum_cost_limit|Určuje mezní hodnotu nákladů použitou při automatickém vakuovém provozu. Pokud -1 je zadán, což je výchozí, je použita hodnota pravidelné vacuum_cost_limit. Pokud existuje více než jeden pracovník, hodnota je rozdělena proporcionálně mezi spuštěné automatické vakuové pracovníky. Součet limitů pro každého pracovníka nepřekračuje hodnotu této proměnné. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|-1
autovacuum_vacuum_cost_delay|Určuje hodnotu zpoždění nákladů použitou při automatických podsávacích operacích. Pokud je zadán -1, použije se hodnota pravidelné vacuum_cost_delay. Výchozí hodnota je 20 milisekund. Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|20 ms
autovacuum_nap_time|Určuje minimální prodlevu mezi spuštěním automatického podtlaku v libovolné databázi. V každém kole, daemon zkoumá databáze a vydává vakuové a analyzovat příkazy podle potřeby pro tabulky v této databázi. Zpoždění se měří v sekundách a výchozí hodnota je jedna minuta (1 min). Tento parametr nastavte pouze v souboru postgresql.conf nebo na příkazovém řádku serveru.|15 s
autovacuum_max_workers|Určuje maximální počet procesů automatického podtlaku, které jsou jiné než spouštěč automatického podtlaku, které lze spustit v jednom okamžiku. Výchozí hodnota je tři. Tento parametr nastavte pouze při spuštění serveru.|3

Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky. 

## <a name="autovacuum-cost"></a>Náklady na automatické vysávání
Zde jsou "náklady" na provoz vakuové operace:

- Datové stránky, na kterých je vakuové, jsou uzamčeny.
- Výpočetní prostředky a paměť se používají při spuštění podsavé úlohy.

V důsledku toho nespouštějte vakuové úlohy příliš často nebo příliš zřídka. Vakuová práce se musí přizpůsobit pracovní zátěži. Otestujte všechny změny parametrů automatického vakua z důvodu kompromisů každého z nich.

## <a name="autovacuum-start-trigger"></a>Aktivační událost automatického vakuového spuštění
Automatické vakuum se aktivuje, když počet mrtvých řazených kolekcí přes a) překročí autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Zde je reltuples konstanta.

Vyčištění z automatického vakua musí držet krok s zatížením databáze. V opačném případě může dojít k nedostatku úložiště a dochází k obecné zpomalení v dotazech. Amortizovánv průběhu času, rychlost, jakou vakuové operace čistí mrtvé řazené kolekce členů by se měla rovnat rychlosti, jakou jsou vytvořeny mrtvé řazené kolekce členů.

Databáze s mnoha aktualizacemi a odstraněními mají více mrtvých řazených kolekcí členů a potřebují více místa. Obecně platí, že databáze s mnoha aktualizacemi a odstraněními těží z nízkých hodnot autovacuum_vacuum_scale_factor a autovacuum_vacuum_threshold. Nízké hodnoty zabraňují dlouhodobé akumulaci mrtvých řazených členů. Vyšší hodnoty můžete použít pro oba parametry s menšími databázemi, protože potřeba vysávání je méně naléhavá. Časté vysávání přichází za cenu výpočetních prostředků a paměti.

Výchozí faktor měřítka 20 procent funguje dobře v tabulkách s nízkým procentem mrtvých řazených kolekcí členů. To nefunguje dobře na stolech s vysokým procentem mrtvých n-tic. Například v tabulce 20 GB se tento faktor měřítka promítá do 4 GB mrtvých řazených kolekcí členů. Na stole o velikosti 1 TB je to 200 GB mrtvých n-tich.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo instance. Dnes můžete nastavit tyto parametry na úrovni tabulky jenom v Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Odhad nákladů na automatické vakuum
Spuštění automatického vakua je "nákladné" a existují parametry pro řízení runtime vakuových operací. Následující parametry pomáhají odhadnout náklady na provoz vakua:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vakuový proces čte fyzické stránky a kontroluje mrtvé řazené kolekce členů. Každá stránka v shared_buffers je považována za stránku s náklady 1 (vacuum_cost_page_hit). Všechny ostatní stránky jsou považovány za náklady 20 (vacuum_cost_page_dirty), pokud existují mrtvé řazené kolekce členů, nebo 10 (vacuum_cost_page_miss), pokud neexistují žádné mrtvé řazené kolekce členů. Vakuový provoz se zastaví, když proces překročí autovacuum_vacuum_cost_limit. 

Po dosažení limitu proces přejde do režimu spánku po dobu určenou parametrem autovacuum_vacuum_cost_delay, než se znovu spustí. Pokud není dosaženo limitu, automatické podtlak se spustí za hodnotou určenou parametrem autovacuum_nap_time.

Stručně řečeno, autovacuum_vacuum_cost_delay a autovacuum_vacuum_cost_limit parametry řídí, kolik vyčištění dat je povoleno za jednotku času. Všimněte si, že výchozí hodnoty jsou příliš nízké pro většinu cenových úrovní. Optimální hodnoty pro tyto parametry jsou závislé na cenové úrovni a měly by být odpovídajícím způsobem nakonfigurovány.

Parametr autovacuum_max_workers určuje maximální počet procesů automatického podtlaku, které lze spustit současně.

S PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo instance. Dnes můžete nastavit tyto parametry na úrovni tabulky jenom v Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimalizace automatického vakua na tabulku
Můžete nakonfigurovat všechny předchozí parametry konfigurace pro každou tabulku. Tady je příklad:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Automatické vakuum je synchronní proces pro tabulku. Čím větší procento mrtvých řazených kolekcí členů, které má tabulka, tím vyšší je "cena" na automatické vakuum. Tabulky, které mají vysokou míru aktualizací a odstranění, můžete rozdělit do více tabulek. Rozdělení tabulek pomáhá paralelizovat automatické vakuum a snížit "náklady" k dokončení automatického vakua na jedné tabulce. Můžete také zvýšit počet paralelních automatických podtlakových pracovníků, abyste zajistili, že pracovníci jsou liberálně naplánováni.

## <a name="next-steps"></a>Další kroky
Další informace o používání a ladění automatického vakua naleznete v následující dokumentaci k PostgreSQL:

 - [Kapitola 18, Konfigurace serveru](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Kapitola 24, Rutinní úlohy údržby databáze](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)

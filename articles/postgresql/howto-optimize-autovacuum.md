---
title: Optimalizovat autovaku-Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete na Azure Database for PostgreSQL jednom serveru optimalizovat autovaku.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012557"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimalizujte autovaku na Azure Database for PostgreSQL – jeden server

Tento článek popisuje, jak efektivně optimalizovat autovaku na Azure Database for PostgreSQL serveru.

## <a name="overview-of-autovacuum"></a>Přehled autovaku

PostgreSQL využívá řízení souběžnosti více verzí (MVCC) k umožnění větší souběžnosti databáze. Každá aktualizace má za následek, že při vložení a odstranění dojde k odstranění výsledků a při každém odstranění se v řádcích označí, že se má odstranit. Měkké označení identifikuje neaktivní řazené kolekce členů, které budou později vyčištěny. Pro provedení těchto úloh PostgreSQL spustí vakuovou úlohu.

Úlohu vaku lze aktivovat ručně nebo automaticky. V případě, že se v databázi vyskytují operace aktualizovat nebo odstranit, existují další mrtvé řazené kolekce členů. Pokud je databáze nečinná, existuje méně nečinných řazených kolekcí členů. Pokud je zatížení databáze těžké, je potřeba vymezit více, pokud je zatížení databáze velmi  snadné.

Lze nakonfigurovat autovaku a využít výhod optimalizace. Výchozí hodnoty, které PostgreSQL dodává, se snaží zajistit, aby produkt fungoval na všech druzích zařízení. Mezi tato zařízení patří Malina PiS. Ideální konfigurační hodnoty závisí na:

- Celkový počet dostupných prostředků, jako je SKU a velikost úložiště.
- Využití prostředků.
- Vlastnosti jednotlivých objektů.

## <a name="autovacuum-benefits"></a>Autovaku – výhody

Pokud neurčíte čas do doby od času, může docházet k neaktivním řazeným kolekcím, které se shromažďují:

- Dispozici determinističtější dat, jako jsou například větší databáze a tabulky.
- Větší podoptimální indexy.
- Zvýšila se vstupně-výstupní operace.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitorování dispozici determinističtější pomocí autovakuických dotazů

Následující vzorový dotaz je navržený tak, aby identifikoval počet neaktivních a živých řazených kolekcí členů v tabulce s názvem XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Autovaku – konfigurace

Parametry konfigurace, které ovládají autovaku, jsou založeny na odpovědích dvou klíčových otázek:

- Kdy by měl začít?
- Kolik byste měli vyčistit po spuštění?

Tady je několik parametrů autovaku pro konfiguraci, které můžete aktualizovat na základě předchozích otázek, spolu s některými pokyny.

Parametr|Popis|Výchozí hodnota
---|---|---
autovacuum_vacuum_threshold|Určuje minimální počet aktualizovaných nebo odstraněných řazených kolekcí členů potřebných pro aktivaci vakuové operace v libovolné tabulce. Výchozí hodnota je 50 n-tice. Tento parametr nastavte pouze v souboru PostgreSQL. conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|50
autovacuum_vacuum_scale_factor|Určuje zlomek velikosti tabulky, která se má přidat do autovacuum_vacuum_threshold při rozhodování, jestli se má aktivovat vakuová operace. Výchozí hodnota je 0,2, což je 20 procent velikosti tabulky. Tento parametr nastavte pouze v souboru PostgreSQL. conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|0.2
autovacuum_vacuum_cost_limit|Určuje hodnotu limitu nákladů používané při automatickém vakuových operací. Je-li zadána hodnota-1, což je výchozí hodnota, použije se běžná hodnota vacuum_cost_limit. Pokud je k dispozici více než jeden pracovní proces, je hodnota rozdělena mezi běžící pracovní procesy autoformování. Součet omezení pro každý pracovní proces nepřekračuje hodnotu této proměnné. Tento parametr nastavte pouze v souboru PostgreSQL. conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|-1
autovacuum_vacuum_cost_delay|Určuje hodnotu zpoždění nákladů použitou při automatickém vakuových operací. Je-li zadán parametr-1, je použita běžná hodnota vacuum_cost_delay. Výchozí hodnota je 20 milisekund. Tento parametr nastavte pouze v souboru PostgreSQL. conf nebo na příkazovém řádku serveru. Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.|20 MS
autovacuum_naptime | Určuje minimální prodlevu mezi autovakuy spuštěnými na všech daných databázích. V každém zaokrouhlení démon vyhledá databázi a problémy v podtlaku a ANALYZUJe příkazy podle potřeby pro tabulky v této databázi. Zpoždění se měří v sekundách. Tento parametr nastavte pouze v souboru PostgreSQL. conf nebo na příkazovém řádku serveru.| 15 s
autovacuum_max_workers | Určuje maximální počet autovakuových procesů, které jsou jiné než spouštěč autovaku, který může běžet kdykoli. Výchozí hodnota je tři. Tento parametr nastavte pouze na serveru Start.|3

Chcete-li přepsat nastavení pro jednotlivé tabulky, změňte parametry úložiště tabulky.

## <a name="autovacuum-cost"></a>Autovaku – náklady

Zde jsou náklady na spuštění vakuové operace:

- Stránky dat, na kterých je vaku spuštěný, jsou zamčené.
- Výpočetní prostředí a paměť se používají, když je spuštěná úloha vaku.

V důsledku toho Nespouštějte úlohy s vakuy buď příliš často, nebo příliš zřídka. Úloha podtlaku musí být přizpůsobena zatížení. Otestujte všechny změny parametrů autovaku z důvodu kompromisů každého z nich.

## <a name="autovacuum-start-trigger"></a>Aktivační událost autovaku Start

Autovakua se aktivuje, když počet nedoručených řazených kolekcí členů překročí autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Zde je reltuples konstanta.

Vyčištění z autovaku musí zachovávat zatížení databáze. V opačném případě můžete vyskytnout úložiště a vyzkoušet obecné zpomalení dotazů. Frekvence, s jakou se v průběhu času vyčistí neaktivní řazené kolekce členů, musí být rovna hodnotě frekvence, s jakou jsou vytvářeny neaktivní řazené kolekce členů.

Databáze s mnoha aktualizacemi a odstraňováním mají více nedoručených kolekcí členů a potřebují více místa. Obecně platí, že databáze s mnoha aktualizacemi a odstraňuje výhody autovacuum_vacuum_scale_factor a autovacuum_vacuum_threshold. Nízké hodnoty zabraňují prodloužené akumulaci nedoručených řazených kolekcí členů. Můžete použít vyšší hodnoty pro oba parametry s menšími databázemi, protože nutnost vaku je méně naléhavá. Časté vyvakuování přináší náklady na výpočetní výkon a paměť.

Výchozí faktor škálování 20 procent funguje dobře u tabulek s nízkým procentem neaktivních řazených kolekcí členů. Nefunguje dobře na tabulkách s vysokým procentem neaktivních řazených kolekcí členů. Například v tabulce 20 GB se tento faktor škálování převede na 4 GB neaktivních řazených kolekcí členů. V tabulce o velikosti 1 TB je 200 GB nedoručených řazených kolekcí členů.

Pomocí PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo instance. V současné době můžete tyto parametry nastavit na úrovni tabulky pouze v Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Odhad nákladů na autovaku

Spouštění autovaku je "nákladné" "a existují parametry pro řízení běhu vakuových operací. Následující parametry vám pomůžou odhadnout náklady na spuštění vaku:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vakuový proces čte fyzické stránky a kontroluje nedoručené řazené kolekce členů. Každá stránka v shared_buffers se považuje za cenu 1 (vacuum_cost_page_hit). Všechny ostatní stránky se považují za ceny 20 (vacuum_cost_page_dirty), pokud existují nedoručené řazené kolekce členů nebo 10 (vacuum_cost_page_miss), pokud žádné neexistují žádné nedoručené řazené kolekce členů. Vakuová operace se zastaví, když proces překročí autovacuum_vacuum_cost_limit.

Po dosažení limitu proces přejde do režimu spánku po dobu určenou parametrem autovacuum_vacuum_cost_delay před tím, než se znovu spustí. Pokud limit není dosaženo, autovaku začne za hodnotou určenou parametrem autovacuum_nap_time.

V souhrnu parametry autovacuum_vacuum_cost_delay a autovacuum_vacuum_cost_limit určují, kolik dat se může v rámci jednotky času vyčistit. Všimněte si, že výchozí hodnoty jsou pro většinu cenové úrovně příliš nízké. Optimální hodnoty pro tyto parametry jsou závislé na cenové úrovni a měly by být nakonfigurovány odpovídajícím způsobem.

Parametr autovacuum_max_workers určuje maximální počet procesů autovaku, které mohou běžet souběžně.

Pomocí PostgreSQL můžete nastavit tyto parametry na úrovni tabulky nebo instance. V současné době můžete tyto parametry nastavit na úrovni tabulky pouze v Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimalizovat autovaku na tabulku

Můžete nakonfigurovat všechny předchozí parametry konfigurace na tabulku. Tady je příklad:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovaku je synchronní proces na jednu tabulku. Větší procento neaktivních řazených kolekcí členů v tabulce je vyšší "náklady" na autovaku. Tabulky, které mají vysokou míru aktualizace a jsou odstraněny, můžete rozdělit do několika tabulek. Rozdělené tabulky pomáhají paralelizovat autovaku a snižovat náklady, aby se na jedné tabulce dokončily autovaku. Můžete také zvýšit počet paralelně pracujících pracovních procesů, aby bylo zajištěno, že se zaměstnanci budou moci opravdu naplánovali.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat a ladit autovaku, najdete v následující dokumentaci k PostgreSQL:

- [Kapitola 18, konfigurace serveru](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Kapitola 24, rutinní úlohy údržby databáze](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)

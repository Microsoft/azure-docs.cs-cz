---
title: Uzly – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Přečtěte si o typech uzlů a tabulek ve skupině serverů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314822"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Uzly a tabulky v Azure Database for PostgreSQL – škálovatelné (Citus)

## <a name="nodes"></a>Uzly

Typ hostingu Citus () Azure Database for PostgreSQL umožňuje, aby se servery (označované jako uzly) mezi sebou vzájemně koordinovaly v architektuře "Shared Nothing". Uzly ve skupině serverů společně uchovávají více dat a využívají více jader procesoru, než by bylo možné na jednom serveru. Architektura také umožňuje škálování databáze přidáním dalších uzlů do skupiny serverů.

### <a name="coordinator-and-workers"></a>Koordinátor a pracovníci

Každá skupina serverů má uzel koordinátora a více pracovních procesů. Aplikace odesílají své dotazy do uzlu koordinátora, který je přenáší na příslušné pracovníky a shromažďuje jejich výsledky. Aplikace se nemůžou přímo připojit k pracovníkům.

Citus () umožňuje správci databáze *distribuovat* tabulky a ukládat různé řádky do různých pracovních uzlů. Distribuované tabulky jsou klíčem k výkonu Citus (Performance Scale). Selhání při distribuci tabulek ponechá je zcela v uzlu koordinátora a nemůže využít paralelní paralelismuy mezi počítači.

Pro každý dotaz v distribuovaných tabulkách ho koordinátor směruje buď na jeden pracovní uzel, nebo ho parallelizes napříč několika v závislosti na tom, jestli jsou požadovaná data umístěná na jednom nebo několika uzlech. Koordinátor rozhodne o tom, co dělat v rámci konzultací s tabulkami metadat. Tyto tabulky sledují názvy DNS a stav pracovních uzlů a distribuci dat napříč uzly.

## <a name="table-types"></a>Typy table

Existují tři typy tabulek ve skupině serverů Citus (), každou uloženou jinak na uzlech a používají se pro různé účely.

### <a name="type-1-distributed-tables"></a>Typ 1: distribuované tabulky

První typ a nejběžnější je distribuované tabulky. Jeví se jako normální tabulky pro příkazy SQL, ale jsou horizontálně rozdělené mezi pracovní uzly. To znamená, že řádky tabulky jsou uloženy v různých uzlech v části fragment tabulky s názvem horizontálních oddílů.

Citus) spouští nejen SQL, ale příkazy DDL v celém clusteru.
Změna schématu distribuované tabulky se zavede na sebe, aby se aktualizovala všechna horizontálních oddílůa tabulky napříč pracovními procesy.

#### <a name="distribution-column"></a>Sloupec distribuce

Citus () používá algoritmus horizontálního dělení k přiřazení řádků do horizontálních oddílů. Přiřazení je provedeno deterministické na základě hodnoty sloupce tabulky nazývaného distribuční sloupec. Správce clusteru musí při distribuci tabulky určit tento sloupec.
Nastavení správné volby je důležité pro výkon a funkčnost.

### <a name="type-2-reference-tables"></a>Typ 2: referenční tabulky

Referenční tabulka je typ distribuované tabulky, jejíž celý obsah se soustředí na jeden horizontálních oddílů. Horizontálních oddílů se replikuje u každého pracovního procesu. Dotazy na kterýkoliv pracovní proces mají přístup k referenčním informacím místně, bez zatížení sítě při vyžadování řádků z jiného uzlu. Referenční tabulky nemají žádný distribuční sloupec, protože nemusíte rozlišovat samostatné horizontálních oddílů na řádek.

Referenční tabulky jsou obvykle malé a slouží k ukládání dat, která jsou relevantní pro dotazy spuštěné v libovolném pracovním uzlu. Příkladem jsou výčtové hodnoty, jako jsou stavy objednávek nebo kategorie produktů.

### <a name="type-3-local-tables"></a>Typ 3: místní tabulky

Při použití Citus () je uzel koordinátora, ke kterému se připojujete, standardní databáze PostgreSQL. V koordinátoru můžete vytvořit obyčejné tabulky a zvolit, aby se horizontálních oddílů.

Dobrým kandidátem na místní tabulky budou malé administrativní tabulky, které se nepodílejí na dotazech JOIN. Příkladem je tabulka uživatelů pro přihlášení aplikace a ověřování.

## <a name="shards"></a>Horizontálních oddílů

Předchozí část popisuje, jak se distribuované tabulky ukládají jako horizontálních oddílů na pracovních uzlech. Tato část se věnuje více technickým podrobnostem.

`pg_dist_shard`Tabulka metadat koordinátora obsahuje řádek pro každý horizontálních oddílů každé distribuované tabulky v systému. Řádek odpovídá ID horizontálních oddílů s rozsahem celých čísel v prostoru hodnot hash (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Pokud uzel koordinátora chce určit, který horizontálních oddílů obsahuje řádek `github_events` , vyhodnotí hodnotu distribučního sloupce v řádku. Uzel pak ověří, který \' Rozsah horizontálních oddílů s má hodnotu hash. Rozsahy jsou definovány tak, že obrázek funkce hash je jejich nesouvislý sjednocení.

### <a name="shard-placements"></a>Horizontálních oddílů místa

Předpokládejme, že horizontálních oddílů 102027 je přidružen k danému řádku. Řádek je načten nebo napsán v tabulce s názvem `github_events_102027` v jednom z pracovních procesů. Který pracovník? To je určeno výhradně tabulkami metadat. Mapování horizontálních oddílů na pracovní proces se označuje jako umístění horizontálních oddílů.

Uzel koordinátora přepíše dotazy na fragmenty, které odkazují na konkrétní tabulky, jako `github_events_102027` a spustí tyto fragmenty na příslušných pracovních procesech. Tady je příklad dotazu spuštěného na pozadí, kde najdete uzel obsahující horizontálních oddílů ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Další kroky

- [Určení typu aplikace](concepts-hyperscale-app-type.md) pro přípravu na modelování dat

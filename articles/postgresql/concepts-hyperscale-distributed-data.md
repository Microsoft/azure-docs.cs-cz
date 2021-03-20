---
title: Distribuovaná data – Citus () – Azure Database for PostgreSQL
description: Přečtěte si o distribuovaných tabulkách, referenčních tabulkách, místních tabulkách a horizontálních oddílů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86114495"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Distribuovaná data v Azure Database for PostgreSQL – Citus (škálování)

Tento článek popisuje tři typy tabulek v Azure Database for PostgreSQL – Citus (škálování).
Ukazuje, jak jsou distribuované tabulky uložené jako horizontálních oddílů, a způsob, jakým se horizontálních oddílů umístí na uzly.

## <a name="table-types"></a>Typy table

Existují tři typy tabulek ve skupině serverů Citus (), které se používají pro různé účely.

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

- Naučte se, jak [zvolit distribuční sloupec](concepts-hyperscale-choose-distribution-column.md) pro distribuované tabulky.

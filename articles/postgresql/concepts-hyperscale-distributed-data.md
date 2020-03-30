---
title: Distribuovaná data – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Další informace o distribuovaných tabulkách, referenčních tabulkách, místních tabulkách a úlomcích v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243650"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Distribuovaná data v databázi Azure pro PostgreSQL – Hyperscale (Citus)

Tento článek popisuje tři typy tabulek v Azure Database for PostgreSQL – Hyperscale (Citus).
Ukazuje, jak distribuované tabulky jsou uloženy jako úlomky a způsob, jakým jsou umístěny šimejcí dříče na uzlech.

## <a name="table-types"></a>Typy tabulek

Existují tři typy tabulek ve skupině serverů Hyperscale (Citus), z nichž každá se používá pro jiné účely.

### <a name="type-1-distributed-tables"></a>Typ 1: Distribuované tabulky

První typ a nejběžnější jsou distribuované tabulky. Zdá se, že normální tabulky příkazy SQL, ale jsou horizontálně rozděleny napříč pracovními uzly. To znamená, že řádky tabulky jsou uloženy v různých uzlech v tabulkách fragmentů nazývaných úlomky.

Hyperscale (Citus) spouští nejen příkazy SQL, ale I DDL v celém clusteru.
Změna schématu distribuované tabulky kaskády aktualizovat všechny úlomky tabulky mezi pracovníky.

#### <a name="distribution-column"></a>Distribuční sloupec

Hyperscale (Citus) používá algoritmické horizontálního dělení přiřadit řádky horizontálních oddílů. Přiřazení je deterministicky založeno na hodnotě sloupce tabulky nazývaného distribuční sloupec. Správce clusteru musí určit tento sloupec při distribuci tabulky.
Správná volba je důležitá pro výkon a funkčnost.

### <a name="type-2-reference-tables"></a>Typ 2: Referenční tabulky

Referenční tabulka je typ distribuované tabulky, jejíž celý obsah je soustředěn do jednoho úlomku. Úlomek je replikován na každém pracovníkovi. Dotazy na všechny pracovníky přístup k referenční informace místně, bez síťové režie požadující řádky z jiného uzlu. Referenční tabulky nemají žádný distribuční sloupec, protože není nutné rozlišovat samostatné úlomky na řádek.

Referenční tabulky jsou obvykle malé a slouží k ukládání dat, která jsou relevantní pro dotazy spuštěné na libovolném pracovním uzlu. Příkladem jsou výčtové hodnoty, jako jsou stavy objednávek nebo kategorie produktů.

### <a name="type-3-local-tables"></a>Typ 3: Místní tabulky

Při použití Hyperscale (Citus), uzel koordinátora, ke kterému se připojujete, je pravidelná databáze PostgreSQL. Můžete vytvořit běžné tabulky na koordinátora a zvolit jejich roztřepnutí.

Dobrým kandidátem pro místní tabulky by byly malé tabulky pro správu, které se neúčastní dotazů na připojení. Příkladem je tabulka uživatelů pro přihlášení a ověřování aplikací.

## <a name="shards"></a>Střepy

Předchozí část popisuje, jak jsou distribuované tabulky uloženy jako šřepy na pracovních uzlech. Tato část pojednává o dalších technických podrobnostech.

Tabulka `pg_dist_shard` metadat na koordinátora obsahuje řádek pro každý oddíl každé distribuované tabulky v systému. Řádek odpovídá ID sřídké části s rozsahem celá čísla v oblasti hash (shardminvalue, shardmaxvalue).

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

Pokud uzel koordinátora chce určit, který oddíl `github_events`je obsahuje řádek , zaměňuje hodnotu distribučního sloupce v řádku. Potom uzel zkontroluje,\'který rozsah svižných pásů obsahuje hodnotu hashed. Rozsahy jsou definovány tak, že obraz funkce hash je jejich nesouvislé spojení.

### <a name="shard-placements"></a>Umístění úlomků

Předpokládejme, že oddíl 102027 je přidružen k příslušnému řádku. Řádek je přečten nebo zapsán `github_events_102027` v tabulce s názvem v jednom z pracovníků. Který pracovník? To je zcela určeno tabulkami metadat. Mapování úlomek na pracovníka se označuje jako umístění střepů.

Uzel koordinátora přepíše dotazy do fragmentů, které `github_events_102027` odkazují na konkrétní tabulky jako a spustí tyto fragmenty na příslušné pracovníky. Tady je příklad dotazu spuštěného na pozadí a najít uzel, který drží id síta 102027.

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

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [vybrat distribuční sloupec](concepts-hyperscale-choose-distribution-column.md) pro distribuované tabulky.

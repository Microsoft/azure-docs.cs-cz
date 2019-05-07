---
title: Distribuovaných dat ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)
description: Tabulky a distribuovat ve skupině serveru horizontálních oddílů.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077332"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Distribuovaných dat ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

Tento článek popisuje, jak tři tabulky typů v Hyperškálovacím (Citus).
Ukazuje, jak distribuované tabulky se ukládají jako horizontální oddíly a způsob, jakým horizontální oddíly jsou umístěné na uzlech.

## <a name="table-types"></a>Typy tabulek

Existují tři typy tabulek v Hyperškálovacím skupiny serverů, každý použit pro různé účely.

### <a name="type-1-distributed-tables"></a>Typu 1: distribuované tabulky

Prvním typem a nejčastějším je *distribuované* tabulky. Zdá se být normální tabulky pro příkazy SQL, ale jsou vodorovně *dělené* napříč uzly pracovního procesu. To znamená, že řádky tabulky jsou uložené na jiných uzlech, a to v tabulkách fragment volá *horizontálních oddílů*.

Velkokapacitní spustí nejen SQL, ale příkazy DDL v celém clusteru, takže změna schématu tabulky distribuované uspořádá sebe aktualizovat všechny tabulky horizontálních oddílů napříč pracovních procesů.

#### <a name="distribution-column"></a>Sloupec distribuce

Velkokapacitní používá vylepšením horizontálního dělení přiřadit řádků do horizontálních oddílů. Přiřazení tvoří nedeterministicky závislosti na hodnotě sloupec tabulky s názvem *distribučního sloupce.* Správce clusteru musíte určit tento sloupec při distribuci tabulku.
Provádění tou správnou volbou je důležité pro výkon a funkčnost.

### <a name="type-2-reference-tables"></a>Typů 2: referenční tabulky

Referenční tabulky je typ distribuované tabulky, jejichž celý obsah se soustředí do jednoho horizontálního oddílu. Horizontálního oddílu je replikovat i na všech pracovních procesů, aby dotazy na libovolné pracovní měli přístup k místně, referenční informace bez režie sítě si vyžádá řádků z jiného uzlu. Referenční tabulky mít žádný sloupec distribuce, protože není potřeba rozlišovat samostatných horizontálních oddílech na každém řádku.

Referenční tabulky jsou obvykle malé a slouží k ukládání dat, která je relevantní pro dotazy, běžící na libovolném uzlu pracovního procesu. Například výčtové hodnoty jako pořadí stavy nebo kategorie produktů.

### <a name="type-3-local-tables"></a>Typů 3: místní tabulky

Při použití Hyperškálovatelného koordinační uzel, ke kterým se připojujete je standardní databázi PostgreSQL. Můžete vytvořit běžné tabulky na koordinátor a nechtějí horizontálního dělení je.

Vhodným kandidátem pro místní tabulky by malé pro správu tabulky, které nechcete podílet na dotazy spojení. Například uživatelé tabulku pro ověření a přihlášení aplikace.

## <a name="shards"></a>Horizontálních oddílů

Předchozí část popisuje jak distribuované tabulky se ukládají jako oddíly na pracovních uzlech. Získá další technické podrobnosti o této části.

`pg_dist_shard` Tabulku metadat na koordinátor obsahuje řádek pro každý horizontální oddíl každé distribuované tabulky v systému. Řádek odpovídá ID horizontálních oddílů s celou řadou celá čísla v rozsahu hodnoty hash (shardminvalue, shardmaxvalue):

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

Pokud chce určit, které horizontální oddíl uchovává řádek koordinační uzel `github_events`, hashuje hodnotu rozdělení sloupce v řádku a kontroluje horizontální oddíl\'s rozsah obsahuje hodnotu hash. (Rozsahů je definována tak, aby image hashovací funkce je jejich nesouvislý sjednocení.)

### <a name="shard-placements"></a>Umístění horizontálních oddílů

Předpokládejme, že je přidružený k řádku dotyčný 102027 tohoto horizontálního oddílu. Na řádku bude číst nebo zapisovat do tabulky nazvané `github_events_102027` v jednom zaměstnanců. Které pracovní? Která je určena výhradně tabulky metadat a mapování horizontálních oddílů k pracovnímu procesu se označuje jako horizontální oddíl *umístění*.

Koordinační uzel přepíše dotazy na fragmenty, které odkazují na konkrétní tabulky jako `github_events_102027`, a spouští tyto fragmenty na příslušné pracovní procesy. Tady je příklad dotazu spustit na pozadí se najít uzel uchovávající ID 102027 horizontálního oddílu.

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

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [zvolte sloupec distribuce](concepts-hyperscale-choose-distribution-column.md) pro distribuované tabulky

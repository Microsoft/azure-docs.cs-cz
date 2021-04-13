---
title: Vyrovnávání zatížení horizontálních oddílů-– škálovatelné (Citus) – Azure Database for PostgreSQL
description: Pro lepší výkon distribuujte horizontálních oddílů rovnoměrně mezi servery.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305697"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Změna rovnováhy horizontálních oddílů ve skupině serverů Citus ()

Aby bylo možné využít nově přidaných uzlů, je nutné znovu vyrovnávat distribuovanou tabulku [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých horizontálních oddílů z existujících uzlů do nových.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Určení, jestli skupina serverů potřebuje novou rovnováhu

Azure Portal může ukázat, jestli jsou data rovnoměrně rozložená mezi uzly pracovního procesu ve skupině serverů. Pokud se chcete podívat, přejděte na stránku **horizontálních oddílů k vyrovnávání zatížení** v nabídce **Správa skupin serverů** . Pokud jsou data mezi pracovními procesy nakloněná, doporučuje se, abyste si zobrazili nové **Vyrovnávání** zpráv spolu se seznamem velikostí jednotlivých uzlů.

Pokud jsou data už vyvážená, uvidíte, že se **v tuto chvíli nedoporučuje vyrovnávání** zpráv.

## <a name="run-the-shard-rebalancer"></a>Spuštění nástroje pro vyrovnávání zatížení horizontálních oddílů

Chcete-li spustit nástroj horizontálních oddílů pro vyrovnávání zatížení, je nutné se připojit k uzlu koordinátor skupiny serverů a spustit funkci [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL v distribuovaných tabulkách. Funkce znovu vyrovnává všechny tabulky ve skupině [kolocation](concepts-hyperscale-colocation.md) v tabulce s názvem v její argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Průběh rerovnováhy monitorování

Chcete-li sledovat Nástroj pro vyrovnávání zatížení po jeho spuštění, vraťte se na Azure Portal. Otevřete stránku **horizontálních oddílů pro vyrovnávání zatížení** ve **správě skupin serverů**. Bude se zobrazovat zpráva, že nové **Vyrovnávání zpráv probíhá** spolu se dvěma tabulkami.

První tabulka zobrazuje počet horizontálních oddílů přesunů do nebo ven z uzlu, například "6 z 24 přesunutých v". Druhá tabulka ukazuje průběh na tabulku databáze: název, ovlivněný počet horizontálních oddílů, ovlivněná velikost dat a stav opětovného vyvážení.

Kliknutím na tlačítko **aktualizovat** stránku aktualizujte. Po dokončení opětovného vyrovnávání se **v tuto chvíli nedoporučuje** znovu vyrovnávat.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
- Horizontální navýšení nebo navýšení [kapacity skupiny serverů](howto-hyperscale-scale-grow.md)
- Zobrazit referenční materiál [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)

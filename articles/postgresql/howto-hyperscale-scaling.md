---
title: Škálování skupiny serverů Azure Database for PostgreSQL Citus ()
description: Úprava paměti, disku a prostředků procesoru skupiny serverů, aby bylo možné řešit zvýšené zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262489"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Škálování skupiny serverů Citus (Scale-Scale)

Azure Database for PostgreSQL – Citus () zajišťuje škálování samoobslužných služeb pro řešení zvýšeného zatížení. Azure Portal usnadňuje přidávání nových pracovních uzlů.

Provedete to tak, že přejdete na kartu **Konfigurace** ve skupině serverů Citus (vlastní měřítko).
Pro změnu hodnoty přetáhněte posuvník pro **počet uzlů pracovního procesu** .

![Posuvníky prostředků](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klikněte na tlačítko Uložit, aby se změna hodnoty projevila.

> [!NOTE]
> Po zvýšení a uložení nelze počet uzlů pracovních procesů snížit pomocí posuvníku.
>
> Virtuální jádra a úložiště se ještě nedají upravit na koordinátora nebo pracovníky s tímto uživatelským rozhraním. Pokud potřebujete škálovat výpočetní výkon v koordinátoru nebo pracovních uzlech, otevřete lístek podpory.

Aby bylo možné využít nově přidaných uzlů, je nutné znovu vyrovnávat distribuovanou tabulku [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých horizontálních oddílů z existujících uzlů do nových. Pokud chcete spustit nástroj horizontálních oddílů pro vyrovnávání zatížení, připojte se k uzlu koordinátora clusteru pomocí psql a spusťte:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkce znovu vyrovnává všechny tabulky ve skupině kolocation v tabulce s názvem v její argumentu. [](concepts-hyperscale-colocation.md) `rebalance_table_shards` Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.

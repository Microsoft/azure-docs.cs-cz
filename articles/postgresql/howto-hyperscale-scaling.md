---
title: Škálování skupiny serverů – Citus (Scale-Scale) – Azure Database for PostgreSQL
description: Úprava paměti, disku a prostředků procesoru skupiny serverů, aby bylo možné řešit zvýšené zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280440"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Škálování skupiny serverů Citus (Scale-Scale)

Azure Database for PostgreSQL – Citus () zajišťuje škálování samoobslužných služeb pro řešení zvýšeného zatížení. Azure Portal usnadňuje přidávání nových pracovních uzlů a zvýšení virtuální jádra stávajících uzlů.

## <a name="add-worker-nodes"></a>Přidat pracovní uzly

Pokud chcete přidat uzly, ve skupině serverů (Citus) na vašem serveru použijte kartu **Konfigurovat** .  Přetažením posuvníku **počtu pracovních uzlů** se změní hodnota.

![Posuvníky prostředků](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klikněte na tlačítko **Uložit** , aby se změna hodnoty projevila.

> [!NOTE]
> Po zvýšení a uložení nelze počet uzlů pracovních procesů snížit pomocí posuvníku.

### <a name="rebalance-shards"></a>Vyvážit horizontálních oddílů

Aby bylo možné využít nově přidaných uzlů, je nutné znovu vyrovnávat distribuovanou tabulku [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých horizontálních oddílů z existujících uzlů do nových. Nejdřív ověřte, že nové pracovní procesy úspěšně dokončily zřizování. Pak spusťte nástroj horizontálních oddílů pro vyrovnávání zatížení tak, že se připojíte k uzlu koordinátora clusteru s psql a spuštěným:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkce `rebalance_table_shards` znovu vyrovnává všechny tabulky ve skupině [kolocation](concepts-hyperscale-colocation.md) v tabulce s názvem v argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

## <a name="increase-vcores"></a>Zvýšit virtuální jádra

Kromě přidávání nových uzlů můžete zvýšit možnosti existujících uzlů. Tato funkce je aktuálně ve verzi Preview – Pokud chcete požádat o zvýšení virtuální jádra pro uzly ve skupině serverů, obraťte se prosím na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.

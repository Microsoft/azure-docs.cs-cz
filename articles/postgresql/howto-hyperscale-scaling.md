---
title: Škálování skupiny serverů – Citus (Scale-Scale) – Azure Database for PostgreSQL
description: Úprava paměti, disku a prostředků procesoru skupiny serverů, aby bylo možné řešit zvýšené zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583997"
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

`rebalance_table_shards` Funkce znovu vyrovnává všechny tabulky ve skupině [kolocation](concepts-hyperscale-colocation.md) v tabulce s názvem v její argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zvětšení nebo zmenšení virtuální jádra na uzlech

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview. Pokud chcete požádat o změnu v virtuální jádra pro uzly ve skupině serverů, obraťte se prosím na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Kromě přidávání nových uzlů můžete zvýšit možnosti existujících uzlů. Navýšení a snížení kapacity výpočetní kapacity může být užitečné při experimentech s výkonem a na krátkodobé nebo dlouhodobé změny v požadavcích na provoz.

Chcete-li změnit virtuální jádra pro všechny pracovní uzly, upravte posuvník **virtuální jádra** v části **konfigurace (na jeden pracovní uzel)**. Virtuální jádra uzlu koordinátora se dá upravovat nezávisle. Klikněte na odkaz **změnit konfiguraci** v **uzlu koordinátor**. Zobrazí se dialogové okno s posuvníky pro virtuální jádra a úložnou kapacitu koordinátora. Změňte posuvníky podle potřeby a vyberte **OK**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.

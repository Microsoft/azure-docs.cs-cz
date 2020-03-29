---
title: Škálovat skupinu serverů – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Úprava paměti skupiny serverů, disků a prostředků procesoru pro řešení zvýšeného zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063102"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Škálování skupiny serverů Hyperscale (Citus)

Azure Database for PostgreSQL – Hyperscale (Citus) poskytuje samoobslužné škálování pro řešení zvýšené zatížení. Portál Azure usnadňuje přidávání nových pracovních uzlů a zvýšení virtuálních jader existujících uzlů.

## <a name="add-worker-nodes"></a>Přidání pracovních uzlů

Chcete-li přidat uzly, přejděte na kartu **Konfigurace** ve skupině serverů Hyperscale (Citus).  Přetažením jezdce pro **počet uzlů pracovního procesu se** změní hodnota.

![Posuvníky zdrojů](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klikněte na tlačítko **Uložit,** aby se změněná hodnota projevila.

> [!NOTE]
> Po zvýšení a uložení nelze snížit počet pracovních uzlů pomocí posuvníku.

### <a name="rebalance-shards"></a>Znovu vyvážit úlomky

Chcete-li využít výhod nově přidaných uzlů, musíte znovu vyvážit distribuované [úlomky tabulek](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých úlomků z existujících uzlů do nových. Nejprve ověřte, zda noví pracovníci úspěšně dokončili zřizování. Potom spusťte rebalancer střepu připojením k uzlu koordinátora clusteru s psql a spuštěním:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkce `rebalance_table_shards` znovu vyrovná všechny tabulky ve skupině [kolokace](concepts-hyperscale-colocation.md) tabulky pojmenované v jejím argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji zavolat na reprezentativní tabulku z každé skupiny kolokace.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zvýšení nebo snížení virtuálních jader na uzlech

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview. Chcete-li požádat o změnu virtuálních jader pro uzly ve skupině serverů, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Kromě přidání nových uzlů můžete zvýšit možnosti existujících uzlů. Nastavení výpočetní kapacity nahoru a dolů může být užitečné pro experimenty s výkonem, stejně jako krátkodobé nebo dlouhodobé změny požadavků na provoz.

Chcete-li změnit virtuální jádra pro všechny pracovní uzly, upravte jezdec **virtuálních jader** v části **Konfigurace (na pracovní uzel).** Virtuální jádra uzlu koordinátora lze upravit nezávisle. Klepněte na odkaz **Změnit konfiguraci** v uzlu **koordinátora**. Zobrazí se dialogové okno s posuvníky pro virtuální jádra a úložnou kapacitu koordinátora. Podle potřeby změňte posuvníky a vyberte **OK**.

## <a name="next-steps"></a>Další kroky

Další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů .

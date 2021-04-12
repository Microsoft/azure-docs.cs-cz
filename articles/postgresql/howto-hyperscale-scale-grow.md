---
title: Škálování skupiny serverů – Citus (Scale-Scale) – Azure Database for PostgreSQL
description: Úprava paměti, disku a prostředků procesoru skupiny serverů, aby bylo možné řešit zvýšené zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012525"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Škálování skupiny serverů Citus (Scale-Scale)

Azure Database for PostgreSQL – Citus () zajišťuje škálování samoobslužných služeb pro řešení zvýšeného zatížení. Azure Portal usnadňuje přidávání nových pracovních uzlů a zvýšení virtuální jádra stávajících uzlů. Přidáním uzlů dojde k žádnému výpadku a dokonce i přesunutí horizontálních oddílů do nových uzlů (nazývaných [horizontálních oddílů rebalancing](howto-hyperscale-scale-rebalance.md)) proběhne bez přerušení dotazů.

## <a name="add-worker-nodes"></a>Přidat pracovní uzly

Pokud chcete přidat uzly, klikněte na kartu **COMPUTE + Storage** ve skupině serverů Citus (škálování).  Přetažením posuvníku **počtu pracovních uzlů** se změní hodnota.

> [!NOTE]
>
> Skupina serverů Citus () vytvořená s [úrovní Basic (Preview)](concepts-hyperscale-tiers.md) nemá žádné pracovní procesy. Zvýšení počtu pracovních procesů automaticky prodlužuje skupinu serverů na úroveň Standard.
> Až provedete prostupnou skupinu serverů na úroveň Standard, nebudete ji moct převést zpět na úroveň Basic.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Posuvníky prostředků":::

Klikněte na tlačítko **Uložit** , aby se změna hodnoty projevila.

> [!NOTE]
> Po zvýšení počtu a uložení pracovních uzlů nelze jejich počet snížit pomocí posuvníku.

> [!NOTE]
> Aby bylo možné využít nově přidaných uzlů, je nutné znovu [vyrovnávat distribuovanou tabulku horizontálních oddílů](howto-hyperscale-scale-rebalance.md), což znamená přesunutí některých [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards) z existujících uzlů do nových.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zvýšení nebo snížení počtu virtuálních jader v uzlech

Kromě přidání nových uzlů můžete zvýšit možnosti stávajících uzlů. Navýšení a snížení kapacity výpočetní kapacity může být užitečné pro experimenty s výkonem a krátkodobé nebo dlouhodobé změny v požadavcích na provoz.

Chcete-li změnit virtuální jádra pro všechny pracovní uzly, upravte posuvník **virtuální jádra** v části **konfigurace (na jeden pracovní uzel)**. Virtuální jádra uzlu koordinátora se dá upravovat nezávisle. V části **konfigurace (uzel koordinátora)** upravte posuvník **virtuální jádra** .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
- [Vyvážení distribuované tabulky horizontálních oddílů](howto-hyperscale-scale-rebalance.md) , aby se všechny pracovní uzly mohly účastnit paralelních dotazů

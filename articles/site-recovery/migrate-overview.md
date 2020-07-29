---
title: Migrace serverů a virtuálních počítačů do Azure pomocí Azure Site Recovery
description: Popisuje, jak migrovat místní a virtuální počítače Azure IaaS do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281306"
---
# <a name="about-migration"></a>Informace o migraci

Službu Azure Migrate můžete použít k migraci virtuálních počítačů a serverů do Azure místo služby Azure Site Recovery. [Přečtěte si další informace](../migrate/migrate-services-overview.md) o Azure Migrate.


## <a name="why-use-azure-migrate"></a>Proč používat službu Azure Migrate?

Použití Azure Migrate pro migraci přináší několik výhod:
 
 
- Azure Migrate poskytuje centralizované centrum pro zjišťování, posuzování a migraci do Azure.
- Použití Azure Migrate zajišťuje interoperabilitu a budoucí rozšiřitelnost pomocí nástrojů Azure Migrate, dalších služeb Azure a nástrojů třetích stran.
- Azure Migrate: Nástroj pro migraci serveru je založený na účelu navržený pro migraci serveru do Azure. Je optimalizovaná pro migraci. Nemusíte se učit o konceptech a scénářích, které nejsou přímo důležité pro migraci. 
- Pro migraci na 180 dní se neúčtují žádné poplatky za použití nástrojů od spuštění replikace pro virtuální počítač. Tím získáte čas na dokončení migrace. Platíte jenom za úložiště a síťové prostředky, které se používají při replikaci, a za poplatky za výpočetní výkon spotřebované během testovacích migrací.
- Pro virtuální počítače VMware Azure Migrate poskytuje kromě migrace na základě agenta i migraci bez agenta.
- Pro Azure Migrate nabízíme prioritní nové funkce, které nabízí jenom nástroj pro migraci serveru.

## <a name="when-to-use-site-recovery"></a>Kdy použít Site Recovery?

Site Recovery by se měla použít:

- Pro zotavení po havárii místních počítačů do Azure.
- Pro zotavení po havárii virtuálních počítačů Azure mezi oblastmi Azure.

I když doporučujeme použít Azure Migrate k migraci místních serverů do Azure, pokud jste už zahájili migraci migrace pomocí Site Recovery, můžete ji dál používat k dokončení migrace.  

## <a name="next-steps"></a>Další kroky

> [Přečtěte si běžné otázky](../migrate/resources-faq.md) týkající se Azure Migrate.

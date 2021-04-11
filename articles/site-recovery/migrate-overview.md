---
title: Porovnání Azure Migrate a Site Recovery migrace do Azure
description: Shrnuje výhody použití Azure Migrate k migraci místo Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: c4d30b8b21bf3a0ea27467d2120b04a107f5a73f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581412"
---
# <a name="migrating-to-azure"></a>Migrace do Azure

Pro migraci doporučujeme použít službu Azure Migrate k migraci virtuálních počítačů a serverů do Azure místo služby Azure Site Recovery. [Přečtěte si další informace](../migrate/migrate-services-overview.md) o Azure Migrate.


## <a name="why-use-azure-migrate"></a>Proč používat službu Azure Migrate?

Použití Azure Migrate pro migraci přináší několik výhod:
 
 
- Azure Migrate poskytuje centralizované centrum pro zjišťování, posuzování a migraci do Azure.
- Použití Azure Migrate zajišťuje interoperabilitu a budoucí rozšiřitelnost pomocí nástrojů Azure Migrate, dalších služeb Azure a nástrojů třetích stran.
- Azure Migrate: Nástroj pro migraci serveru je založený na účelu navržený pro migraci serveru do Azure. Je optimalizovaná pro migraci. Nemusíte se učit o konceptech a scénářích, které nejsou přímo důležité pro migraci. 
- Pro migraci na 180 dní se neúčtují žádné poplatky za použití nástrojů od spuštění replikace pro virtuální počítač. Tím získáte čas na dokončení migrace. Platíte jenom za úložiště a síťové prostředky, které se používají při replikaci, a za poplatky za výpočetní výkon spotřebované během testovacích migrací.
- Azure Migrate podporuje všechny scénáře migrace podporované Site Recovery. Pro virtuální počítače VMware navíc Azure Migrate poskytuje možnost migrace bez agenta.
- Pro Azure Migrate nabízíme prioritní nové funkce, které nabízí jenom nástroj pro migraci serveru. Tyto funkce nejsou cílené na Site Recovery.

## <a name="when-to-use-site-recovery"></a>Kdy použít Site Recovery?

Site Recovery by se měla použít:

- Pro zotavení po havárii místních počítačů do Azure.
- Pro zotavení po havárii virtuálních počítačů Azure mezi oblastmi Azure.

I když doporučujeme použít Azure Migrate k migraci místních serverů do Azure, pokud jste už zahájili migraci migrace pomocí Site Recovery, můžete ji dál používat k dokončení migrace.  

## <a name="next-steps"></a>Další kroky

> [Přečtěte si běžné otázky](../migrate/resources-faq.md) týkající se Azure Migrate.

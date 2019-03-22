---
title: Informace o migraci místních počítačů a Azure virtuální počítače Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak migrovat místní a virtuální počítače Azure IaaS do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315629"
---
# <a name="about-migration"></a>Informace o migraci

V tomto článku rychlý přehled o tom, jak [Azure Site Recovery](site-recovery-overview.md) service vám umožní migrovat počítače. 

Zde je, je možné migrovat pomocí služby Site Recovery:

- **Migrace do Azure z místního**: Migrace místních virtuálních počítačů Hyper-V, virtuálních počítačů VMware a fyzických serverů do Azure. Po dokončení migrace budou úlohy spouštěné na místních počítačích běžet na virtuálních počítačích Azure. 
- **Migrovat v rámci Azure**: Migrace virtuálních počítačů Azure mezi oblastmi Azure. 
- **Migrace AWS**: Migrace instancí Windows AWS do virtuálních počítačů Azure IaaS. 


## <a name="what-do-we-mean-by-migration"></a>Co myslíme pojmem migrace?

Kromě používání Site Recovery pro zotavení po havárii místní a virtuální počítače Azure, můžete použít služby Site Recovery k migraci je. Jaký je rozdíl?

- Pro zotavení po havárii se počítače v pravidelných intervalech replikovat do Azure. Pokud dojde k výpadku, můžete převzít služby počítačů z primární lokality do sekundární lokality Azure a přistupovat k nim tam. Pokud primární lokalita opět dostupná, převezme služby při obnovení z Azure.
- Pro migraci replikujete místní počítače do Azure nebo virtuální počítače Azure do sekundární oblasti. Pak selhání virtuálního počítače z primární lokality do sekundární a dokončete proces migrace. Žádné navrácení služeb po obnovení se neprovede.  


## <a name="migration-scenarios"></a>Scénáře migrace

**Scénář** | **Podrobnosti**
--- | ---
**Migrace z místní do Azure** | Můžete migrovat místní virtuální počítače VMware, virtuálních počítačů Hyper-V a fyzické servery do Azure. K tomuto účelu dokončení téměř stejným způsobem jako při úplné havárii. Jednoduše není počítačů obnovení z Azure do místní lokality nezdaří.
**Migrace mezi oblastmi Azure** | Můžete migrovat virtuální počítače Azure z jedné oblasti Azure do jiné. Po dokončení migrace můžete nakonfigurovat zotavení po havárii pro virtuální počítače Azure nyní v sekundární oblasti, do které jste migrovali.
**Migrace AWS do Azure** | Můžete migrovat instance AWS do virtuálních počítačů Azure. Site Recovery zachází jako s fyzickými servery pro účely migrace instancí AWS. 

## <a name="next-steps"></a>Další postup

- [Migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md)
- [Migrace virtuálních počítačů z jedné oblasti Azure do jiné](azure-to-azure-tutorial-migrate.md)
- [Migrace AWS do Azure](migrate-tutorial-aws-azure.md)

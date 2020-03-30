---
title: Migrace serverů a virtuálních počítačů do Azure pomocí Azure Site Recovery
description: Popisuje, jak migrovat místní virtuální počítače Azure IaaS do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257937"
---
# <a name="about-migration"></a>Informace o migraci

Přečtěte si tento článek, kde najdete rychlý přehled o tom, jak vám služba [Azure Site Recovery](site-recovery-overview.md) pomáhá migrovat počítače. 

Co můžete migrovat pomocí site recovery:

- **Migrujte z místního do Azure:** Migrujte místní virtuální počítače Hyper-V, virtuální počítače VMware a fyzické servery do Azure. Po dokončení migrace budou úlohy spouštěné na místních počítačích běžet na virtuálních počítačích Azure. 
- **Migrovat v rámci Azure:** Migrují se virtuální počítače Azure mezi oblastmi Azure. 
- **Migrovat AWS:** Migrují se instance Windows AWS do virtuálních počítačů Azure IaaS. 

> [!NOTE]
> Teď můžete migrovat z místního do Azure pomocí služby Azure Migrate. [Další informace](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Co myslíme pojmem migrace?

Kromě použití site recovery pro zotavení po havárii místních virtuálních počítačích a virtuálních počítačích Azure, můžete použít službu Site Recovery k jejich migraci. V čem spočívá rozdíl?

- Pro zotavení po havárii replikovat počítače v pravidelných intervalech do Azure. Dojde-li k výpadku, selhání počítače z primární lokality do sekundární lokality Azure a přístup k nim odtud. Když je primární lokalita znovu k dispozici, můžete z Azure navrácení mů e-li.
- Pro migraci replikujete místní počítače do Azure nebo virtuálních počítačů Azure do sekundární oblasti. Pak se nezdaří virtuálního virtuálního zařízení z primární lokality na sekundární a dokončení procesu migrace. Žádné navrácení služeb po obnovení se neprovede.  


## <a name="migration-scenarios"></a> Scénáře migrace

**Scénář** | **Podrobnosti**
--- | ---
**Migrace z místního do Azure** | Do Azure můžete migrovat místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery. Chcete-li to provést, dokončíte téměř stejné kroky jako pro úplné zotavení po havárii. Jednoduše nepobijete počítače zpět z Azure do místního webu.
**Migrace mezi oblastmi Azure** | Virtuální počítače Azure můžete migrovat z jedné oblasti Azure do jiné. Po dokončení migrace můžete nakonfigurovat zotavení po havárii pro virtuální počítače Azure nyní v sekundární oblasti, do které jste migrovali.
**Migrace AWS do Azure** | Můžete migrovat instance AWS do virtuálních počítačů Azure. Site Recovery zachází s instancemi AWS jako s fyzickými servery pro účely migrace. 

## <a name="next-steps"></a>Další kroky

- [Migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md)
- [Migrace virtuálních počítačů z jedné oblasti Azure do jiné](azure-to-azure-tutorial-migrate.md)
- [Migrace AWS do Azure](migrate-tutorial-aws-azure.md)

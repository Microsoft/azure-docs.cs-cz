---
title: Migrace místních počítačů a virtuálních počítačů Azure Azure Site Recovery
description: Tento článek popisuje, jak migrovat místní a virtuální počítače Azure IaaS do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: c043950de9565f96d52c848f96efac80385f2321
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814482"
---
# <a name="about-migration"></a>Informace o migraci

Přečtěte si tento článek, kde najdete rychlý přehled toho, jak služba [Azure Site Recovery](site-recovery-overview.md) pomáhá migrovat počítače. 

Můžete provést migraci pomocí Site Recovery:

- **Migrace z místního prostředí do Azure**: Migrujte místní virtuální počítače Hyper-V, virtuální počítače VMware a fyzické servery do Azure. Po dokončení migrace budou úlohy spouštěné na místních počítačích běžet na virtuálních počítačích Azure. 
- **Migrace v rámci Azure**: Migrujte virtuální počítače Azure mezi oblastmi Azure. 
- **Migrace AWS**: Migrujte instance Windows AWS do virtuálních počítačů Azure IaaS. 

> [!NOTE]
> Teď můžete migrovat z místního prostředí do Azure pomocí služby Azure Migrate. [Další informace](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Co myslíme pojmem migrace?

Kromě používání Site Recovery pro zotavení po havárii místních a virtuálních počítačů Azure můžete k jejich migraci použít službu Site Recovery. Jaký je rozdíl?

- V případě zotavení po havárii pravidelně replikují počítače do Azure. V případě výpadku dojde k selhání počítačů z primární lokality do sekundární lokality Azure a získáte k nim přístup. Jakmile bude primární lokalita opět dostupná, vrátíte se z Azure zpátky.
- Pro migraci replikujte místní počítače do Azure nebo virtuální počítače Azure do sekundární oblasti. Pak dojde k selhání virtuálního počítače z primární lokality do sekundárního a dokončení procesu migrace. Žádné navrácení služeb po obnovení se neprovede.  


## <a name="migration-scenarios"></a>Scénáře migrace

**Scénář** | **Podrobnosti**
--- | ---
**Migrace z místního prostředí do Azure** | Můžete migrovat místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery do Azure. K tomu je potřeba provést téměř stejný postup jako při úplném zotavení po havárii. Nedaří se vám jednoduše navrátit počítače z Azure do místní lokality.
**Migrace mezi oblastmi Azure** | Virtuální počítače Azure můžete migrovat z jedné oblasti Azure do jiné. Po dokončení migrace můžete nakonfigurovat zotavení po havárii pro virtuální počítače Azure hned v sekundární oblasti, do které jste migrovali.
**Migrace AWS do Azure** | Můžete migrovat instance AWS do virtuálních počítačů Azure. Site Recovery považuje instance AWS za fyzické servery pro účely migrace. 

## <a name="next-steps"></a>Další postup

- [Migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md)
- [Migrace virtuálních počítačů z jedné oblasti Azure do jiné](azure-to-azure-tutorial-migrate.md)
- [Migrace AWS do Azure](migrate-tutorial-aws-azure.md)

---
title: Migrace místních počítačů pomocí Azure Migrate
description: Tento článek shrnuje, jak migrovat místní počítače do Azure a doporučuje Azure Migrate.
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87847308"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrace místních počítačů do Azure

Tento článek popisuje možnosti migrace místních počítačů do Azure. 

## <a name="migrate-with-azure-migrate"></a>Migrace pomocí Azure Migrate

K migraci počítačů do Azure doporučujeme použít službu [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate je účelově sestavený pro migraci serveru. Azure Migrate poskytuje centralizované centrum pro zjišťování, posuzování a migraci místních počítačů do Azure.

Pomocí těchto odkazů proveďte migraci s Azure Migrate:

- [Přečtěte si o](../migrate/server-migrate-overview.md) možnostech migrace pro virtuální počítače VMware a pak migrujte virtuální počítače do Azure pomocí [agenta bez agentů](../migrate/tutorial-migrate-vmware.md) nebo migrace [založené na agentech](../migrate/tutorial-migrate-vmware-agent.md) .
- [Migrujte virtuální počítače Hyper-V](../migrate/tutorial-migrate-hyper-v.md) do Azure.
- Migrujte [fyzické servery nebo jiné virtuální počítače](../migrate/tutorial-migrate-physical-virtual-machines.md)včetně [instancí AWS](../migrate/tutorial-migrate-aws-virtual-machines.md) do Azure.

## <a name="migrate-with-site-recovery"></a>Migrace pomocí Site Recovery
Site Recovery by se měly používat jenom pro zotavení po havárii, a ne na migraci.

Pokud již používáte Azure Site Recovery a chcete ho nadále používat pro migraci, postupujte podle kroků, které používáte pro zotavení po havárii.

- Virtuální počítače VMware: [Připravte si Azure](tutorial-prepare-azure.md) a [VMware](vmware-azure-tutorial-prepare-on-premises.md), začněte [replikovat počítače](vmware-azure-tutorial.md), [Ověřte](tutorial-dr-drill-azure.md) , že všechno funguje, a [spusťte převzetí služeb při selhání](vmware-azure-tutorial-failover-failback.md).
- Virtuální počítače Hyper-V [: Připravte si Azure](tutorial-prepare-azure-for-hyperv.md) a [Hyper-v](hyper-v-prepare-on-premises-tutorial.md), začněte [replikovat počítače](hyper-v-azure-tutorial.md), [Ověřte](tutorial-dr-drill-azure.md) , že všechno funguje, a [spusťte převzetí služeb při selhání](hyper-v-azure-failover-failback-tutorial.md).
- Fyzické servery: [Projděte si návod](physical-azure-disaster-recovery.md) k přípravě Azure, přípravě počítačů na zotavení po havárii a nastavení replikace.

> [!NOTE]
> Když spustíte převzetí služeb při selhání pro zotavení po havárii, jako poslední krok potvrdíte převzetí služeb při selhání. Při migraci místních počítačů není možnost **potvrzení** relevantní. Místo toho vyberte možnost **kompletní migrace** . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si běžné otázky](../migrate/resources-faq.md) týkající se Azure Migrate.

  

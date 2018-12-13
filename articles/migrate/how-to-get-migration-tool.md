---
title: Migrace počítačů po posouzení pomocí Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak získat doporučení pro migrace počítačů po spuštění posouzení pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249767"
---
# <a name="migrate-machines-after-assessment"></a>Migrace počítačů po posouzení


[Azure Migrate](migrate-overview.md) posuzuje vhodnost místních počítačů ke kontrole, jestli jsou vhodné k migraci do Azure a poskytuje odhady velikostí a nákladů pro spuštění počítače v Azure. V současné době Azure Migrate posuzuje pouze počítačů pro migraci. Samotné migrace se aktuálně provádí pomocí dalších služeb Azure.

Tento článek popisuje, jak získat návrhy pro nástroj pro migraci, až spustíte posouzení migrace.

> [!NOTE]
> Návrh nástroje Migrace není k dispozici ve službě Azure Government.

## <a name="migration-tool-suggestion"></a>Návrh nástroj pro migraci

Pokud chcete získat návrhy týkající se nástroje pro migraci, je potřeba hloubkové zjišťování v místním prostředí. Hloubkové zjišťování provádí instalaci agentů na místních počítačích.  

1. Vytvoření projektu Azure Migrate, zjišťování místních počítačů a vytvořit posouzení migrace. [Další informace](tutorial-assessment-vmware.md).
2. Stáhněte a nainstalujte agenty Azure Migrate na každém počítači v místním, pro kterou chcete zobrazit metody doporučené migrace. [Tento postup](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) nainstalovat agenty.
2. Identifikujte své místní počítače, které jsou vhodné k migraci lift and shift. Toto jsou virtuální počítače, které nevyžadují žádné změny aplikace, které běží na nich a je možné migrovat, protože je.
3. Pro migraci lift and shift doporučujeme pomocí služby Azure Site Recovery. [Další informace](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternativně můžete použít nástroje třetích stran, které podporují migraci do Azure.
4. Pokud máte místní počítače, které nejsou vhodné pro migraci lift and shift to znamená, pokud chcete migrovat konkrétní aplikaci, nikoli celý virtuální počítač, můžete použít jiné nástroje pro migraci. Například, doporučujeme [Azure Database Migration service](https://azure.microsoft.com/campaigns/database-migration/) Pokud budete chtít migrovat místní databáze takové SQL serveru, MySQL nebo Oracle do Azure.


## <a name="review-suggested-migration-methods"></a>Zkontrolujte navrhované migrace metody

1. Než budete moct získat metodu navrhované migrace, budete muset vytvořit projekt Azure Migrate, přečtěte si na místních počítačích a spusťte posouzení migrace. [Další informace](tutorial-assessment-vmware.md).
2. Jakmile bude posouzení hotové, zobrazit ho v projektu > **přehled** > **řídicí panel**. Klikněte na tlačítko **posouzení připravenosti**

    ![Posouzení připravenosti](./media/tutorial-assessment-vmware/assessment-report.png)  

3. V **navrhovaný nástroj**, zkontrolujte návrhy pro nástroje, které můžete použít pro migraci.

    ![Navrhovaný nástroj](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.

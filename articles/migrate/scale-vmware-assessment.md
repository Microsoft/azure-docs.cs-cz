---
title: Vyhodnoťte velký počet virtuálních počítačů VMware pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit velký počet virtuálních počítačů VMware pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: c9c57a07100f2ea6db86408826bf74d05c8df5aa
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868686"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Vyhodnocení velkého počtu virtuálních počítačů VMware pro migraci do Azure


Tento článek popisuje, jak vyhodnotit Velká čísla (1000 35000) místních virtuálních počítačů VMware pro migraci do Azure pomocí nástroje pro vyhodnocení serveru Azure Migrate.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánujte hodnocení ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a připravte VMware na posouzení.
> * Vytvořte projekt Azure Migrate a vytvořte posouzení.
> * Projděte si hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet zkušební verzi, abyste si vyhodnotili několik virtuálních počítačů před vyhodnocením škálování, postupujte podle našich [kurzů](tutorial-prepare-vmware.md) .

## <a name="plan-for-assessment"></a>Plánování posouzení

Při plánování posouzení velkého počtu virtuálních počítačů VMware je potřeba zvážit několik věcí:

- **Plánování Azure Migrate projektů**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud jsou datová centra v různých geografických oblastech nebo potřebujete ukládat metadata pro zjišťování, hodnocení nebo migraci v různých zeměpisných oblastech, možná budete potřebovat více projektů. 
- **Zařízení pro plánování**: Azure Migrate používá místní zařízení Azure Migrate nasazené jako virtuální počítač VMware pro průběžné zjišťování virtuálních počítačů. Zařízení monitoruje změny prostředí, jako je například přidání virtuálních počítačů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení potřebujete nasadit.
- **Plánování účtů pro zjišťování**: Zařízení Azure Migrate používá účet s přístupem k vCenter Server, aby bylo možné zjistit virtuální počítače pro účely posouzení a migrace. Pokud zjišťujete více než 10 000 virtuálních počítačů, nastavte více účtů.


## <a name="planning-limits"></a>Omezení plánování
 
Pro plánování použijte omezení shrnutá v této tabulce.

**Plánování** | **Omezení**
--- | --- 
**Azure Migrate projekty** | Vyhodnoťte až 35 000 virtuálních počítačů v projektu.
**Zařízení Azure Migrate** | Zařízení se může připojit jenom k jednomu vCenter Server.<br/><br/> Zařízení může být přidruženo pouze k jednomu Azure Migrate projektu.<br/> Zařízení může na vCenter Server zjistit až 10 000 virtuálních počítačů.
**Posouzení Azure Migrate** | V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.

Tato omezení jsou uvedená v některých ukázkových nasazeních:


**Server vCenter** | **Virtuální počítače na serveru** | **Doporučení** | **Akce**
---|---|---
hodinu | < 10 000 | Jeden Azure Migrate projekt.<br/> Jedno zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavte zařízení, připojte se k vCenter Server pomocí účtu.
hodinu | > 10 000 | Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Několik účtů vCenter. | Nastavte zařízení pro každý virtuální počítač 10 000.<br/><br/> Nastavte účty vCenter a vydělte inventář, abyste omezili přístup k účtu na méně než 10 000 virtuálních počítačů.<br/> Připojte každé zařízení k vCenter serveru pomocí účtu.<br/> Můžete analyzovat závislosti mezi počítači, které jsou zjištěny s různými zařízeními.
Několik | < 10 000 |  Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavte zařízení, připojte se k vCenter Server pomocí účtu.<br/> Můžete analyzovat závislosti mezi počítači, které jsou zjištěny s různými zařízeními.
Několik | > 10 000 | Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Několik účtů vCenter. | Pokud vCenter Server Discovery < 10 000 virtuálních počítačů, nastavte zařízení pro jednotlivé vCenter Server.<br/><br/> Pokud vCenter Server Discovery > 10 000 virtuálních počítačů, nastavte zařízení pro každých 10 000 virtuálních počítačů.<br/> Nastavte účty vCenter a vydělte inventář, abyste omezili přístup k účtu na méně než 10 000 virtuálních počítačů.<br/> Připojte každé zařízení k vCenter serveru pomocí účtu.<br/> Můžete analyzovat závislosti mezi počítači, které jsou zjištěny s různými zařízeními.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Plánování zjišťování v prostředí s více klienty

Pokud plánujete pro více tenantů prostředí, můžete určit rozsah zjišťování vCenter Server.

- Rozsah zjišťování zařízení můžete nastavit vCenter Server datacentrech, clusterech nebo složkách clusterů, hostitelů nebo složek hostitelů nebo jednotlivých virtuálních počítačů.
- Pokud je vaše prostředí sdíleno mezi klienty a chcete zjistit jednotlivé klienty samostatně, můžete nastavit rozsah přístupu k účtu vCenter, který zařízení používá ke zjišťování. 
    - Můžete chtít obor podle složek virtuálních počítačů, pokud klienti sdílejí hostitele. Azure Migrate nemůže zjistit virtuální počítače, pokud má účet vCenter udělen přístup na úrovni složky virtuálního počítače vCenter. Pokud chcete určit obor zjišťování pomocí složek virtuálních počítačů, můžete to provést tak, že účet vCenter má přiřazený přístup jen pro čtení na úrovni virtuálního počítače. Další informace o zjišťování oboru [najdete tady](tutorial-assess-vmware.md#scoping-discovery).

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Připravte Azure a VMware pro vyhodnocování serveru. 

1. Ověřte [požadavky a omezení podpory VMware](migrate-support-matrix-vmware.md).
2. Nastavte oprávnění pro účet Azure pro interakci s Azure Migrate.
3. Připravte VMware na posouzení.

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](tutorial-prepare-vmware.md) .


## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Vytvořte Azure Migrate projekty.
2. Přidejte do projektů Nástroj pro vyhodnocení Azure Migrate serveru.

[Víc se uč](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte posouzení pro virtuální počítače VMware.
1. Projděte si posouzení v části Příprava na plánování migrace.


Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](tutorial-assess-vmware.md) .
    

## <a name="next-steps"></a>Další postup

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno pro škálování Azure Migrate posouzení pro virtuální počítače VMware
> * Připravené Azure a VMware pro posouzení
> * Vytvořil se Azure Migrate projekt a běžela posouzení.
> * Revidována posouzení při přípravě na migraci.

Nyní se [naučíte](concepts-assessment-calculation.md) počítat vyhodnocení a postup [Úpravy hodnocení](how-to-modify-assessment.md).

---
title: Vyhodnoťte velký počet virtuálních počítačů Hyper-V pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit velký počet virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: f54283e4e63af22b30bddf97456eed75de3806cc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492960"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Vyhodnocení velkého počtu virtuálních počítačů Hyper-V pro migraci do Azure

Tento článek popisuje, jak vyhodnotit velké počty místních virtuálních počítačů Hyper-V pro migraci do Azure pomocí nástroje Azure Migrate Server Assessment Tool.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 


V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánujte hodnocení ve velkém měřítku.
> * Konfigurace oprávnění Azure a příprava technologie Hyper-V pro posouzení.
> * Vytvořte projekt Azure Migrate a vytvořte posouzení.
> * Projděte si hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet zkušební verzi, abyste si vyhodnotili několik virtuálních počítačů před vyhodnocením škálování, postupujte podle našich [kurzů](./tutorial-discover-hyper-v.md) .

## <a name="plan-for-assessment"></a>Plánování posouzení

Při plánování posouzení velkého počtu virtuálních počítačů s technologií Hyper-V existuje několik věcí, které je třeba vzít v úvahu:

- **Plánování Azure Migrate projektů**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud jsou datová centra v různých geografických oblastech nebo potřebujete ukládat metadata pro zjišťování, hodnocení nebo migraci v různých zeměpisných oblastech, možná budete potřebovat více projektů.
- **Zařízení pro plánování**: Azure Migrate používá místní zařízení Azure Migrate nasazené jako virtuální počítač s technologií Hyper-V pro průběžné zjišťování virtuálních počítačů pro účely posouzení a migrace. Zařízení monitoruje změny prostředí, jako je například přidání virtuálních počítačů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení se má nasadit.


## <a name="planning-limits"></a>Omezení plánování
 
Pro plánování použijte omezení shrnutá v této tabulce.

**Plánování** | **Omezení**
--- | --- 
**Azure Migrate projekty** | Vyhodnoťte až 35 000 virtuálních počítačů v projektu.
**Zařízení Azure Migrate** | Zařízení může zjistit až 5000 virtuálních počítačů.<br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.<br/> Zařízení může být přidruženo pouze k jednomu Azure Migrate projektu.<br/> K jednomu Azure Migrate projektu může být přidružen libovolný počet zařízení. <br/><br/> 
**Skupina** | Do jedné skupiny můžete přidat až 35 000 virtuálních počítačů.
**Posouzení Azure Migrate** | V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.



## <a name="other-planning-considerations"></a>Další požadavky na plánování

- Pokud chcete spustit zjišťování ze zařízení, musíte vybrat každého hostitele Hyper-V. 
- Pokud používáte víceklientské prostředí, nemůžete aktuálně zjišťovat jenom virtuální počítače, které patří do konkrétního tenanta. 

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Připravte Azure a technologii Hyper-V pro vyhodnocování serveru. 

1. Ověřte [požadavky a omezení podpory technologie Hyper-V](migrate-support-matrix-hyper-v.md).
2. Nastavení oprávnění pro účet Azure pro interakci s Azure Migrate
3. Příprava hostitelů a virtuálních počítačů Hyper-V

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-discover-hyper-v.md) .

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Vytvořte Azure Migrate projekty.
2. Přidejte do projektů Nástroj pro vyhodnocení Azure Migrate serveru.

[Další informace](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte posouzení pro virtuální počítače Hyper-V.
1. Projděte si posouzení v části Příprava na plánování migrace.

[Přečtěte si další informace](tutorial-assess-hyper-v.md) o vytváření a kontrole hodnocení.
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno pro škálování Azure Migrate posouzení pro virtuální počítače Hyper-V
> * Připravené Azure a technologie Hyper-V pro posouzení
> * Vytvořil se Azure Migrate projekt a běžela posouzení.
> * Revidována posouzení při přípravě na migraci.

Nyní se [dozvíte, jak](concepts-assessment-calculation.md) se počítají posouzení a jak [upravovat hodnocení](how-to-modify-assessment.md) .
---
title: Vyhodnoťte velký počet fyzických serverů pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit velký počet fyzických serverů pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: 6009975337f8f4caaf43e4b8b715e42c6ce2ffdc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494575"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Vyhodnocení velkého počtu fyzických serverů pro migraci do Azure

Tento článek popisuje, jak pomocí nástroje pro vyhodnocení serveru Azure Migrate vyhodnotit velký počet místních fyzických serverů pro migraci do Azure.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 


V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánujte hodnocení ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a připravte fyzické servery k posouzení.
> * Vytvořte projekt Azure Migrate a vytvořte posouzení.
> * Projděte si hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet zkušební verzi, abyste si vyhodnotili několik serverů před hodnocením škálování, postupujte podle našich [kurzů](./tutorial-discover-physical.md).

## <a name="plan-for-assessment"></a>Plánování posouzení

Při plánování posouzení velkého počtu fyzických serverů je třeba zvážit několik věcí:

- **Plánování Azure Migrate projektů**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud jsou datová centra v různých geografických oblastech nebo potřebujete ukládat metadata pro zjišťování, hodnocení nebo migraci v různých zeměpisných oblastech, možná budete potřebovat více projektů.
- **Zařízení pro plánování**: Azure Migrate používá místní zařízení Azure Migrate nasazené na počítači s Windows k průběžnému zjišťování serverů pro účely posouzení a migrace. Zařízení monitoruje změny prostředí, jako je například přidání virtuálních počítačů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení se má nasadit.


## <a name="planning-limits"></a>Omezení plánování
 
Pro plánování použijte omezení shrnutá v této tabulce.

**Plánování** | **Omezení**
--- | --- 
**Azure Migrate projekty** | Vyhodnoťte až 35 000 serverů v projektu.
**Zařízení Azure Migrate** | Zařízení může zjistit až 1000 serverů.<br/> Zařízení může být přidruženo pouze k jednomu Azure Migrate projektu.<br/> K jednomu Azure Migrate projektu může být přidružen libovolný počet zařízení. <br/><br/> 
**Skupina** | Do jedné skupiny můžete přidat až 35 000 serverů.
**Posouzení Azure Migrate** | V jednom posouzení můžete posoudit až 35 000 serverů.


## <a name="other-planning-considerations"></a>Další požadavky na plánování

- Chcete-li spustit zjišťování ze zařízení, je nutné vybrat jednotlivé fyzické servery. 

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Připravte si Azure a fyzické servery pro posouzení serveru. 

1. Ověřte [požadavky a omezení podpory fyzického serveru](migrate-support-matrix-physical.md).
2. Nastavte oprávnění pro účet Azure pro interakci s Azure Migrate.
3. Připravte fyzické servery.

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-discover-physical.md) .

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Jak vytvořit projekt Azure Migrate.
2. Přidejte do projektů Nástroj pro vyhodnocení Azure Migrate serveru.

[Další informace](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte posouzení pro fyzické servery.
1. Projděte si posouzení v části Příprava na plánování migrace.

[Přečtěte si další informace](tutorial-assess-physical.md) o vytváření a kontrole hodnocení.
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno pro škálování Azure Migrate posouzení pro fyzické servery.
> * Připravte si Azure a fyzické servery na posouzení.
> * Vytvořili Azure Migrate projekt a běžela posouzení.
> * Revidována posouzení při přípravě na migraci.

Nyní se [naučíte](concepts-assessment-calculation.md) počítat vyhodnocení a postup [Úpravy hodnocení](how-to-modify-assessment.md).
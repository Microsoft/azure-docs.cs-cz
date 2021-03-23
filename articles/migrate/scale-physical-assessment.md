---
title: Vyhodnoťte velký počet fyzických serverů pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit velký počet fyzických serverů pro migraci do Azure pomocí služby Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780306"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Vyhodnocení velkého počtu fyzických serverů pro migraci do Azure

Tento článek popisuje, jak vyhodnotit velké počty místních fyzických serverů pro migraci do Azure pomocí nástroje Azure Migrate Discovery and Assessment Tool.

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
- **Zařízení pro plánování**: Azure Migrate používá místní zařízení Azure Migrate nasazené na Windows serveru pro průběžné zjišťování serverů pro účely posouzení a migrace. Zařízení monitoruje změny prostředí, například přidávání serverů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení se má nasadit.


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

Příprava Azure a fyzických serverů pro nástroj pro zjišťování a hodnocení:  

1. Ověřte [požadavky a omezení podpory fyzického serveru](migrate-support-matrix-physical.md).
2. Nastavte oprávnění pro účet Azure pro interakci s Azure Migrate.
3. Připravte fyzické servery.

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-discover-physical.md) .

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Jak vytvořit projekt Azure Migrate.
2. Přidejte do projektů Nástroj pro zjišťování a vyhodnocení Azure Migrate.

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
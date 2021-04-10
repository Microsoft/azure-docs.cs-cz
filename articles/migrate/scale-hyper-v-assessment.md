---
title: Vyhodnoťte velký počet serverů v prostředí Hyper-V pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit velký počet serverů v prostředí Hyper-V pro migraci do Azure pomocí služby Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780289"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Vyhodnocení velkého počtu serverů v prostředí Hyper-V pro migraci do Azure

Tento článek popisuje, jak vyhodnotit velký počet místních serverů v prostředí Hyper-V pro migraci do Azure pomocí nástroje pro zjišťování a vyhodnocení Azure Migrate.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 


V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánujte hodnocení ve velkém měřítku.
> * Konfigurace oprávnění Azure a příprava technologie Hyper-V pro posouzení.
> * Vytvořte projekt Azure Migrate a vytvořte posouzení.
> * Projděte si hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet zkušební verzi, abyste před hodnocením škálování vyhodnotili několik serverů, postupujte podle našich [kurzů](./tutorial-discover-hyper-v.md) .

## <a name="plan-for-assessment"></a>Plánování posouzení

Při plánování hodnocení velkého počtu serverů v prostředí technologie Hyper-V existuje několik věcí, které je třeba zvážit:

- **Plánování Azure Migrate projektů**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud jsou datová centra v různých geografických oblastech nebo potřebujete ukládat metadata pro zjišťování, hodnocení nebo migraci v různých zeměpisných oblastech, možná budete potřebovat více projektů.
- **Zařízení pro plánování**: Azure Migrate používá místní zařízení Azure Migrate nasazené jako virtuální počítač s technologií Hyper-V pro průběžné zjišťování serverů pro účely posouzení a migrace. Zařízení monitoruje změny prostředí, například přidávání serverů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení se má nasadit.


## <a name="planning-limits"></a>Omezení plánování
 
Pro plánování použijte omezení shrnutá v této tabulce.

**Plánování** | **Omezení**
--- | --- 
**Azure Migrate projekty** | Vyhodnoťte až 35 000 serverů v projektu.
**Zařízení Azure Migrate** | Zařízení může zjistit až 5000 serverů.<br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.<br/> Zařízení může být přidruženo pouze k jednomu Azure Migrate projektu.<br/> K jednomu Azure Migrate projektu může být přidružen libovolný počet zařízení. <br/><br/> 
**Skupina** | Do jedné skupiny můžete přidat až 35 000 serverů.
**Posouzení Azure Migrate** | V jednom posouzení můžete posoudit až 35 000 serverů.



## <a name="other-planning-considerations"></a>Další požadavky na plánování

- Pokud chcete spustit zjišťování ze zařízení, musíte vybrat každého hostitele Hyper-V. 
- Pokud používáte víceklientské prostředí, nemůžete v současné době zjišťovat jenom servery, které patří do konkrétního tenanta. 

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Příprava Azure a technologie Hyper-V pro zjišťování a vyhodnocení nástroje: 

1. Ověřte [požadavky a omezení podpory technologie Hyper-V](migrate-support-matrix-hyper-v.md).
2. Nastavení oprávnění pro účet Azure pro interakci s Azure Migrate
3. Příprava hostitelů a serverů Hyper-V

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-discover-hyper-v.md) .

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Vytvořte Azure Migrate projekty.
2. Přidejte do projektů Nástroj pro zjišťování a vyhodnocení Azure Migrate.

[Další informace](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte vyhodnocení pro servery v prostředí Hyper-V.
1. Projděte si posouzení v části Příprava na plánování migrace.

[Přečtěte si další informace](tutorial-assess-hyper-v.md) o vytváření a kontrole hodnocení.
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno pro škálování Azure Migrate posouzení pro servery v prostředí technologie Hyper-V
> * Připravené Azure a technologie Hyper-V pro posouzení
> * Vytvořil se Azure Migrate projekt a běžela posouzení.
> * Revidována posouzení při přípravě na migraci.

Nyní se [dozvíte, jak](concepts-assessment-calculation.md) se počítají posouzení a jak [upravovat hodnocení](how-to-modify-assessment.md) .
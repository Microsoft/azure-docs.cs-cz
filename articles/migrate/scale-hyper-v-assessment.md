---
title: Vyhodnocení velký počet virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak posoudit velký počet virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811541"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Vyhodnocení velký počet virtuálních počítačů Hyper-V pro migraci do Azure

Tento článek popisuje, o posouzení místních virtuálních počítačů Hyper-V pro migraci do Azure pomocí služby Azure Migrate Server Assessment tool velké počty (> 1000).

[Azure Migrate](migrate-services-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 


V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánovat vyhodnocování ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a příprava na posouzení Hyper-V.
> * Vytvoření projektu Azure Migrate a vytvořit posouzení.
> * Při plánování migrace, projděte si hodnocení.


> [!NOTE]
> Pokud chcete vyzkoušet testování konceptu k vyhodnocení několika virtuálních počítačů před posouzení ve velkém měřítku, postupujte podle našich [série kurzů](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plán pro hodnocení

Při plánování pro posouzení velký počet virtuálních počítačů Hyper-V, existuje několik věcí zvážit:

- **Plánování projektů Azure Migrate**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud vaše datová centra jsou v různých zeměpisných oblastech nebo potřebujete ukládat zjišťování, vyhodnocení nebo metadata související s migrací v jiném datovém typu geography, můžete potřebovat více projektů.
- **Plán zařízení**: Azure Migrate ho použije místní Azure Migrate zařízení, nasazená jako virtuální počítač Hyper-V, se průběžně zjistit virtuální počítače pro vyhodnocení a migraci. Zařízení sleduje změny v prostředí jako je například přidávání virtuálních počítačů, disků nebo síťových adaptérů. Rovněž odesílá data výkon a metadata o nich do Azure. Budete muset zjistit, kolik zařízení tak, aby nasazení.


## <a name="planning-limits"></a>Plánování omezení
 
Použijte výše uvedené v této tabulce pro plánování.

**Plánování** | **Omezení**
--- | --- 
**Projekty Azure Migrate** | Posouzení až 10 000 virtuálních počítačů v projektu.
**Zařízení Azure Migrate** | Zařízení možné vyhledat až 5000 virtuálních počítačů.<br/> Zařízení může připojit k až 300 hostitele Hyper-V.<br/> Zařízení lze přidružit pouze pomocí jednoho projektu Azure Migrate.<br/><br/> 
**Vyhodnocení Azure Migrate** | Můžete posoudit až 10 000 virtuálních počítačů v rámci jednoho interního hodnocení.



## <a name="other-planning-considerations"></a>Další informace o plánování

- Chcete-li spustit vyhledávání od zařízení, budete muset vyberte každého hostitele Hyper-V. 
- Pokud používáte prostředí s více tenanty, nelze momentálně zjistit jenom virtuální počítače, které patří ke konkrétnímu tenantu. 

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Příprava Azure a Hyper-V na serveru posouzení. 

1. Ověřte [požadavky na podporu technologie Hyper-V a omezení](migrate-support-matrix-hyper-v.md).
2. Nastavení oprávnění k vašemu účtu Azure k interakci s Azure Migrate
3. Příprava hostitelů Hyper-V a virtuálních počítačů

Postupujte podle pokynů v [v tomto kurzu](tutorial-prepare-hyper-v.md) k nakonfigurování těchto nastavení.

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavků na plánování postupujte takto:

1. Vytvoření Azure Migrate projektů.
2. K projektům přidáte Azure Migrate Server Assessment tool.

[Víc se uč](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte posouzení virtuálních počítačů Hyper-V.
1. Kontrola posouzení v rámci přípravy pro plánování migrace.

[Další informace](tutorial-assess-hyper-v.md) o vytváření a revizí posouzení.
    

## <a name="next-steps"></a>Další postup

V tomto článku:
 
> [!div class="checklist"] 
> * Plánované škálování Azure Migrate posouzení virtuálních počítačů Hyper-V
> * Připravit Azure a Hyper-V pro posouzení
> * Vytvoří projekt Azure Migrate a spuštění posouzení
> * Zkontrolovat, jestli posouzení v rámci přípravy na migraci.

Nyní [zjistěte, jak](concepts-assessment-calculation.md) se posouzení počítá a jak [Upravit hodnocení](how-to-modify-assessment.md)

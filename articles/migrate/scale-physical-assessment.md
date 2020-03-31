---
title: Vyhodnoťte velký počet fyzických serverů pro migraci do Azure pomocí migrace Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak vyhodnotit velký počet fyzických serverů pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294367"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Posouzení velkého počtu fyzických serverů pro migraci do Azure

Tento článek popisuje, jak posoudit velký počet místních fyzických serverů pro migraci do Azure pomocí nástroje Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou objevovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum obsahuje nástroje pro migraci Azure a nabídky nezávislého dodavatele softwaru (ISV) od jiných výrobců. 


V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Plán hodnocení ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a připravte fyzické servery k vyhodnocení.
> * Vytvořte projekt Migrace Azure a vytvořte hodnocení.
> * Zkontrolujte hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet proof-of-concept posoudit pár serverů před posouzením ve velkém měřítku, postupujte podle našeho [výukového programu série](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Plán hodnocení

Při plánování pro posouzení velkého počtu fyzických serverů je třeba přemýšlet o několika věcech:

- **Plánování projektů migrace Azure:** Zjistěte, jak nasadit projekty Migrace Azure. Pokud se například vaše datová centra nacházejí v různých zeměpisných oblastech nebo potřebujete ukládat metadata zjišťování, hodnocení nebo migrace v jiné zeměpisné oblasti, budete pravděpodobně potřebovat více projektů.
- **Zařízení pro plánování:** Azure Migrate používá místní zařízení Pro migraci Azure nasazené v počítači s Windows k průběžnému zjišťování serverů pro hodnocení a migraci. Zařízení monitoruje změny prostředí, jako je přidávání virtuálních počítačů, disků nebo síťových adaptérů. Také odesílá metadata a údaje o výkonu o nich do Azure. Musíte zjistit, kolik zařízení nasadit.


## <a name="planning-limits"></a>Limity plánování
 
Pro plánování použijte limity shrnuté v této tabulce.

**Plánování** | **Limity**
--- | --- 
**Projekty migrace Azure** | Vyhodnoťte až 35 000 serverů v projektu.
**Zařízení Azure Migrate** | Zařízení může objevit až 250 serverů.<br/> Zařízení lze přidružit pouze k jednomu projektu migrace Azure.<br/> K jednomu projektu migrace Azure lze přidružit libovolný počet zařízení. <br/><br/> 
**Skupiny** | V jedné skupině můžete přidat až 35 000 serverů.
**Vyhodnocení migrace Azure** | V jednom hodnocení můžete posoudit až 35 000 serverů.


## <a name="other-planning-considerations"></a>Další aspekty plánování

- Chcete-li spustit zjišťování z zařízení, musíte vybrat každý fyzický server. 

## <a name="prepare-for-assessment"></a>Připravte se na posouzení

Připravte Azure a fyzické servery pro vyhodnocení serveru. 

1. Ověřte [požadavky a omezení fyzické podpory serveru](migrate-support-matrix-physical.md).
2. Nastavte oprávnění pro svůj účet Azure pro interakci s Azure Migrate.
3. Připravte fyzické servery.

Podle pokynů v [tomto kurzu](tutorial-prepare-physical.md) nakonfigurujte tato nastavení.

## <a name="create-a-project"></a>Vytvoření projektu

V souladu s vašimi požadavky na plánování postupujte takto:

1. Jak vytvořit projekt Azure Migrate.
2. Přidejte do projektů nástroj Azure Migrate Server Assessment.

[Další informace](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a revize hodnocení

1. Vytvořte hodnocení pro fyzické servery.
1. Projděte si hodnocení v rámci přípravy na plánování migrace.

[Přečtěte si další informace](tutorial-assess-physical.md) o vytváření a kontrole hodnocení.
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno škálování hodnocení Migrace Azure pro fyzické servery.
> * Připravené Azure a fyzické servery pro posouzení.
> * Vytvořil projekt Migrace Azure a spustil hodnocení.
> * Zhodnocená hodnocení v rámci přípravy na migraci.

Nyní [se dozvíte, jak](concepts-assessment-calculation.md) se hodnocení počítají a jak [upravit hodnocení](how-to-modify-assessment.md).

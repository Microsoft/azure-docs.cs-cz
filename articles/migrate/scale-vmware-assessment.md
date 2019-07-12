---
title: Vyhodnocení velký počet virtuálních počítačů VMware pro migraci do Azure pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak posoudit velký počet virtuálních počítačů VMware pro migraci do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811333"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Vyhodnocení velký počet virtuálních počítačů VMware pro migraci do Azure


Tento článek popisuje, jak k vyhodnocení velký (1 000 35 000) z místních virtuálních počítačů VMware pro migraci do Azure pomocí nástroje Azure Migrate Server Assessment

[Azure Migrate](migrate-services-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánovat vyhodnocování ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a příprava VMware na posouzení.
> * Vytvoření projektu Azure Migrate a vytvořit posouzení.
> * Při plánování migrace, projděte si hodnocení.


> [!NOTE]
> Pokud chcete vyzkoušet testování konceptu k vyhodnocení několika virtuálních počítačů před posouzení ve velkém měřítku, postupujte podle našich [série kurzů](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plán pro hodnocení

Při plánování pro posouzení velký počet virtuálních počítačů VMware, existuje několik věcí zvážit:

- **Plánování projektů Azure Migrate**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud vaše datová centra jsou v různých zeměpisných oblastech nebo potřebujete ukládat zjišťování, vyhodnocení nebo metadata související s migrací v jiném datovém typu geography, můžete potřebovat více projektů. 
- **Plán zařízení**: Azure Migrate ho použije místní Azure Migrate zařízení, nasazená jako virtuální počítač VMware, se průběžně zjistit virtuální počítače. Zařízení sleduje změny v prostředí jako je například přidávání virtuálních počítačů, disků nebo síťových adaptérů. Rovněž odesílá data výkon a metadata o nich do Azure. Budete muset zjistit, kolik zařízení je třeba nasadit.
- **Plánování účty pro zjišťování**: Zařízení Azure Migrate používá účet s přístupem k serveru vCenter pro zjištění virtuálních počítačů pro vyhodnocení a migraci. Pokud jste zjišťování víc než 10 000 virtuálních počítačů, nastavte víc účtů.


## <a name="planning-limits"></a>Plánování omezení
 
Použijte výše uvedené v této tabulce pro plánování.

**Plánování** | **Omezení**
--- | --- 
**Projekty Azure Migrate** | Posouzení až 35 000 virtuálních počítačů v projektu.
**Zařízení Azure Migrate** | Zařízení lze připojit pouze k jedné systému vCenter Server.<br/><br/> Zařízení lze přidružit pouze pomocí jednoho projektu Azure Migrate.<br/> Zařízení možné vyhledat až 10 000 virtuálních počítačů na vCenter serveru.
**Vyhodnocení Azure Migrate** | Můžete posoudit až 35 000 virtuálních počítačů v rámci jednoho interního hodnocení.

Pomocí těchto omezení na paměti tady jsou některá ukázková nasazení:


**Server vCenter** | **Virtuální počítače na serveru** | **Doporučení** | **Akce**
---|---|---
Jeden | < 10,000 | Jeden projekt Azure Migrate.<br/> Jeden zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavení zařízení, připojení k vCenter serveru pomocí účtu.
Jeden | > 10,000 | Jeden projekt Azure Migrate.<br/> Více zařízení.<br/> Více účtů vCenter. | Nastavení zařízení pro každých 10 000 virtuálních počítačů.<br/><br/> Nastavení účtů vCenter a dělení inventář, abyste omezili přístup k účtu na méně než 10 000 virtuálních počítačů.<br/> Každé zařízení připojte k vCenter serveru pomocí účtu.<br/> Analýzu závislostí mezi počítači, které se zjišťují pomocí různých zařízení.
Několik | < 10,000 |  Jeden projekt Azure Migrate.<br/> Více zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavení zařízení, připojení k vCenter serveru pomocí účtu.<br/> Analýzu závislostí mezi počítači, které se zjišťují pomocí různých zařízení.
Několik | > 10,000 | Jeden projekt Azure Migrate.<br/> Více zařízení.<br/> Více účtů vCenter. | Pokud zjišťování serveru vCenter < 10 000 virtuálních počítačů, nastavit zařízení pro každou vCenter Server.<br/><br/> Pokud zjišťování serveru vCenter > 10 000 virtuálních počítačů, nastavit zařízení pro každých 10 000 virtuálních počítačů.<br/> Nastavení účtů vCenter a dělení inventář, abyste omezili přístup k účtu na méně než 10 000 virtuálních počítačů.<br/> Každé zařízení připojte k vCenter serveru pomocí účtu.<br/> Analýzu závislostí mezi počítači, které se zjišťují pomocí různých zařízení.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Plánování zjišťování v prostředí s více tenanty

Pokud máte v plánu pro prostředí s více tenanty, můžete omezit rozsah zjišťování serveru vCenter.

- Můžete nastavit obor zjišťování zařízení k serveru vCenter Server datacenter, clusteru nebo složce clustery, hostitele nebo složky hostitele nebo jednotlivé virtuální počítače.
- Pokud vaše prostředí se sdílí mezi tenanty a nechcete zjišťovat každý klient samostatně, můžete omezit rozsah přístup k serveru vCenter účtu, který zařízení používá pro zjišťování. 
    - Pokud klienti sdílejí hostitele, vytvořte pro virtuální počítače, které patří ke konkrétnímu tenantu. přihlašovací údaje s přístupem jen pro čtení. 
    - Pomocí těchto přihlašovacích údajů pro Azure Migrate zjišťování zařízení.
    - Vyhodnocení Azure Migrate nelze zjistit virtuální počítače, pokud má účet vCenter přístupu na úrovni složky serveru vCenter virtuálního počítače. Jsou podporovány složky hostitelů a clusterů. 

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Příprava Azure i z VMware na serveru posouzení. 

1. Ověřte [VMware podporují požadavky a omezení](migrate-support-matrix-vmware.md).
2. Nastavení oprávnění k vašemu účtu Azure k interakci s Azure Migrate.
3. Příprava VMware na posouzení.


Postupujte podle pokynů v [v tomto kurzu](tutorial-prepare-vmware.md) k nakonfigurování těchto nastavení.


## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavků na plánování postupujte takto:

1. Vytvoření Azure Migrate projektů.
2. K projektům přidáte Azure Migrate Server Assessment tool.

[Víc se uč](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte posouzení pro virtuální počítače VMware.
1. Kontrola posouzení v rámci přípravy pro plánování migrace.


Postupujte podle pokynů v [v tomto kurzu](tutorial-assess-vmware.md) k nakonfigurování těchto nastavení.
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánované škálování Azure Migrate posouzení pro virtuální počítače VMware
> * Azure a VMware pro posouzení
> * Vytvoří projekt Azure Migrate a spuštění posouzení
> * Zkontrolovat, jestli posouzení v rámci přípravy na migraci.

Nyní [zjistěte, jak](concepts-assessment-calculation.md) se posouzení počítá a jak [upravit posouzení](how-to-modify-assessment.md).

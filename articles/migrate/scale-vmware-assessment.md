---
title: Vyhodnoťte velký počet serverů v prostředí VMware pro migraci do Azure pomocí Azure Migrate
description: Popisuje, jak vyhodnotit velký počet serverů v prostředí VMware pro migraci do Azure pomocí služby Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778249"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Vyhodnocení velkého počtu serverů v prostředí VMware pro migraci do Azure


Tento článek popisuje, jak vyhodnotit Velká čísla (1000 – 35000) místních serverů v prostředí VMware pro migraci do Azure pomocí nástroje pro zjišťování a vyhodnocení Azure Migrate.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Naplánujte hodnocení ve velkém měřítku.
> * Nakonfigurujte oprávnění Azure a připravte VMware na posouzení.
> * Vytvořte projekt Azure Migrate a vytvořte posouzení.
> * Projděte si hodnocení při plánování migrace.


> [!NOTE]
> Pokud si chcete vyzkoušet zkušební verzi, abyste před hodnocením škálování vyhodnotili několik serverů, postupujte podle našich [kurzů](./tutorial-discover-vmware.md) .

## <a name="plan-for-assessment"></a>Plánování posouzení

Při plánování hodnocení velkého počtu serverů v prostředí VMware je potřeba zvážit několik věcí:

- **Plánování Azure Migrate projektů**: Zjistěte, jak nasadit Azure Migrate projekty. Například pokud jsou datová centra v různých geografických oblastech nebo potřebujete ukládat metadata pro zjišťování, hodnocení nebo migraci v různých zeměpisných oblastech, možná budete potřebovat více projektů. 
- **Zařízení pro plánování**: Azure Migrate pro průběžné zjišťování serverů používá místní zařízení Azure Migrate nasazené jako virtuální počítač VMware. Zařízení monitoruje změny prostředí, například přidávání serverů, disků nebo síťových adaptérů. Do Azure taky odesílají metadata a data o výkonu. Potřebujete zjistit, kolik zařízení potřebujete nasadit.
- **Plánování účtů pro zjišťování**: zařízení Azure Migrate používá účet s přístupem k vCenter Server, aby bylo možné zjišťovat servery pro účely posouzení a migrace. Pokud zjišťujete víc než 10 000 serverů, nastavte víc účtů tak, jak je potřeba, a mezi servery zjištěnými ze všech dvou zařízení v projektu se překrývají. 

> [!NOTE]
> Pokud nastavujete více zařízení, zajistěte, aby se mezi servery na dostupných účtech vCenter nepřekrývaly. Scénář zjišťování s takovým překrýváním se nepodporuje. Pokud je Server zjištěn více než jedním zařízením, výsledkem je duplicita při zjišťování a v problémech při povolování replikace serveru pomocí Azure Portal v části migrace serveru.

## <a name="planning-limits"></a>Omezení plánování
 
Pro plánování použijte omezení shrnutá v této tabulce.

**Plánování** | **Omezení**
--- | --- 
**Azure Migrate projekty** | Vyhodnoťte až 35 000 serverů v projektu.
**Zařízení Azure Migrate** | Zařízení může na vCenter Server zjistit až 10 000 serverů.<br/> Zařízení se může připojit jenom k jednomu vCenter Server.<br/> Zařízení může být přidruženo pouze k jednomu Azure Migrate projektu.<br/>  K jednomu Azure Migrate projektu může být přidružen libovolný počet zařízení. <br/><br/> 
**Skupina** | Do jedné skupiny můžete přidat až 35 000 serverů.
**Posouzení Azure Migrate** | V jednom posouzení můžete posoudit až 35 000 serverů.

Tato omezení jsou uvedená v některých ukázkových nasazeních:


**Server vCenter** | **Servery na serveru** | **Doporučení** | **Akce**
---|---|---|---
Jednu | < 10 000 | Jeden Azure Migrate projekt.<br/> Jedno zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavte zařízení, připojte se k vCenter Server pomocí účtu.
Jednu | > 10 000 | Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Několik účtů vCenter. | Nastavte zařízení pro každý server 10 000.<br/><br/> Nastavte účty vCenter a vydělte inventář, abyste omezili přístup k účtu na méně než 10 000 serverů.<br/> Připojte každé zařízení k vCenter serveru pomocí účtu.<br/> Můžete analyzovat závislosti mezi servery, které jsou zjištěny s různými zařízeními. <br/> <br/> Zajistěte, aby se mezi servery na dostupných účtech vCenter překrývaly. Scénář zjišťování s takovým překrýváním se nepodporuje. Pokud je Server zjištěn více než jedním zařízením, výsledkem je duplicita při zjišťování a v problémech při povolování replikace serveru pomocí Azure Portal v části migrace serveru.
Několik | < 10 000 |  Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Jeden účet vCenter pro zjišťování. | Nastavte zařízení, připojte se k vCenter Server pomocí účtu.<br/> Můžete analyzovat závislosti mezi servery, které jsou zjištěny s různými zařízeními.
Několik | > 10 000 | Jeden Azure Migrate projekt.<br/> Více zařízení.<br/> Několik účtů vCenter. | Pokud vCenter Server Discovery < servery 10 000, nastavte zařízení pro jednotlivé vCenter Server.<br/><br/> Pokud vCenter Server Discovery > servery 10 000, nastavte zařízení pro všechny servery 10 000.<br/> Nastavte účty vCenter a vydělte inventář, abyste omezili přístup k účtu na méně než 10 000 serverů.<br/> Připojte každé zařízení k vCenter serveru pomocí účtu.<br/> Můžete analyzovat závislosti mezi servery, které jsou zjištěny s různými zařízeními. <br/><br/> Zajistěte, aby se mezi servery na dostupných účtech vCenter překrývaly. Scénář zjišťování s takovým překrýváním se nepodporuje. Pokud je Server zjištěn více než jedním zařízením, výsledkem je duplicita při zjišťování a v problémech při povolování replikace serveru pomocí Azure Portal v části migrace serveru.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Plánování zjišťování v prostředí s více klienty

Pokud plánujete pro více tenantů prostředí, můžete určit rozsah zjišťování vCenter Server.

- Rozsah zjišťování zařízení můžete nastavit na vCenter Server datacentra, clustery nebo složky clusterů, hostitelů nebo složku hostitelů nebo na jednotlivé servery.
- Pokud je vaše prostředí sdíleno mezi klienty a chcete zjistit jednotlivé klienty samostatně, můžete nastavit rozsah přístupu k účtu vCenter, který zařízení používá ke zjišťování. 
    - Můžete chtít obor podle složek virtuálních počítačů, pokud klienti sdílejí hostitele. Azure Migrate nemůže vyhledat servery, pokud má účet vCenter udělen přístup na úrovni složky virtuálního počítače vCenter. Pokud chcete určit obor zjišťování pomocí složek virtuálních počítačů, můžete to udělat tak, že účet vCenter bude mít přiřazený přístup jen pro čtení na úrovni serveru. [Další informace](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Příprava na posouzení

Příprava Azure a VMware pro zjišťování a nástroj pro vyhodnocení:

1. Ověřte [požadavky a omezení podpory VMware](migrate-support-matrix-vmware.md).
2. Nastavte oprávnění pro účet Azure pro interakci s Azure Migrate.
3. Připravte VMware na posouzení.

Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-discover-vmware.md) .


## <a name="create-a-project"></a>Vytvoření projektu

V souladu s požadavky na plánování udělejte toto:

1. Vytvořte Azure Migrate projekty.
2. Přidejte do projektů Nástroj pro zjišťování a vyhodnocení Azure Migrate.

[Další informace](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Vytvoření a kontrola posouzení

1. Vytvořte vyhodnocení pro servery v prostředí VMware.
1. Projděte si posouzení v části Příprava na plánování migrace.


Pokud chcete nakonfigurovat tato nastavení, postupujte podle pokynů v [tomto kurzu](./tutorial-assess-vmware-azure-vm.md) .
    

## <a name="next-steps"></a>Další kroky

V tomto článku:
 
> [!div class="checklist"] 
> * Plánováno pro škálování Azure Migrate posouzení pro servery v prostředí VMware
> * Připravené Azure a VMware pro posouzení
> * Vytvořil se Azure Migrate projekt a běžela posouzení.
> * Revidována posouzení při přípravě na migraci.

Nyní se [naučíte](concepts-assessment-calculation.md) počítat vyhodnocení a postup [Úpravy hodnocení](how-to-modify-assessment.md).